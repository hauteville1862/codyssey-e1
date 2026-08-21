# 11 심층 인터뷰


## 포트/볼륨 설정을 어떤 방식으로 재현 가능하게 정리했는지 설명할 수 있는가?

이 저장소를 클론한 사람이 아래 명령만으로 동일한 결과를 확인할 수 있도록 정리한 절차. 사용하는 포트와 볼륨 이름을 먼저 고정해 두었다.

| 리소스 | 값 | 용도 |
| --- | --- | --- |
| 호스트 포트 | `8080` | 커스텀 이미지 접속 확인 / 바인드 마운트 검증 |
| 호스트 포트 | `8081` | 두 번째 포트 매핑 / 마운트 없는 대비 검증 |
| 컨테이너 포트 | `80` | nginx 기본 포트 (`app/Dockerfile`의 `EXPOSE 80`) |
| 이미지 이름 | `codyssey-e1-web` | 빌드 결과물 |
| 볼륨 이름 | `codyssey-e1-data` | 영속성 검증용 |

### 1) 이미지 빌드 및 포트 매핑 확인

```bash
$ git clone https://github.com/hauteville1862/codyssey-e1.git
$ cd codyssey-e1/app
$ docker build -t codyssey-e1-web .
$ docker run -d -p 8080:80 --name codyssey-e1-web-container codyssey-e1-web
$ curl http://localhost:8080          # 또는 브라우저에서 http://localhost:8080
```

### 2) 바인드 마운트 반영 확인

```bash
# 호스트 경로는 반드시 절대 경로. 아래 <저장소경로>를 자신의 경로로 바꿔서 실행
$ docker run -d -p 8080:80 \
    -v "<저장소경로>/app/site:/usr/share/nginx/html" \
    --name codyssey-e1-bind codyssey-e1-web

$ curl -s http://localhost:8080 | grep "<h1>"     # 변경 전
# app/site/index.html 을 수정한 뒤 (컨테이너 재시작 없이)
$ curl -s http://localhost:8080 | grep "<h1>"     # 변경 후 - 수정 내용이 바로 보임
```

### 3) 볼륨 영속성 확인

```bash
$ docker volume create codyssey-e1-data
$ docker run -d --name vol-test -v codyssey-e1-data:/data ubuntu sleep infinity
$ docker exec vol-test bash -lc "echo 'codyssey-e1 volume persistence test' > /data/hello.txt"
$ docker rm -f vol-test
$ docker run -d --name vol-test2 -v codyssey-e1-data:/data ubuntu sleep infinity
$ docker exec vol-test2 bash -lc "cat /data/hello.txt"
codyssey-e1 volume persistence test    # 컨테이너를 지웠는데도 남아있음
```

### 4) 정리

```bash
$ docker rm -f codyssey-e1-web-container codyssey-e1-bind codyssey-e1-nomount vol-test2
$ docker rmi codyssey-e1-web
$ docker volume rm codyssey-e1-data
```


## 이미지와 컨테이너의 차이를 빌드/실행/변경 관점에서?

이미지는 `docker build`가 Dockerfile을 읽어 만들어내는 **읽기 전용 템플릿**이고, 컨테이너는 그 이미지를 `docker run`으로 실행해 쓰기 가능한 레이어를 하나 얹은 **살아있는 인스턴스**다.

| 관점 | 이미지 | 컨테이너 |
| --- | --- | --- |
| 빌드 | <ul><li>`docker build -t codyssey-e1-web .`처럼 Dockerfile을 순서대로 읽어 레이어를 쌓아 **생성**됨</li></ul> | <ul><li>빌드되지 않음</li><li>이미 만들어진 이미지 위에서 생성됨</li></ul> |
| 실행 | <ul><li>실행 상태가 없음</li><li>`docker images` 목록에만 존재</li></ul> | <ul><li>`docker run`으로 이미지 위에 쓰기 가능한 레이어를 얹어 프로세스로 살아 있음</li><li>같은 이미지 하나로 여러 컨테이너를 동시에 띄울 수 있음 </li></ul> |
| 변경 | <ul><li>수정 불가</li><li>바꾸려면 Dockerfile을 고쳐 **재빌드**해야 함</li></ul> | <ul><li>내부에서 파일을 바꿀 수 있음</li><li>그 변경은 컨테이너의 쓰기 레이어에만 남고 `docker rm`과 함께 사라짐</li></ul> |

## 절대 경로/상대 경로는 어떤 상황에서 선택하는지?

- **절대 경로**: `/`나 `C:/`로 시작하는, 맨 위 루트부터 적은 "전체 주소". 어디서 실행해도 항상 같은 파일을 가리킨다.
- **상대 경로**: `note.txt`처럼 `/`로 시작하지 않는, **지금 있는 폴더 기준**의 "짧은 길". 폴더를 옮기면 결과가 달라질 수 있다.

**선택 기준**: 실행 위치가 항상 같다고 확신하면 상대 경로(예: Dockerfile의 `COPY site/ .`), 확신할 수 없으면 절대 경로(예: `docker run -v`의 호스트 경로)를 쓴다.

## 호스트 포트가 이미 사용 중이라 포트 매핑이 실패한다면, 어떤 순서로 원인을 진단할지?

1. **에러 메시지로 원인을 확정한다.** `Bind for 0.0.0.0:8080 failed: port is already allocated`처럼 Docker가 충돌을 명시적으로 알려주므로 추측 전에 문구부터 읽는다.
2. **Docker 안에서 점유 중인지 확인한다.** `docker ps -a --filter publish=8080`으로 확인한다. `Up` 상태 컨테이너가 나오면 그것이 범인이고, `Exited` 상태만 나온다면 포트는 이미 해제된 상태이므로 다른 원인(이름 충돌 등)을 의심한다.
3. **Docker 밖(호스트 프로세스)이 점유 중인지 확인한다.** 2단계에서 아무것도 안 나오면 Docker와 무관한 프로그램이 쓰는 것이다. Windows는 `netstat -ano | findstr :8080`으로 PID를 찾고, macOS/Linux는 `lsof -i :8080`으로 확인한다.
4. **조치를 선택한다.** 내가 만든 불필요한 컨테이너면 `docker stop`/`docker rm`으로 정리하고, 꺼도 되는 호스트 프로그램이면 종료한다. 끌 수 없는 서비스라면 컨테이너 포트는 그대로 두고 호스트 포트만 바꿔서 실행한다(예: `-p 8090:80`).
5. **재발을 방지한다.** 실행 전 `docker ps`로 포트 사용 현황을 먼저 보는 습관을 들이고, 프로젝트가 쓰는 포트를 문서에 고정해 둔다. 이 저장소는 [재현 방법](#포트볼륨-설정을-어떤-방식으로-재현-가능하게-정리했는지-설명할-수-있는가)에 8080/8081을 명시해 두었다.

→ 자세한 근거: [00 개념 노트 - 호스트 포트 충돌 진단 순서](00-concepts.md#호스트-포트-충돌-진단-순서)

## 컨테이너 삭제 후 데이터가 사라진 경험이 있다면, 이를 방지하기 위한 대안은?

원인은 데이터를 **컨테이너와 수명이 같은 쓰기 레이어**에 둔 것이다. 컨테이너는 읽기 전용 이미지 레이어 위에 쓰기 가능한 레이어를 하나 얹어 동작하는데, 이 레이어는 `docker rm`과 동시에 사라진다. 즉 데이터가 "사라진" 게 아니라 애초에 컨테이너와 한 몸인 곳에 저장한 것이 문제다.

대안은 데이터를 **컨테이너 바깥**에 두는 것이며, 성격에 따라 두 가지로 나눠 쓴다.

- **Docker 볼륨(영속 데이터용, 권장)**: 컨테이너 바깥의 독립된 저장 객체에 데이터를 두면 컨테이너를 지워도 남는다. 09번에서 직접 증명했다 — 볼륨(`codyssey-e1-data`)에 데이터를 쓰고, `docker rm -f`로 컨테이너를 지운 뒤, 같은 볼륨에 연결한 새 컨테이너에서 동일한 데이터를 확인했다.
- **바인드 마운트**: 호스트의 특정 디렉토리를 연결해 데이터를 내 PC의 파일로 남긴다. 소스 코드·설정 파일처럼 내가 직접 편집하고 Git으로 관리해야 하는 것에 적합하다.

운영 습관도 함께 지킨다: 컨테이너를 지우기 전에 "이 안에만 있는 데이터가 있는가"를 먼저 확인하고, `docker rm -f`를 습관적으로 쓰지 않으며, `docker volume prune`은 연결된 컨테이너가 없는 볼륨을 모두 지우므로 컨테이너를 지운 직후에는 특히 주의한다.

→ 자세한 근거: [09 바인드 마운트·볼륨 영속성](09-volume.md), [00 개념 노트 - 컨테이너 삭제 후 데이터 소실 방지 대안](00-concepts.md#컨테이너-삭제-후-데이터-소실-방지-대안)

