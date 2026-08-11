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