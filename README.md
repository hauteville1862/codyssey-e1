# README

## 1. 프로젝트 개요

**내 컴퓨터에 개발자용 '작업실' 꾸미기** — 개발 워크스테이션을 손으로 직접 세팅하며 리눅스 CLI(터미널), Docker(컨테이너), Git/GitHub(버전 관리)를 익히는 미션입니다.

- **목표**: 코드가 "내 컴퓨터에서만" 돌아가는 문제를 줄이고, 팀원 누구나 같은 방식으로 실행·배포·디버깅할 수 있는 재현 가능한 개발 환경을 구성한다.
- **수행 흐름**: 터미널로 작업 디렉토리·권한 정리 → Docker 설치 및 점검 → 기존 Dockerfile 기반 커스텀 이미지 제작 → 포트 매핑으로 접속 확인 → 바인드 마운트/볼륨으로 "변경 반영"과 "데이터 영속성" 검증 → Git 설정 및 GitHub/VSCode 연동
- **평가 관점**(`평가기준.md` 기준): 기능 동작 검증, 동작 구조 설계, 핵심 기술 원리 적용, 심층 인터뷰의 4개 항목으로 PASS/FAIL 평가

## 2. 실행 환경

| 항목 | 값 |
| --- | --- |
| OS | Windows 11 (MINGW64_NT-10.0-26200) |
| Shell/터미널 | Git Bash (bash.exe / MSYS) |
| Docker | 29.6.2 |
| Git | 2.55.0.windows.3 |

> Docker Desktop이 설치되어 있으며(`docker context ls` 기준 `desktop-linux` 컨텍스트), 터미널에서 `docker` 명령어를 바로 사용할 수 있습니다.

## 3. 수행 항목

미션 요구사항(터미널/권한/Docker/Dockerfile/포트/마운트·볼륨/Git·GitHub) 기준 체크리스트이며, 각 항목의 상세 수행 로그와 검증 결과는 문서 링크에서 확인할 수 있습니다.

| 번호 | 상태 | 항목 | 문서 |
| --- | --- | --- | --- |
| 01 | ✅ | 터미널 기본 조작 | [docs/01-terminal.md](docs/01-terminal.md) |
| 02 | ✅ | 파일/디렉토리 권한 확인 및 변경 실습 | [docs/02-permission.md](docs/02-permission.md) |
| 03 | ⬜ | Docker 설치·점검 및 기본 운영 명령 | [docs/03-docker.md](docs/03-docker.md) |
| 04 | ⬜ | 기존 Dockerfile 기반 커스텀 이미지 제작 — Dockerfile 작성 | [docs/04-dockerfile.md](docs/04-dockerfile.md) |
| 05 | ⬜ | 포트 매핑 및 브라우저 접속 증거 | [docs/05-port.md](docs/05-port.md) |
| 06 | ⬜ | hello-world / ubuntu 컨테이너 실행 실습 및 목록 확인 | [docs/06-list.md](docs/06-list.md) |
| 07 | ⬜ | 기존 Dockerfile 기반 커스텀 이미지 제작 — 이미지 빌드 | [docs/07-image-build.md](docs/07-image-build.md) |
| 08 | ⬜ | 바인드 마운트 반영 + Docker 볼륨 영속성 증거 | [docs/08-volume.md](docs/08-volume.md) |
| 09 | ⬜ | Git 사용자 정보·기본 브랜치 설정 및 GitHub/VSCode 연동 | [docs/09-git-github.md](docs/09-git-github.md) |

## 4. 트러블슈팅

- [문제 1 - 스크린샷을 첨부했을 때 이미지가 제대로 보이지 않음](troubleshooting/screenshot-error.md)
- 문제 2 - [진행 중]
