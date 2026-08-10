# 내 컴퓨터에 개발자용 '작업실' 꾸미기

## 1. 프로젝트 개요

개발 워크스테이션을 손으로 직접 세팅하며 리눅스 CLI(터미널), Docker(컨테이너), Git/GitHub(버전 관리)를 익히는 미션.

- **목표**: 코드가 "내 컴퓨터에서만" 돌아가는 문제를 줄이고, 팀원 누구나 같은 방식으로 실행·배포·디버깅할 수 있는 재현 가능한 개발 환경 구성.
- **수행 흐름**: 터미널로 작업 디렉토리·권한 정리 → Docker 설치 및 점검 → 기존 Dockerfile 기반 커스텀 이미지 제작 → 포트 매핑으로 접속 확인 → 바인드 마운트/볼륨으로 "변경 반영"과 "데이터 영속성" 검증 → Git 설정 및 GitHub/VSCode 연동
- **평가 관점**(`평가기준.md` 기준): 기능 동작 검증, 동작 구조 설계, 핵심 기술 원리 적용, 심층 인터뷰의 4개 항목으로 PASS/FAIL 평가

## 2. 디렉토리 구조

```
codyssey-e1/
├── README.md              # 프로젝트 개요·진행 상황 요약
├── codyssey/               # 미션 원본 지시문, 평가 기준 문서
│   ├── 미션.md
│   └── 평가기준.md
├── docs/                   # 항목별(01~09) 수행 로그 및 검증 결과
│   ├── 01-terminal.md
│   ├── 02-permission.md
│   └── ...
├── screenshots/            # 문서에서 참조하는 캡처 이미지
└── troubleshooting/         # 진행 중 발생한 문제와 해결 과정 기록
    └── screenshot-error.md
```

## 3. 실행 환경

### 환경A — 코디세이 학습장 (iMac)

| 항목 | 값 |
| --- | --- |
| OS | macOS (iMac, 서울캠퍼스 학습장) |
| Shell/터미널 | 미션 문서 미기재 |
| 컨테이너 런타임 | OrbStack (Docker Desktop 대체) |
| Docker | 미션 문서 미기재 |
| Git | 미션 문서 미기재 |

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

## 4. 수행 항목

미션 요구사항(터미널/권한/Docker/Dockerfile/포트/마운트·볼륨/Git·GitHub) 기준 체크리스트. 각 항목의 상세 수행 로그와 검증 결과는 문서 링크에서 확인 가능.

| 번호 | 상태 | 항목 | 문서 |
| --- | --- | --- | --- |
| 01 | ✅ | 터미널 기본 조작 | [docs/01-terminal.md](docs/01-terminal.md) |
| 02 | ✅ | 파일/디렉토리 권한 확인 및 변경 실습 | [docs/02-permission.md](docs/02-permission.md) |
| 03 | ⬜ | Docker 설치·점검 및 기본 운영 명령 | [docs/03-docker.md](docs/03-docker.md) |
| 04 | ⬜ | 기존 Dockerfile 기반 커스텀 이미지 제작 | [docs/04-dockerfile.md](docs/04-dockerfile.md) |
| 05 | ⬜ | 포트 매핑 및 브라우저 접속 증거 | [docs/05-port.md](docs/05-port.md) |
| 06 | ⬜ | hello-world / ubuntu 컨테이너 실행 실습 및 목록 확인 | [docs/06-list.md](docs/06-list.md) |
| 07 | ⬜ | 기존 Dockerfile 기반 커스텀 이미지 빌드 | [docs/07-image-build.md](docs/07-image-build.md) |
| 08 | ⬜ | 바인드 마운트 반영 + Docker 볼륨 영속성 증거 | [docs/08-volume.md](docs/08-volume.md) |
| 09 | ⬜ | Git 사용자 정보·기본 브랜치 설정 및 GitHub/VSCode 연동 | [docs/09-git-github.md](docs/09-git-github.md) |

## 5. 트러블슈팅

- [문제 1 - 스크린샷을 첨부했을 때 이미지가 제대로 보이지 않음](troubleshooting/screenshot-error.md)
- 문제 2 - [진행 중]
