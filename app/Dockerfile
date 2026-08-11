FROM nginx:alpine

LABEL org.opencontainers.image.title="my-custom-nginx"
LABEL org.opencontainers.image.description="codyssey-e1 mission 05"

ENV APP_ENV=production

WORKDIR /usr/share/nginx/html

COPY site/ .

EXPOSE 80
