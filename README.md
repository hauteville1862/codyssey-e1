# 내 컴퓨터에 개발자용 '작업실' 꾸미기

## I. 프로젝트 개요

개발 워크스테이션을 손으로 직접 세팅하며 리눅스 CLI(터미널), Docker(컨테이너), Git/GitHub(버전 관리)를 익히는 미션.

- **목표**: 코드가 "내 컴퓨터에서만" 돌아가는 문제를 줄이고, 팀원 누구나 같은 방식으로 실행·배포·디버깅할 수 있는 재현 가능한 개발 환경 구성.
- **수행 흐름**: 터미널로 작업 디렉토리·권한 정리 → Docker 설치 및 점검 → 기존 Dockerfile 기반 커스텀 이미지 제작 → 포트 매핑으로 접속 확인 → 바인드 마운트/볼륨으로 "변경 반영"과 "데이터 영속성" 검증 → Git 설정 및 GitHub/VSCode 연동
- **평가 관점**(`평가기준.md` 기준): 기능 동작 검증, 동작 구조 설계, 핵심 기술 원리 적용, 심층 인터뷰의 4개 항목으로 PASS/FAIL 평가

## II. 디렉토리 구조

```
codyssey-e1/
├── README.md              # 프로젝트 개요·진행 상황 요약
├── app/                    # Dockerfile 기반 커스텀 이미지 소스 (nginx:alpine + 정적 콘텐츠)
│   ├── Dockerfile
│   └── site/
│       └── index.html
├── codyssey/               # 미션 원본 지시문, 평가 기준 문서
│   ├── 미션.md
│   └── 평가기준.md
├── docs/                   # 항목별(01~10) 수행 로그 및 검증 결과
│   ├── 01-terminal.md
│   ├── 02-permission.md
│   └── ...
├── img/                    # 문서에서 참조하는 캡처 이미지
└── troubleshooting/         # 진행 중 발생한 문제와 해결 과정 기록
    └── screenshot-error.md
```

### 구조를 이렇게 나눈 기준

**1) "빌드에 들어가는 것"과 "설명하는 것"을 분리했다.**
`app/`에는 이미지에 실제로 들어가는 것만 둔다. Dockerfile의 빌드 컨텍스트가 `app/`이므로, 여기에 문서나 캡처를 섞으면 빌드 시 불필요한 파일까지 전송된다. 반대로 `docs/`의 내용은 아무리 늘어나도 이미지 크기에 영향을 주지 않는다. 그래서 `docker build`는 항상 `app/`에서 실행한다.

**2) 문서를 수행 순서대로 번호를 매겼다.**
`01-terminal` → `10-git-github`까지의 번호가 곧 실습 순서이자 미션 요구사항의 순서다. 평가자가 README 체크리스트에서 항목을 보고 같은 번호의 문서로 바로 이동할 수 있고, 문서끼리도 "05·06번의 `COPY`와 달리"처럼 번호로 상호 참조한다.

**3) 성격이 다른 글을 섞지 않았다.**
- `docs/01~10` — **무엇을 했는가**(명령 + 출력). 시간 순서대로 읽히는 수행 로그
- `docs/00-concepts.md` — **왜 그런가**(배경 개념, 인터뷰 답변). 실습 로그 중간에 긴 설명이 들어가면 흐름이 끊기므로 따로 뺐다
- `troubleshooting/` — **무엇이 틀어졌는가**(문제 → 가설 → 확인 → 해결). 성공 로그와 실패 기록은 읽는 목적이 달라 분리했다

**4) 이미지는 한 곳에 모았다.**
`img/`에 모아두고 문서에서 `../img/파일명.png`로 참조한다. 문서별로 이미지를 흩어놓으면 경로가 제각각이 되고, 실제로 이 경로 문제로 이미지가 깨지는 오류를 겪었다([트러블슈팅 문제 1](troubleshooting/screenshot-error.md#문제-1---스크린샷을-첨부했을-때-이미지가-제대로-보이지-않는-오류-해결)). 파일명도 `port-mapping1.png`, `vscode-github-login.png`처럼 용도가 드러나게 통일했다.

## III. 실행 환경

### 환경A — 코디세이 학습장 (iMac)

| 항목 | 값 |
| --- | --- |
| OS | macOS (iMac, 서울캠퍼스 학습장) |
| Shell/터미널 | zsh (macOS 기본, 프롬프트 `%`) |
| 컨테이너 런타임 | OrbStack (Docker Desktop 대체) |
| Docker | OrbStack 내장 엔진 — 버전은 학습장 환경에 따름 |
| Git | macOS 기본 제공 git |

> 서울캠퍼스 시스템 보안 정책상 `sudo` 권한 사용이 제한되어 Docker 직접 설치·제어 어려움. 이를 위해 별도의 `sudo` 권한 없이 컨테이너를 실행·관리할 수 있는 OrbStack을 사용하며, OrbStack 실행 시 내부적으로 Docker 엔진이 함께 구동되어 터미널에서 기존과 동일하게 `docker run`, `docker ps`, `docker build` 등의 명령어 사용 가능. (출처: `codyssey/미션.md`)

### 환경B — 개인 PC (Windows11)

| 항목 | 값 |
| --- | --- |
| OS | Windows 11 (MINGW64_NT-10.0-26200) |
| Shell/터미널 | Git Bash (bash.exe / MSYS) |
| 컨테이너 런타임 | Docker Desktop |
| Docker | 29.6.2 |
| Git | 2.55.0.windows.3 |

> Docker Desktop 설치됨(`docker context ls` 기준 `desktop-linux` 컨텍스트). 터미널에서 `docker` 명령어 바로 사용 가능.

### 문서의 로그가 어느 환경에서 나왔는지

여러 환경을 오가며 수행했기 때문에, 문서의 프롬프트 모양이 절마다 다르다. 읽을 때 혼동하지 않도록 아래에 대응을 정리한다.

| 프롬프트 모양 | 환경 | 해당 문서 |
| --- | --- | --- |
| `%` + `/Users/hauteville18620603/...` | 환경A (macOS, 학습장 iMac) | 01번 일부(`pwd`, `touch`, `mkdir`, `rm`) |
| `$` + `C:/Users/Yuhyun Lim/...` | 환경B (Windows, Git Bash) | 01·02·03·04·06~10번 대부분 |
| `yuhyun_lim@DESKTOP-1KO5JJM:/mnt/c/...$` | 환경B 내 WSL(Ubuntu) | 02번 실행 권한 실습, 06번 빌드 로그 |

- **WSL을 따로 쓴 이유**: Windows 파일시스템(`/mnt/c/...`)에서는 리눅스 실행 비트가 강제되지 않아 `chmod`의 효과를 관찰할 수 없다. 그래서 02번의 실행 권한 실습만 WSL의 리눅스 홈 디렉토리에서 수행했다([02-2 참고](docs/02-permission.md#2-2-파일-실행-권한-변경)).
- **Docker는 어느 쪽에서 실행해도 동일**하다. Docker Desktop의 WSL 통합 덕분에 Git Bash와 WSL이 같은 Docker 데몬을 바라보므로, 어느 터미널에서 `docker` 명령을 실행했는지는 결과에 영향을 주지 않는다.

## IV. 수행 항목

미션 요구사항(터미널/권한/Docker/Dockerfile/포트/마운트·볼륨/Git·GitHub) 기준 체크리스트. 각 항목의 상세 수행 로그와 검증 결과는 문서 링크에서 확인 가능.

| 번호 | 상태 | 항목 | 검증 방법 (무엇을 어떤 명령으로 확인했는가) | 문서 |
| --- | --- | --- | --- | --- |
| 01 | ✅ | 터미널 기본 조작 | `pwd`/`ls -al`로 위치·숨김 파일 확인, `touch`·`mkdir`·`cp -r`·`mv`·`rm -r`로 생성/복사/이동/삭제, `cat`으로 내용 확인, 절대·상대 경로 동작 비교 | [docs/01-terminal.md](docs/01-terminal.md) |
| 02 | ✅ | 파일/디렉토리 권한 확인 및 변경 실습 | 파일 1개·디렉토리 1개에 `chmod 644→755→400` 적용, 각 단계마다 `ls -l`로 전/후 비교 + 실제 실행·쓰기 시도로 차단 확인 | [docs/02-permission.md](docs/02-permission.md) |
| 03 | ✅ | Docker 설치·점검 및 기본 운영 명령 | `docker --version`·`docker info`로 설치와 데몬 동작 확인, `docker pull`/`images`/`ps -a`/`logs`/`stats`로 운영 명령 실행 | [docs/03-docker.md](docs/03-docker.md) |
| 04 | ✅ | 컨테이너 실행/관리 | `docker run hello-world` 성공, `ubuntu` 컨테이너 내부 진입 후 명령 수행, `exec`와 `attach`의 종료 동작 차이를 `docker ps`로 비교 | [docs/04-container.md](docs/04-container.md) |
| 05 | ✅ | 기존 Dockerfile 기반 커스텀 이미지 제작 | `nginx:alpine` 베이스에 `COPY site/ .`로 정적 콘텐츠 교체, 지시어별 커스텀 목적을 표로 정리 | [docs/05-dockerfile.md](docs/05-dockerfile.md) |
| 06 | ✅ | 기존 Dockerfile 기반 커스텀 이미지 빌드 | `docker build -t codyssey-e1-web .` 빌드 성공 로그 + `docker images` 목록 등재 확인 | [docs/06-image-build.md](docs/06-image-build.md) |
| 07 | ✅ | 포트 매핑 및 브라우저 접속 증거 | `-p 8080:80`, `-p 8081:80`으로 2회 실행 후 주소창이 포함된 브라우저 접속 화면 캡처 | [docs/07-port.md](docs/07-port.md) |
| 08 | ✅ | 커스텀 이미지 삭제 및 정리 | `docker stop`/`rm`으로 참조 컨테이너 정리 후 `docker rmi`, `docker images`로 삭제 확인 | [docs/08-cleanup.md](docs/08-cleanup.md) |
| 09 | ✅ | 바인드 마운트 반영 + Docker 볼륨 영속성 증거 | 바인드 마운트: 호스트 파일 수정 전/후 `curl` 비교 + 마운트 없는 컨테이너와 대비 검증. 볼륨: 데이터 작성 → `docker rm -f` → 새 컨테이너에서 동일 데이터 확인 | [docs/09-volume.md](docs/09-volume.md) |
| 10 | ✅ | Git 사용자 정보·기본 브랜치 설정 및 GitHub/VSCode 연동 | `git config --list`로 설정 반영 확인, `git remote -v`·`git push` 성공 로그, VSCode GitHub 로그인 화면 캡처 | [docs/10-git-github.md](docs/10-git-github.md) |

> ✅ 완료 / 🔶 진행 중 / ⬜ 미착수
> 배경 개념과 심층 인터뷰 상세 답변은 [docs/00-concepts.md](docs/00-concepts.md)에 별도로 정리했다.

## V. 심층 인터뷰

| 질문 | 관련 항목 | 답변 |
| --- | --- | --- |
| 컨테이너 내부 포트로 직접 접속할 수 없는 이유와 필요한 이유는? | 항목3 | 컨테이너는 호스트와 분리된 네트워크 네임스페이스를 가짐. `EXPOSE`로 문서화된 포트라도 `-p 호스트포트:컨테이너포트`로 명시적으로 매핑하지 않으면 호스트에서 접근 경로가 없어 접속 불가. `-p`는 이 둘을 잇는 다리 역할 |
| 프로젝트 디렉토리 구조를 어떤 기준으로 구성했는가? | 항목2 | ① 빌드 컨텍스트(`app/`)와 설명 문서(`docs/`) 분리 ② 문서 번호가 곧 수행 순서 ③ 수행 로그·배경 개념·트러블슈팅을 성격별로 분리 ④ 이미지는 `img/`에 집중 → [상세](#구조를-이렇게-나눈-기준) |
| 포트/볼륨 설정을 어떤 방식으로 재현 가능하게 정리했는가? | 항목2 | 포트·볼륨은 Dockerfile에 담기지 않는 런타임 옵션이라 기록하지 않으면 소실된다. 그래서 ① 값을 표로 먼저 고정 ② 환경 종속 부분(호스트 절대 경로)을 분리 표기 ③ 정리 명령까지 넣어 반복 실행 가능하게 구성 → [VII. 재현 방법](#vii-재현-방법), [상세](docs/00-concepts.md#포트볼륨-설정을-재현-가능하게-정리한-방식) |
| 이미지와 컨테이너의 차이를 빌드/실행/변경 관점에서? | 항목3 | 이미지는 `docker build`로 만들어지는 읽기 전용 템플릿이고, 컨테이너는 그 위에 쓰기 레이어를 얹어 실행한 인스턴스. 이미지는 수정이 불가해 바꾸려면 재빌드해야 하고, 컨테이너 내부 변경은 `docker rm` 시 함께 사라짐. 08에서 이미지를 지운 뒤 09에서 같은 Dockerfile로 복원한 것이 그 근거 → [상세](docs/00-concepts.md#이미지와-컨테이너의-차이-빌드실행변경) |
| 절대 경로/상대 경로는 어떤 상황에서 선택하는지? | 항목3 | "실행 위치가 항상 같다고 보장되는가"가 기준. 보장되면 상대 경로(Dockerfile의 `COPY site/ .`), 보장 안 되면 절대 경로(`docker run -v`의 호스트 경로 — Docker 데몬은 내 현재 위치를 모름) → [상세](docs/01-terminal.md#1-9-절대-경로와-상대-경로) |
| 파일 권한 숫자 표기(755, 644)가 결정되는 규칙은? | 항목3 | `r=4`, `w=2`, `x=1`을 소유자·그룹·기타 3자리에 각각 더한 8진수. `755`=`rwxr-xr-x`, `644`=`rw-r--r--`. 디렉토리에서 `x`는 실행이 아니라 진입(통과) 권한 → [상세](docs/02-permission.md#개념-요약) |
| 호스트 포트가 이미 사용 중이라 포트 매핑이 실패한다면, 어떤 순서로 원인을 진단할지? | 항목4 | ① 에러 문구(`port is already allocated`)로 충돌 확정 → ② `docker ps -a --filter publish=8080`으로 Docker 내부 점유 확인(Exited면 포트는 이미 해제된 상태) → ③ `netstat -ano \| findstr :8080`으로 호스트 프로세스 확인 → ④ 불필요한 컨테이너면 정리, 끌 수 없는 서비스면 **컨테이너 포트는 두고 호스트 포트만** 변경 → [상세](docs/00-concepts.md#호스트-포트-충돌-진단-순서) |
| 컨테이너 삭제 후 데이터가 사라진 경험이 있다면, 이를 방지하기 위한 대안은? | 항목4 | 원인은 데이터를 컨테이너와 수명이 같은 쓰기 레이어에 둔 것. 대안은 데이터를 컨테이너 바깥에 두는 것으로, 영속 데이터는 named volume, 내가 편집하는 소스는 바인드 마운트를 쓴다. 09에서 볼륨 연결 → 데이터 작성 → `docker rm -f` → 새 컨테이너에서 동일 데이터 확인으로 증명함 → [상세](docs/00-concepts.md#컨테이너-삭제-후-데이터-소실-방지-대안) |
| 이 미션에서 가장 어려웠던 지점과 해결 과정은? | 항목4 | 컨테이너 내부에서 `docker` 명령이 `command not found`로 실패한 문제. PATH 문제라는 첫 가설을 `which docker`로 반증(파일 자체가 없었음)하고, 격리된 파일시스템 때문이라는 가설을 호스트에서 재실행해 확인. 이후 `-p`(네트워크)와 `-v`(파일)가 모두 같은 격리 원리의 다른 표현임을 이해함 → [상세](docs/00-concepts.md#가장-어려웠던-지점과-해결-과정) |

## VI. 트러블슈팅

- [문제 1 - 스크린샷을 첨부했을 때 이미지가 제대로 보이지 않음](troubleshooting/screenshot-error.md#문제-1---스크린샷을-첨부했을-때-이미지가-제대로-보이지-않는-오류-해결)
- [문제 2 - 컨테이너 내부에서 docker 명령 실행 시 "command not found" 오류](troubleshooting/screenshot-error.md#문제-2---컨테이너-내부에서-docker-명령-실행-시-command-not-found-오류)

## VII. 재현 방법

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

### 재현 시 주의사항

- **호스트 경로는 환경마다 다르다.** 위 2)의 `-v` 앞부분은 이 PC에서는 `C:/Users/Yuhyun Lim/codyssey-e1/app/site`였다. 저장소를 어디에 클론하든 그 위치의 절대 경로로 바꿔야 한다. Git Bash에서는 `pwd -W`로 Windows 형식 절대 경로를 얻을 수 있다.
- **Windows(Git Bash)에서 경로가 자동 변환되는 문제**가 있다. `-v "C:/...:/usr/share/nginx/html"`이 의도와 다르게 해석되면 `MSYS_NO_PATHCONV=1`을 명령 앞에 붙인다. macOS/Linux(환경A)에서는 필요 없다.
- **포트가 이미 사용 중이면** 호스트 포트만 바꿔서 실행한다(예: `-p 8090:80`). 컨테이너 포트 `80`은 바꾸지 않는다. 진단 순서는 [심층 인터뷰 항목](#v-심층-인터뷰) 참고.
- **권한 실습(02번)의 실행 비트**는 Windows 마운트 경로(`/mnt/c/...`)에서 강제되지 않는다. 리눅스 홈 디렉토리나 macOS에서 실행해야 `Permission denied`가 재현된다.
