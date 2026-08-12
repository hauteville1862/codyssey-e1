# 07 포트 매핑 및 브라우저 접속 증거

## 명령어 요약

| 명령어 | 설명 |
| --- | --- |
| `docker run -d -p 호스트포트:컨테이너포트 --name 이름 이미지` | 컨테이너 실행과 동시에 포트 매핑 (06번에서 이미 수행) |
| `curl http://localhost:호스트포트` | 터미널에서 응답을 텍스트로 확인 (브라우저 스크린샷의 보조/대안) |

> 이미지 빌드·컨테이너 실행 자체는 [06 이미지 빌드](06-image-build.md)에서 다룸. 이 문서(07)는 그 컨테이너에 실제로 접속해서 포트 매핑이 동작함을 증명하는 데 집중함

### 7-1. 브라우저 접속 증거
06번에서 `-p 8080:80`으로 실행한 `codyssey-e1-web-container`에 브라우저로 접속한 화면.
- 미션 요구사항: 주소창(포트 포함)과 응답 화면이 함께 보여야 함
- 캡처 파일은 `img/`에 저장 후 아래처럼 링크

![포트 매핑 접속 화면](../img/port-mapping1.png)

### 7-2. curl 응답으로 보조 확인
```bash
$ curl http://localhost:8080
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>codyssey-e1 custom nginx page</title>
<style>
  body {
    font-family: -apple-system, "Segoe UI", sans-serif;
    max-width: 640px;
    margin: 4rem auto;
    padding: 0 1.5rem;
    line-height: 1.6;
    color: #1a1a1a;
  }
  h1 { font-size: 1.6rem; }
  code {
    background: #f2f2f2;
    padding: 0.15rem 0.4rem;
    border-radius: 4px;
  }
  footer {
    margin-top: 2rem;
    font-size: 0.85rem;
    color: #666;
  }
</style>
</head>
<body>
  <h1>내 컴퓨터에 개발자용 '작업실' 꾸미기</h1>
  <p>이 페이지는 nginx 기본 웰컴 페이지가 아닌, <code>Dockerfile</code>의 <code>COPY site/ .</code> 지시어로 이미지에 복사된 정적 콘텐츠입니다.</p>
  <p>베이스 이미지 <code>nginx:alpine</code> 위에 이 <code>site/</code> 디렉토리 내용만 교체한 것이 이번 미션의 커스텀 포인트입니다.</p>
  <footer>codyssey-e1 · hauteville1862</footer>
</body>
</html>
```

### 7-3. 재현성 확인 — 다른 호스트 포트로 한 번 더 매핑
같은 이미지를 호스트 포트만 바꿔서 다시 실행해도 동일하게 접속되는지 확인
```bash
$ docker run -d -p 8081:80 --name codyssey-e1-web-container-2 codyssey-e1-web
bf8bfab18437bdae95b88078bc5639a252101016a207ccade1742cab8b375a97
```
```bash
$ curl http://localhost:8081
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>codyssey-e1 custom nginx page</title>
<style>
  body {
    font-family: -apple-system, "Segoe UI", sans-serif;
    max-width: 640px;
    margin: 4rem auto;
    padding: 0 1.5rem;
    line-height: 1.6;
    color: #1a1a1a;
  }
  h1 { font-size: 1.6rem; }
  code {
    background: #f2f2f2;
    padding: 0.15rem 0.4rem;
    border-radius: 4px;
  }
  footer {
    margin-top: 2rem;
    font-size: 0.85rem;
    color: #666;
  }
</style>
</head>
<body>
  <h1>내 컴퓨터에 개발자용 '작업실' 꾸미기</h1>
  <p>이 페이지는 nginx 기본 웰컴 페이지가 아닌, <code>Dockerfile</code>의 <code>COPY site/ .</code> 지시어로 이미지에 복사된 정적 콘텐츠입니다.</p>
  <p>베이스 이미지 <code>nginx:alpine</code> 위에 이 <code>site/</code> 디렉토리 내용만 교체한 것이 이번 미션의 커스텀 포인트입니다.</p>
  <footer>codyssey-e1 · hauteville1862</footer>
</body>
</html>
```
![두 번째 포트 매핑 접속 화면](../img/port-mapping2.png)