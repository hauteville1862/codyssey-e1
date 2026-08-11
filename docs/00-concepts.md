# 00 개념 노트

실습 문서(01~10)에서 다루기엔 흐름이 끊기는 배경 개념들을 모아두는 문서. 각 절 끝에 관련 실습 문서로 돌아가는 링크를 붙여둠.

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
