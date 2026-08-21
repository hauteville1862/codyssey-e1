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
| 11 | ✅ | 심층 인터뷰 및 재현 방법 | 심층 인터뷰 질문 4개 정리, 포트·볼륨 고정값과 빌드/마운트/볼륨 검증 명령어로 재현 절차 정리 | [docs/11-interview.md](docs/11-interview.md) |

## V. 트러블슈팅

- [문제 1 - 스크린샷을 첨부했을 때 이미지가 제대로 보이지 않음](troubleshooting/screenshot-error.md#문제-1---스크린샷을-첨부했을-때-이미지가-제대로-보이지-않는-오류-해결)
- [문제 2 - 컨테이너 내부에서 docker 명령 실행 시 "command not found" 오류](troubleshooting/screenshot-error.md#문제-2---컨테이너-내부에서-docker-명령-실행-시-command-not-found-오류)
