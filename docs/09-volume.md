# 09 바인드 마운트 반영 + Docker 볼륨 영속성 증거

## 명령어 요약

| 명령어 | 설명 |
| --- | --- |
| `docker run ... -v 호스트경로:컨테이너경로` | 바인드 마운트로 호스트 디렉토리를 컨테이너에 연결해 실행 |
| `docker volume create 이름` | Docker 볼륨 생성 |
| `docker run ... -v 볼륨이름:컨테이너경로` | 볼륨을 컨테이너에 연결해 실행 |
| `docker exec -it 컨테이너 명령` | 컨테이너 안에서 데이터 확인/작성 |
| `docker rm -f 컨테이너` | 컨테이너 강제 삭제 (볼륨 데이터가 남아있는지 검증하기 위한 삭제) |
| `docker volume ls` | 볼륨 목록에서 생성 여부 확인 |

> 미션 요구사항: 바인드 마운트는 "호스트 변경 전/후 비교", 볼륨은 "컨테이너 삭제 전/후 비교"로 증명.

### 사전 작업 — 커스텀 이미지 재빌드
08번에서 `codyssey-e1-web` 이미지를 삭제했기 때문에, 바인드 마운트 실습을 위해 [06번](06-image-build.md)과 동일한 명령으로 이미지를 다시 빌드
```bash
$ cd app
$ docker build -t codyssey-e1-web .
...
#7 [3/3] COPY site/ .
#7 DONE 0.2s

#8 exporting to image
#8 naming to docker.io/library/codyssey-e1-web:latest 0.0s done
#8 unpacking to docker.io/library/codyssey-e1-web:latest 0.2s done
#8 DONE 1.0s
```
> 이미지를 지워도 Dockerfile만 있으면 언제든 동일한 이미지를 다시 만들 수 있다는 점이 확인됨. 이것이 "재현 가능한 환경"의 실제 의미

## Part 1. 바인드 마운트 — 호스트 변경이 즉시 반영되는지 확인

### 9-1. 바인드 마운트로 컨테이너 실행
`docker run -d -p 호스트포트:80 -v 호스트경로:/usr/share/nginx/html --name 이름 이미지`: 호스트의 `app/site` 디렉토리를 컨테이너의 웹 루트에 그대로 연결
- `COPY`(05·06번)와 차이점: `COPY`는 `docker build` 시점의 스냅샷을 이미지 안에 고정하지만, 바인드 마운트는 컨테이너가 실행되는 동안 호스트 경로를 계속 실시간으로 들여다봄
```bash
$ docker run -d -p 8080:80 -v "C:/Users/Yuhyun Lim/codyssey-e1/app/site:/usr/share/nginx/html" --name codyssey-e1-bind codyssey-e1-web
33e1df4466df6c94361aaacc97662f8052b34b258b670b743caa797b69dbd04f

$ docker ps --filter name=codyssey-e1-bind
CONTAINER ID   IMAGE             COMMAND                   CREATED         STATUS         PORTS                                     NAMES
33e1df4466df   codyssey-e1-web   "/docker-entrypoint.…"   8 seconds ago   Up 7 seconds   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   codyssey-e1-bind
```
> 호스트 경로는 반드시 절대 경로로 지정해야 함. Windows에서는 `C:/Users/...` 형식을 사용

### 9-2. 변경 전 상태 확인
`curl`로 현재 페이지 내용을 확인 (전체 HTML 중 확인에 필요한 줄만 발췌)
```bash
$ curl -s http://localhost:8080 | grep -n "<h1>\|<footer>"
29:  <h1>내 컴퓨터에 개발자용 '작업실' 꾸미기</h1>
32:  <footer>codyssey-e1 · hauteville1862</footer>
```

### 9-3. 호스트 파일 수정 후 즉시 반영 확인
호스트의 `app/site/index.html`에 문단 한 개를 추가하고, **컨테이너를 재시작하거나 이미지를 다시 빌드하지 않은 채로** 다시 접속
```html
<!-- app/site/index.html 에 추가한 내용 -->
<p><strong>바인드 마운트 반영 테스트</strong>: 이 문단은 컨테이너를 재시작하거나 이미지를 다시 빌드하지 않고, 호스트의 <code>app/site/index.html</code>을 수정한 것만으로 추가되었습니다.</p>
```
```bash
$ docker ps --filter name=codyssey-e1-bind --format "STATUS: {{.Status}}"
STATUS: Up 21 seconds

$ curl -s http://localhost:8080 | grep -n "<h1>\|바인드 마운트 반영 테스트\|<footer>"
29:  <h1>내 컴퓨터에 개발자용 '작업실' 꾸미기</h1>
32:  <p><strong>바인드 마운트 반영 테스트</strong>: 이 문단은 컨테이너를 재시작하거나 이미지를 다시 빌드하지 않고, 호스트의 <code>app/site/index.html</code>을 수정한 것만으로 추가되었습니다.</p>
33:  <footer>codyssey-e1 · hauteville1862</footer>
```
확인 포인트
- 32번 줄에 수정한 내용이 그대로 나타남 → 호스트 변경이 컨테이너에 즉시 반영됨
- `STATUS: Up 21 seconds` → 컨테이너가 재시작되지 않고 계속 살아있는 상태였음을 증명 (재시작했다면 `Up` 시간이 초기화됨)

### 9-4. 대비 검증 — 마운트 없이 실행하면 반영되지 않음
같은 이미지를 **바인드 마운트 없이** 다른 포트로 띄워서, 이미지 안의 내용은 빌드 시점 그대로임을 확인
```bash
$ docker run -d -p 8081:80 --name codyssey-e1-nomount codyssey-e1-web

$ curl -s http://localhost:8081 | grep -c "바인드 마운트 반영 테스트"
0
```
> 결과가 `0`건 = 마운트 없는 컨테이너에는 수정 내용이 없음. 같은 이미지인데 8080은 바뀐 내용이 보이고 8081은 안 보이는 것이, 변경이 **이미지가 아니라 마운트를 통해 호스트에서 오고 있다**는 직접적인 증거

## Part 2. Docker 볼륨 — 컨테이너를 삭제해도 데이터가 유지되는지 확인

### 9-5. 볼륨 생성 및 컨테이너 연결
`docker volume create 이름`: Docker가 관리하는 저장 공간을 만드는 명령어
```bash
$ docker volume create codyssey-e1-data
codyssey-e1-data
```
`docker volume ls`: 볼륨이 실제로 만들어졌는지 목록에서 확인
```bash
$ docker volume ls
DRIVER    VOLUME NAME
local     codyssey-e1-data
local     my-volume
local     my_volume
```
`docker run -v 볼륨이름:경로`: 만든 볼륨을 컨테이너의 `/data` 경로에 연결해 실행
```bash
$ docker run -d --name vol-test -v codyssey-e1-data:/data ubuntu sleep infinity
81b5027e9e9a6ca4a04f5c8fa82fa8abda54d7161eb9117f8aa1f439b279cf54
```
> `sleep infinity`는 컨테이너를 계속 살려두기 위한 명령. ubuntu 이미지는 기본적으로 할 일이 끝나면 바로 종료되므로(04번 참고), 안에 들어가서 작업하려면 프로세스를 붙잡아 둘 필요가 있음

### 9-6. 데이터 작성 후 컨테이너 삭제
`docker exec`로 볼륨이 연결된 `/data`에 파일을 작성
```bash
$ docker exec vol-test bash -lc "echo 'codyssey-e1 volume persistence test' > /data/hello.txt && cat /data/hello.txt"
codyssey-e1 volume persistence test
```
`docker rm -f 컨테이너`: 실행 중인 컨테이너를 강제로 삭제
```bash
$ docker rm -f vol-test
vol-test

$ docker ps -a --filter name=vol-test
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
> 목록이 비어 있음 = 컨테이너가 완전히 삭제됨

컨테이너는 사라졌지만 볼륨은 그대로 남아있는지 확인
```bash
$ docker volume ls --filter name=codyssey-e1-data
DRIVER    VOLUME NAME
local     codyssey-e1-data
```

### 9-7. 새 컨테이너로 데이터 유지 확인 (영속성 증명)
같은 볼륨을 연결한 **새 컨테이너**를 띄워서, 삭제된 이전 컨테이너가 남긴 데이터가 그대로 읽히는지 확인
```bash
$ docker run -d --name vol-test2 -v codyssey-e1-data:/data ubuntu sleep infinity
71d25a7bb3615a80e3e0bff89d67451e454fa5b83074e5ecf25a1a5fd0970423

$ docker exec vol-test2 bash -lc "ls -l /data && cat /data/hello.txt"
total 4
-rw-r--r-- 1 root root 36 Aug 19 10:24 hello.txt
codyssey-e1 volume persistence test
```
확인 포인트
- `hello.txt`의 크기(36바이트)와 내용이 삭제 전과 동일
- 이 파일을 만든 컨테이너(`vol-test`)는 이미 삭제됐는데도 새 컨테이너(`vol-test2`)에서 그대로 읽힘

> 컨테이너는 삭제됐지만 볼륨은 컨테이너와 독립된 별개의 저장 객체라 데이터가 그대로 남아있음 — 이게 "데이터 영속성". [README - 심층 인터뷰](../README.md#v-심층-인터뷰)의 "컨테이너 삭제 후 데이터가 사라진 경험 방지 대안" 질문과도 바로 연결되는 내용

## 바인드 마운트 vs 볼륨 정리

| | 바인드 마운트 | Docker 볼륨 |
| --- | --- | --- |
| 저장 위치 | 호스트의 특정 경로 (내가 지정) | Docker가 관리하는 영역 (경로를 신경 쓸 필요 없음) |
| 지정 방법 | `-v /절대/경로:/컨테이너/경로` | `-v 볼륨이름:/컨테이너/경로` |
| 주 용도 | 개발 중 소스 수정을 즉시 반영 | DB 데이터 등 오래 보관해야 하는 데이터 |
| 확인한 것 | 호스트 파일 수정 → 재빌드 없이 반영 (9-3) | 컨테이너 삭제 → 데이터 유지 (9-7) |

- 둘 다 `-v` 옵션을 쓰지만, 앞에 오는 값이 **경로면 바인드 마운트, 이름이면 볼륨**이다.
- 공통점은 "데이터를 컨테이너 바깥에 두는 것". 컨테이너 안에만 저장한 데이터는 컨테이너를 지우는 순간 함께 사라진다.
