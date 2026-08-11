# 06 기존 Dockerfile 기반 커스텀 이미지 빌드

## 명령어 요약

| 명령어 | 설명 |
| --- | --- |
| `docker build -t 이미지이름[:태그] 경로` | Dockerfile을 기반으로 이미지를 빌드 |
| `docker images` | 빌드된 이미지가 로컬 목록에 추가됐는지 확인 |
| `docker run -d -p 호스트포트:컨테이너포트 --name 이름 이미지` | 빌드한 이미지를 컨테이너로 실행 |
| `docker ps` | 컨테이너가 정상적으로 실행 중인지 확인 |

### 6-1. 이미지 빌드
`docker build -t 이미지이름[:태그] 경로`: Dockerfile을 기반으로 이미지를 빌드하는 명령어
```bash
yuhyun_lim@DESKTOP-1KO5JJM:/mnt/c/Users/Yuhyun Lim/codyssey-e1$ cd app
```
```bash
yuhyun_lim@DESKTOP-1KO5JJM:/mnt/c/Users/Yuhyun Lim/codyssey-e1/app$ docker build -t codyssey-e1-web .
[+] Building 5.8s (8/8) FINISHED                                                          docker:default
 => [internal] load build definition from Dockerfile                                                0.2s
 => => transferring dockerfile: 576B                                                                0.0s
 => [internal] load metadata for docker.io/library/nginx:alpine                                     2.3s
 => [internal] load .dockerignore                                                                   0.2s
 => => transferring context: 2B                                                                     0.0s
 => CACHED [1/3] FROM docker.io/library/nginx:alpine@sha256:4a73073bd557c65b759505da037898b61f1be6  0.2s
 => => resolve docker.io/library/nginx:alpine@sha256:4a73073bd557c65b759505da037898b61f1be6cbcc3c2  0.1s
 => [internal] load build context                                                                   0.3s
 => => transferring context: 1.07kB                                                                 0.1s
 => [2/3] WORKDIR /usr/share/nginx/html                                                             0.2s
 => [3/3] COPY site/ .                                                                              0.3s
 => exporting to image                                                                              1.6s
 => => exporting layers                                                                             0.8s
 => => exporting manifest sha256:b9dbe9ae446a7989faabd7c0620050281b1e58884d6c2ab76898bebd6c718db4   0.1s
 => => exporting config sha256:04d4e4812b1bd29b3b85492ae49aecc58ae7e69147b7d4096fc9638c18979cbd     0.1s
 => => exporting attestation manifest sha256:e117af227bbf45b501833f2e4a676a5b553a4513fd9af08764347  0.2s
 => => exporting manifest list sha256:609140e712e6aa36cd56f1f832e6f75475fa20c9884e3a2bf1624744e2e3  0.1s
 => => naming to docker.io/library/codyssey-e1-web:latest                                           0.0s
 => => unpacking to docker.io/library/codyssey-e1-web:latest                                        0.2s
```
- 빌드 성공 후 `docker images`로 목록에 새 이미지가 추가됐는지 확인
```bash
$ docker images
                                                                                     i Info →   U  In Use
IMAGE                    ID             DISK USAGE   CONTENT SIZE   EXTRA
codyssey-e1-web:latest   609140e712e6       92.7MB         26.1MB        
ubuntu:latest            678c6550cc43        160MB         45.3MB    U   
```

### 6-2. 컨테이너 실행
`docker run -d -p 호스트포트:컨테이너포트 --name 이름 이미지`: 빌드한 이미지를 컨테이너로 실행하는 명령어
```bash
$ docker run -d -p 8080:80 --name codyssey-e1-web-container codyssey-e1-web
93b2ca033581efa129de013f030baf2c05e509179cdc81f45276bd99c357cb35
```
- 실행 성공 후 `docker ps`로 컨테이너가 `Up` 상태인지 확인
```bash
$ docker ps
CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS          PORTS                                     NAMES
93b2ca033581   codyssey-e1-web   "/docker-entrypoint.…"   17 seconds ago   Up 15 seconds   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   codyssey-e1-web-container
```
> 포트 매핑 옵션(`-p`)과 브라우저 접속 증거는 [07 포트 매핑](07-port.md)에서 다룸. 테스트가 끝난 뒤 컨테이너/이미지 정리는 [08 커스텀 이미지 삭제 및 정리](08-cleanup.md)에서 다룸
