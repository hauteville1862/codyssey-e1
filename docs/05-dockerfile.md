# 05 기존 Dockerfile 기반 커스텀 이미지 제작

## 개념 요약

| 지시어 | 의미 |
| --- | --- |
| `FROM` | 베이스 이미지를 지정 (Dockerfile의 첫 줄) |
| `LABEL` | 이미지에 메타데이터(작성자, 버전 등)를 부여 |
| `WORKDIR` | 이후 명령이 실행될 작업 디렉토리 지정 |
| `COPY` | 호스트의 파일/디렉토리를 이미지 내부로 복사 |
| `EXPOSE` | 컨테이너가 사용하는 포트를 문서화 (실제 매핑은 `docker run -p`에서 수행) |

기존 Dockerfile/이미지를 기반으로 커스텀 이미지를 만드는 두 가지 방식 중 하나를 선택함.
- (A) 웹 서버 베이스 이미지(nginx/apache 등) + 정적 콘텐츠·설정 교체
- (B) Linux 베이스 이미지(ubuntu/alpine 등) + 패키지/사용자/환경변수/헬스체크 등 기본 기능 추가

## 명령어 요약

| 명령어 | 설명 |
| --- | --- |
| `docker build -t 이미지이름[:태그] 경로` | Dockerfile을 기반으로 이미지를 빌드 |
| `docker run -d -p 호스트포트:컨테이너포트 --name 이름 이미지` | 빌드한 이미지를 컨테이너로 실행 |
| `docker rmi 이미지이름[:태그]` | 빌드한 커스텀 이미지를 삭제 |

> 실제 빌드/실행 명령과 결과 로그, 포트 접속 증거는 [08 이미지 빌드](08-image-build.md), [06 포트 매핑](06-port.md)에서 다룸. 이 문서(05)는 Dockerfile 작성 자체(베이스 선택 이유, 지시어별 역할, 커스텀 포인트)에 집중함

### 5-1. 베이스 이미지 선택
```
선택: (A) 웹 서버 베이스 이미지 활용 - nginx:alpine

이유:
- 미션 요구사항 (A)안("웹 서버 베이스 이미지 + 정적 콘텐츠·설정 교체")에 부합
  미션 예시의 Dockerfile도 동일하게 `FROM nginx:alpine`을 사용함
- alpine 계열이라 이미지 용량이 작아 pull/build 속도가 빠름
> 08번(이미지 빌드)에서  반복적으로 build/run을 시연하기에 부담이 적음
- 정적 콘텐츠(`site/` 등)를 `/usr/share/nginx/html/`로 COPY하는 것만으로 커스텀 포인트가 명확하게 드러남
```
> [00 개념 노트 - nginx:alpine을 베이스 이미지로 선택한 이유](00-concepts.md#nginxalpine을-베이스-이미지로-선택한-이유) 참고

### 5-2. Dockerfile 작성
- Dockerfile: 이미지를 어떻게 만들지 순서대로 적어둔 빌드 스크립트 (텍스트 파일)
> [00 개념 노트 - Dockerfile이란 무엇인가](00-concepts.md#dockerfile이란-무엇인가) 참고
> [00 개념 노트 - Dockerfile 작성 단계별 가이드](00-concepts.md#dockerfile-작성-단계별-가이드) 참고

- 실제 파일
    - [app/Dockerfile](../app/Dockerfile)
    - [app/site/index.html](../app/site/index.html)

```dockerfile
# 베이스 이미지 지정 (Dockerfile의 첫 줄)
FROM nginx:alpine

# 이미지에 메타데이터(제목) 부여
LABEL org.opencontainers.image.title="codyssey-e1-web"

# 이후 명령이 실행될 작업 디렉토리 지정 (nginx 기본 웹 루트와 동일)
WORKDIR /usr/share/nginx/html

# 호스트의 site/ 디렉토리 내용을 WORKDIR로 복사 (정적 콘텐츠 교체 = 커스텀 포인트)
COPY site/ .

# 컨테이너가 사용하는 포트 문서화 (실제 매핑은 docker run -p에서 수행)
EXPOSE 80
```

- `app/site/index.html`: nginx 기본 페이지 대신 서빙할 정적 콘텐츠. `COPY site/ .`로 `WORKDIR`(=`/usr/share/nginx/html`)에 그대로 복사됨
> [00 개념 노트 - Dockerfile과 site/ 폴더의 관계](00-concepts.md#dockerfile과-site-폴더의-관계) 참고

### 5-3. 커스텀 포인트 정리
적용한 커스텀 포인트별 목적을 정리 (미션 요구사항: 커스텀 포인트 각각의 목적 간단 요약)

| 지시어/설정 | 적용 내용 | 목적 |
| --- | --- | --- |
| (예: `COPY`) | (예: `site/` → `/usr/share/nginx/html/`) | (예: 정적 콘텐츠 교체) |

(여기에 실제 적용한 커스텀 포인트를 표로 정리해 주세요)

### 5-4. 커스텀 이미지 삭제
`docker rmi 이미지이름[:태그]`: 더 이상 사용하지 않는 이미지를 삭제하는 명령어
- 해당 이미지를 참조하는 컨테이너(중지 상태 포함)가 남아있으면 삭제가 실패하므로, `docker ps -a`로 관련 컨테이너를 먼저 확인해 `docker rm`으로 지운 뒤 이미지를 삭제함
```bash
(여기에 커스텀 이미지 삭제 실행 결과를 붙여넣어 주세요)
```
> 사용하지 않는 이미지를 한 번에 정리할 때는 `docker image prune`(태그 없는 이미지만) 또는 `docker image prune -a`(어떤 컨테이너에도 쓰이지 않는 이미지 전부)를 사용할 수 있음
