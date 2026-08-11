# 00 개념 노트

실습 문서(01~10)에서 다루기엔 흐름이 끊기는 배경 개념들을 모아두는 문서. 각 절 끝에 관련 실습 문서로 돌아가는 링크를 붙여둠.

## 목차

- [백그라운드 실행과 attach/exec](#백그라운드-실행과-attachexec)
- [Dockerfile이란 무엇인가](#dockerfile이란-무엇인가)
- [Dockerfile과 site/ 폴더의 관계](#dockerfile과-site-폴더의-관계)
- [nginx:alpine을 베이스 이미지로 선택한 이유](#nginxalpine을-베이스-이미지로-선택한-이유)
- [Dockerfile 작성 단계별 가이드](#dockerfile-작성-단계별-가이드)

## 백그라운드 실행과 attach/exec

관련 실습: [04 컨테이너 실행/관리 - 4-4](04-container.md)

### 포그라운드 vs 백그라운드

- **포그라운드(기본값)**: `docker run ubuntu bash`처럼 실행하면 터미널이 그 컨테이너에 붙잡힘. `-it` 없이 실행하면 입출력이 연결 안 돼 있어서 컨테이너의 메인 프로세스가 순식간에 끝나버리고, 그러면 컨테이너도 같이 종료됨 (4-2에서 다룬 내용).
- **백그라운드(-d, detached)**: `docker run -d 이미지`처럼 `-d` 옵션을 주면 컨테이너를 뒤에서 띄워놓고 터미널은 바로 프롬프트로 돌아옴. 컨테이너 ID만 출력되고 끝.

### 왜 attach/exec 실습에 -d가 필요한가

`docker attach`나 `docker exec`는 "현재 실행 중인" 컨테이너에 연결하는 명령어다. 그런데 지금까지 문서의 예시들(`docker run -it ubuntu bash` → `exit`)은 실행하자마자 상호작용하고 바로 종료해버리는 흐름이라, attach/exec를 시도할 시점엔 이미 컨테이너가 죽어있는 경우가 많다.

또, 포그라운드로 `-it` 실행한 터미널은 이미 그 세션을 붙잡고 있어서, 같은 컨테이너에 attach/exec를 "다른 터미널에서" 시도해보려면 애초에 백그라운드로 띄워야 한다.

### 실습용으로 컨테이너를 살려두는 방법

```bash
$ docker run -dit --name test-box ubuntu bash
```

- `-d`: 백그라운드 실행
- `-it`: tty(가상 터미널) 할당 + 표준입력 연결 유지 → bash가 입력을 기다리며 죽지 않고 계속 살아있음
- `-dit`는 사실 `-d -i -t`를 합친 것

이제 `docker ps`로 확인하면 `test-box`가 계속 `Up` 상태로 떠 있다.

### attach vs exec 비교 실습

**exec (별도 프로세스로 진입, 나가도 컨테이너 안 죽음)**
```bash
$ docker exec -it test-box bash
root@...:/# echo hi
hi
root@...:/# exit
$ docker ps   # test-box가 여전히 Up 상태
```

**attach (메인 프로세스 자체에 연결, 나가면 메인 프로세스=컨테이너도 종료될 수 있음)**
```bash
$ docker attach test-box
root@...:/# exit
$ docker ps   # test-box가 사라짐 (Exited)
```

> attach를 먼저 하면 컨테이너가 죽어서 그 뒤 exec를 실습할 대상이 없어지니, exec를 먼저 해보고 마지막에 attach로 종료시키는 순서를 추천한다.

## Dockerfile이란 무엇인가

관련 실습: [05 Dockerfile 작성 - 5-2](05-dockerfile.md#5-2-dockerfile-작성)

Dockerfile은 이미지를 어떻게 만들지 순서대로 적어놓은 텍스트 형태의 빌드 설명서(레시피)임.

- 사람이 매번 손으로 프로그램을 설치·설정하는 대신, 그 절차를 지시어로 파일 하나에 기록해 둠.
- `docker build`가 이 파일을 위에서부터 순서대로 읽어 이미지를 자동으로 조립함.
- 같은 Dockerfile이면 실행하는 사람/컴퓨터가 달라도 항상 동일한 결과(이미지)가 나옴 — 재현성 확보가 핵심 목적.
- 완성된 이미지를 실행하면 컨테이너가 되고, 그 안에서 실제 프로그램(이 미션에서는 nginx)이 동작함.

정리하면 Dockerfile은 "이미지를 어떻게 조립할지" 적어둔 설계도, 이미지는 그 설계도로 찍어낸 실행 가능한 패키지, 컨테이너는 그 패키지를 실제로 실행한 상태임.

## Dockerfile과 site/ 폴더의 관계

관련 실습: [05 Dockerfile 작성 - 5-2](05-dockerfile.md#5-2-dockerfile-작성)

`app/site/`는 이미지에 넣을 정적 콘텐츠가 담긴 일반 폴더일 뿐이고, 실제로 그 내용을 이미지 안으로 옮기는 건 Dockerfile의 `COPY site/ .` 한 줄임.

- `WORKDIR /usr/share/nginx/html`로 목적지를 지정해뒀으므로 `COPY site/ .`의 `.`은 그 경로를 가리킴.
- `site/`처럼 소스 경로 끝에 `/`를 붙이면 `site`라는 폴더 자체가 아니라 **그 안의 내용물만** 목적지로 복사됨 (`site/index.html` → `index.html`).
- 복사는 `docker build` 실행 시점에 딱 한 번 일어남 — 빌드 이후 `site/index.html`을 고쳐도 이미 만들어진 이미지에는 반영되지 않고, 반영하려면 다시 빌드해야 함.
- "수정하면 즉시 반영"되는 방식은 09번 문서의 **바인드 마운트**가 따로 담당함. 05~08번에서 쓰는 `COPY`는 그와 달리 빌드 시점에 내용을 고정해서 넣는 방식.

## nginx:alpine을 베이스 이미지로 선택한 이유

관련 실습: [05 Dockerfile 작성 - 5-1](05-dockerfile.md#5-1-베이스-이미지-선택)

### alpine 계열이 이미지 용량이 작은 이유

`nginx:latest`(데비안 기반)와 `nginx:alpine`(Alpine Linux 기반)은 같은 nginx를 담고 있어도 베이스 OS 레이어의 구성이 다르다.

- **베이스 OS 차이**: `nginx:latest`는 Debian(glibc, apt 패키지 생태계)을 베이스로 쓰는 반면, `nginx:alpine`은 Alpine Linux를 베이스로 씀. Alpine은 glibc 대신 경량 C 라이브러리인 **musl libc**를 쓰고, 기본 셸/유틸리티도 GNU coreutils가 아닌 **BusyBox**(여러 명령어를 하나의 실행 파일로 압축한 것)로 구성됨.
- **불필요한 패키지 최소화**: Debian 기반 이미지는 문서, 로케일, apt 캐시 등 범용 리눅스 배포판이 기본으로 포함하는 부가 파일들을 상당수 갖고 있는 반면, Alpine은 "컨테이너 안에서 딱 필요한 것만" 담는 것을 지향해서 기본 설치 크기 자체가 훨씬 작음(Alpine 베이스 이미지 자체가 수 MB 수준).
- **결과**: 같은 nginx 기능을 제공하면서도 이미지 레이어 수와 총 용량이 작아짐 → `docker pull`/`docker build`가 빨라지고, `docker push`/이미지 배포 시 전송량도 줄어듦. 이 미션처럼 07~09 문서에서 이미지를 반복적으로 빌드·삭제·재빌드하는 과정에서는 이 차이가 체감상 크게 느껴짐.
- **트레이드오프(참고)**: musl libc는 glibc와 100% 호환은 아니라서, glibc에 강하게 의존하는 일부 바이너리/패키지는 Alpine에서 문제가 생길 수 있음. 하지만 이 미션은 nginx가 정적 파일을 서빙하는 단순한 시나리오라 해당 이슈와는 무관함.

### COPY 한 줄만으로 커스텀 포인트가 드러나는 이유

nginx 공식 이미지(alpine 태그 포함)는 내부적으로 다음과 같이 동작하도록 이미 구성되어 있음.

- nginx의 기본 설정 파일(`/etc/nginx/conf.d/default.conf`)이 **웹 루트를 `/usr/share/nginx/html`로 지정**해 둠.
- 이미지에 포함된 `docker-entrypoint.sh`가 컨테이너 시작 시 자동으로 nginx를 이 설정으로 구동함 (별도의 `CMD`/`ENTRYPOINT`를 직접 작성하지 않아도 됨 — `03-docker.md` 3-5의 `docker run nginx` 로그에서 `/docker-entrypoint.sh: Configuration complete; ready for start up`로 확인 가능).
- 즉, 베이스 이미지가 "정적 파일을 `/usr/share/nginx/html`에 두면 그대로 서빙한다"는 동작을 이미 갖추고 있기 때문에, 커스텀 Dockerfile에서 해야 할 일은 오직 **호스트의 정적 콘텐츠를 그 경로로 복사하는 것** 뿐임.

```dockerfile
FROM nginx:alpine
COPY site/ /usr/share/nginx/html/
```

- 이 한 줄이 곧 미션이 요구하는 "커스텀 포인트"(정적 콘텐츠 교체)가 됨 — nginx 실행 로직, 포트 리스닝, 워커 프로세스 관리 등 복잡한 부분은 베이스 이미지가 이미 해결해 두었고, 우리가 명시적으로 바꾼 부분(`COPY`)만 남기 때문에 Dockerfile을 읽는 사람이 "무엇을 커스터마이징했는지" 한눈에 파악할 수 있음.
- 반대로 (B)안(ubuntu/alpine 베이스)이었다면 nginx/apache 같은 웹 서버를 `RUN apt-get install`/`apk add`로 직접 설치하고, 설정 파일 경로도 손수 지정해야 해서 Dockerfile이 길어지고 "어디까지가 베이스의 기본 동작이고 어디부터가 내 커스텀인지"가 상대적으로 덜 명확해짐.

## Dockerfile 작성 단계별 가이드

관련 실습: [05 Dockerfile 작성 - 5-2](05-dockerfile.md#5-2-dockerfile-작성)

5-1에서 정한 `(A) nginx:alpine + 정적 콘텐츠` 방식을 기준으로, `app/Dockerfile`을 어떤 순서로 채워 넣을지 정리한 노트.

### 1. `FROM` — 베이스 이미지 지정 (항상 첫 줄)

5-1에서 정한 `nginx:alpine`을 명시. 형식은 `FROM 이미지이름:태그`. 태그를 생략하면 `latest`가 자동 적용되는데, 재현성을 위해 `alpine`처럼 태그를 명시하는 편이 좋음.

### 2. (선택) `LABEL` — 메타데이터

누가/왜 만든 이미지인지 표시하고 싶으면 `LABEL key="value"` 형식으로 추가. 필수는 아니지만 5-3 커스텀 포인트 표에 채울 항목이 됨.

### 3. `WORKDIR` — 작업 디렉토리 지정

nginx가 정적 파일을 서빙하는 기본 경로는 `/usr/share/nginx/html`([앞 절](#copy-한-줄만으로-커스텀-포인트가-드러나는-이유) 참고). `WORKDIR 그경로`로 지정해두면, 이후 `COPY`의 목적지를 풀 경로 대신 `.`으로 쓸 수 있고 `RUN`/`CMD`도 이 디렉토리 기준으로 실행됨.

### 4. `COPY` — 정적 콘텐츠 복사

`COPY 호스트경로 이미지내경로` 형식. 호스트 쪽은 Dockerfile이 있는 `app/` 기준 상대경로(`site/`), 이미지 쪽은 `WORKDIR`을 잡았다면 `.`. 이 한 줄이 "nginx 기본 웰컴 페이지를 내 콘텐츠로 교체"하는 핵심 커스텀 포인트.

### 5. (선택) `EXPOSE` — 포트 문서화

nginx는 기본적으로 80번 포트를 씀. `EXPOSE 80`을 추가하면 이 이미지가 어떤 포트를 쓰는지 문서화되지만, 실제 포트 매핑은 [06 포트 매핑](06-port.md)에서 `docker run -p`로 수행하므로 여기선 "설명 목적"임에 유의.

### 6. `CMD`/`ENTRYPOINT` 결정

`nginx:alpine` 베이스 이미지 자체에 이미 nginx를 포그라운드로 실행하는 `CMD`가 내장돼 있음([앞 절](#copy-한-줄만으로-커스텀-포인트가-드러나는-이유) 참고). 선택지는 두 가지.
- 그대로 생략 → 베이스 이미지의 기본 CMD 사용 (가장 단순)
- 직접 `CMD ["nginx", "-g", "daemon off;"]`처럼 명시 → 의도적으로 지정했음을 5-3 표에 명확히 남길 수 있음

둘 중 하나를 고르고, 이유를 5-3에 한 줄로 남김.

### 7. `app/site/index.html` 작성

4번에서 복사할 실제 콘텐츠. 순수 HTML로 간단한 텍스트만 있어도 충분하며, nginx 기본 페이지와 구분되는 내용이면 됨.

### 8. 지시어 순서 최종 점검

Dockerfile은 위에서 아래로 레이어가 쌓이므로 순서가 중요함. 권장 순서:
```
FROM → LABEL → WORKDIR → COPY → EXPOSE → CMD
```

### 9. 05 문서 채우기로 마무리

- **5-2**: 완성된 `app/Dockerfile` 전체 내용을 코드블록에 붙여넣기
- **5-3**: 실제 사용한 지시어(`WORKDIR`, `COPY`, `EXPOSE` 등)별로 "적용 내용 → 목적"을 표에 정리

작성이 끝나면 [08 이미지 빌드](08-image-build.md)에서 `docker build`/`docker run`으로 실제 빌드·실행해 검증하는 흐름으로 이어짐.
