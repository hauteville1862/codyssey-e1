# 03 Docker 설치 및 기본 운영

## 개념 요약

| 구분 | 의미 | 비유 |
| --- | --- | --- |
| Dockerfile | 이미지를 어떻게 만들지 순서대로 적어둔 빌드 스크립트(텍스트 파일) | 설계 지침서(레시피) |
| 이미지(Image) | 컨테이너 실행에 필요한 파일·설정을 담은 불변 템플릿(빌드 결과물) | 설계도 |
| 컨테이너(Container) | 이미지를 실행한 인스턴스. 실행 중 상태가 변할 수 있으나 삭제하면 변경 내용은 사라짐 | 설계도로 지은 실제 건물 |

Dockerfile을 `docker build`로 빌드하면 이미지가 만들어지고, 이미지를 `docker run`으로 실행하면 컨테이너가 되며, 컨테이너 안에서의 변경은 `docker commit` 없이는 이미지에 반영되지 않음

## 명령어 요약

| 명령어 | 설명 |
| --- | --- |
| `docker --version` | 설치된 Docker 클라이언트 버전 출력 |
| `docker info` | Docker 데몬 상태 및 시스템 정보 출력 |
| `docker pull 이미지명` | 레지스트리에서 이미지 다운로드 |
| `docker images` | 로컬에 저장된 이미지 목록 출력 |
| `docker run 이미지명` | 이미지를 컨테이너로 실행 |
| `docker ps` | 실행 중인 컨테이너 목록 출력 |
| `docker ps -a` | 중지된 컨테이너까지 포함한 전체 목록 출력 |
| `docker stop 컨테이너` | 실행 중인 컨테이너 중지 |
| `docker logs 컨테이너` | 컨테이너의 표준 출력·에러 로그 확인 |
| `docker stats` | 컨테이너의 CPU·메모리 등 리소스 사용량 실시간 확인 |
| `docker rm 컨테이너` | 중지된 컨테이너 삭제 |
| `docker rmi 이미지` | 사용하지 않는 이미지 삭제 |
| `docker commit 컨테이너 이미지이름[:태그]` | 컨테이너의 현재 상태를 이름을 지정해 새 이미지로 저장 |

### 3-1. Docker 버전 확인
`docker --version`: 설치된 Docker 클라이언트 버전을 출력하는 명령어
```bash
$ docker --version
Docker version 29.6.2, build dfc4efb
```

### 3-2. Docker 데몬 동작 확인
`docker info`: Docker 데몬(엔진)이 정상적으로 동작 중인지, 컨테이너·이미지 개수 등 시스템 정보를 출력하는 명령어
```bash
$ docker info
Client:
 Version:    29.6.2
 Context:    default
 Debug Mode: false
 Plugins:
  agent: Docker AI Agent Runner (Docker Inc.)
    Version:  v1.115.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-agent
  ai: Docker AI Agent - Ask Gordon (Docker Inc.)
    Version:  v1.27.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-ai
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.35.0-desktop.2
    Path:     /usr/local/lib/docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v5.3.1
    Path:     /usr/local/lib/docker/cli-plugins/docker-compose
  debug: Get a shell into any image or container (Docker Inc.)
    Version:  0.0.47
    Path:     /usr/local/lib/docker/cli-plugins/docker-debug
  desktop: Docker Desktop commands (Docker Inc.)
    Version:  v0.4.3
    Path:     /usr/local/lib/docker/cli-plugins/docker-desktop
  dhi: CLI for managing Docker Hardened Images (Docker Inc.)
    Version:  v0.0.7
    Path:     /usr/local/lib/docker/cli-plugins/docker-dhi
  extension: Manages Docker extensions (Docker Inc.)
    Version:  v0.2.31
    Path:     /usr/local/lib/docker/cli-plugins/docker-extension
  init: Creates Docker-related starter files for your project (Docker Inc.)
    Version:  v1.4.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-init
  mcp: Docker MCP Plugin (Docker Inc.)
    Version:  v0.43.3
    Path:     /usr/local/lib/docker/cli-plugins/docker-mcp
  model: Docker Model Runner (Docker Inc.)
    Version:  v1.2.6
    Path:     /usr/local/lib/docker/cli-plugins/docker-model
  offload: Docker Offload (Docker Inc.)
    Version:  v0.6.9
    Path:     /usr/local/lib/docker/cli-plugins/docker-offload
  pass: Docker Pass Secrets Manager Plugin (beta) (Docker Inc.)
    Version:  v0.2.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-pass
  sandbox: "docker sandbox" is deprecated, use Docker Sandboxes instead (Docker Inc.)
    Version:  v0.13.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-sandbox
  scout: Docker Scout (Docker Inc.)
    Version:  v1.23.1
    Path:     /usr/local/lib/docker/cli-plugins/docker-scout

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 29.6.2
 Storage Driver: overlayfs
  driver-type: io.containerd.snapshotter.v1
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
 CDI spec directories:
  /etc/cdi
  /var/run/cdi
 Discovered Devices:
  cdi: docker.com/gpu=webgpu
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 nvidia runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: e53c7c1516c3b2bff98eb76f1f4117477e6f4e66
 runc version: v1.3.6-0-g491b69ba
 init version: de40ad0
 Security Options:
  seccomp
   Profile: builtin
  cgroupns
 Kernel Version: 6.18.33.2-microsoft-standard-WSL2
 Operating System: Docker Desktop
 OSType: linux
 Architecture: x86_64
 CPUs: 12
 Total Memory: 7.434GiB
 Name: docker-desktop
 ID: bed5617e-13e3-4bf0-8563-62d3346dc04d
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 HTTP Proxy: http.docker.internal:3128
 HTTPS Proxy: http.docker.internal:3128
 No Proxy: hubproxy.docker.internal
 Labels:
  com.docker.desktop.address=unix:///var/run/docker-cli.sock
 Experimental: false
 Insecure Registries:
  hubproxy.docker.internal:5555
  ::1/128
  127.0.0.0/8
 Live Restore Enabled: false
 Firewall Backend: iptables
```

### 3-3. 이미지 다운로드
`docker pull 이미지명`: 레지스트리(Docker Hub 등)에서 이미지를 내려받는 명령어
```bash
$ docker pull hello-world
Using default tag: latest
latest: Pulling from library/hello-world
4f55086f7dd0: Pull complete 
d5e71e642bf5: Download complete 
Digest: sha256:7f4da0fc94bcece205a8c0b6f4d11c8196924654ffe5c4d1aa439b7f632048b2
Status: Downloaded newer image for hello-world:latest
docker.io/library/hello-world:latest
```
```bash
$ docker pull ubuntu
Using default tag: latest
latest: Pulling from library/ubuntu
a7fb98a8eddd: Pull complete 
617772c7d19b: Pull complete 
cc2ffdbc1bf7: Download complete 
Digest: sha256:678c6550cc43645e08669028bc177f50be4e7c5b8cca677067b1914d4afc7a03
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest
```
```bash
$ docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
Digest: sha256:8541484afbc9c8a5a8a99b379568ebbc957f658583ec9448fc43104229c03cf8
Status: Image is up to date for nginx:latest
docker.io/library/nginx:latest
```

### 3-4. 이미지 목록 확인
`docker images`: 로컬에 저장된 이미지 목록을 출력하는 명령어
```bash
$ docker images
                                                                      i Info →   U  In Use
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
hello-world:latest   7f4da0fc94bc       25.9kB         9.49kB        
ubuntu:latest        678c6550cc43        160MB         45.3MB
```

### 3-5. 컨테이너 실행/중지 및 목록 확인
`docker run`: 이미지를 컨테이너로 실행하는 명령어
```bash
$ docker run nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
5a4222b844e8: Download complete 
5a4222b844e8: Pull complete 
c0df8d325117: Pull complete 
d84ae7b21412: Pull complete 
3c55dc422a81: Pull complete 
26c307b5e35a: Pull complete 
f5de6e85ac74: Pull complete 
0f03cb4db0ef: Download complete 
92fcf0fc2ef2: Download complete 
Digest: sha256:8541484afbc9c8a5a8a99b379568ebbc957f658583ec9448fc43104229c03cf8
Status: Downloaded newer image for nginx:latest
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/08/10 06:40:33 [notice] 1#1: using the "epoll" event method
2026/08/10 06:40:33 [notice] 1#1: nginx/1.31.3
2026/08/10 06:40:33 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
2026/08/10 06:40:33 [notice] 1#1: OS: Linux 6.18.33.2-microsoft-standard-WSL2
2026/08/10 06:40:33 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2026/08/10 06:40:33 [notice] 1#1: start worker processes
2026/08/10 06:40:33 [notice] 1#1: start worker process 29
2026/08/10 06:40:33 [notice] 1#1: start worker process 30
2026/08/10 06:40:33 [notice] 1#1: start worker process 31
2026/08/10 06:40:33 [notice] 1#1: start worker process 32
2026/08/10 06:40:33 [notice] 1#1: start worker process 33
2026/08/10 06:40:33 [notice] 1#1: start worker process 34
2026/08/10 06:40:33 [notice] 1#1: start worker process 35
2026/08/10 06:40:33 [notice] 1#1: start worker process 36
2026/08/10 06:40:33 [notice] 1#1: start worker process 37
2026/08/10 06:40:33 [notice] 1#1: start worker process 38
2026/08/10 06:40:33 [notice] 1#1: start worker process 39
2026/08/10 06:40:33 [notice] 1#1: start worker process 40
^C2026/08/10 06:40:49 [notice] 1#1: signal 2 (SIGINT) received, exiting
2026/08/10 06:40:49 [notice] 29#29: exiting
2026/08/10 06:40:49 [notice] 31#31: exiting
2026/08/10 06:40:49 [notice] 33#33: exiting
2026/08/10 06:40:49 [notice] 32#32: exiting
2026/08/10 06:40:49 [notice] 29#29: exit
2026/08/10 06:40:49 [notice] 34#34: exiting
2026/08/10 06:40:49 [notice] 31#31: exit
2026/08/10 06:40:49 [notice] 38#38: exiting
2026/08/10 06:40:49 [notice] 39#39: exiting
2026/08/10 06:40:49 [notice] 34#34: exit
2026/08/10 06:40:49 [notice] 37#37: exiting
2026/08/10 06:40:49 [notice] 36#36: exiting
2026/08/10 06:40:49 [notice] 35#35: exiting
2026/08/10 06:40:49 [notice] 37#37: exit
2026/08/10 06:40:49 [notice] 39#39: exit
2026/08/10 06:40:49 [notice] 36#36: exit
2026/08/10 06:40:49 [notice] 30#30: exiting
2026/08/10 06:40:49 [notice] 33#33: exit
2026/08/10 06:40:49 [notice] 30#30: exit
2026/08/10 06:40:49 [notice] 38#38: exit
2026/08/10 06:40:49 [notice] 35#35: exit
2026/08/10 06:40:49 [notice] 32#32: exit
2026/08/10 06:40:49 [notice] 40#40: exiting
2026/08/10 06:40:49 [notice] 40#40: exit
2026/08/10 06:40:49 [notice] 1#1: signal 14 (SIGALRM) received
2026/08/10 06:40:49 [notice] 1#1: signal 17 (SIGCHLD) received from 39
2026/08/10 06:40:49 [notice] 1#1: worker process 39 exited with code 0
2026/08/10 06:40:49 [notice] 1#1: signal 29 (SIGIO) received
2026/08/10 06:40:49 [notice] 1#1: signal 17 (SIGCHLD) received from 38
2026/08/10 06:40:49 [notice] 1#1: worker process 37 exited with code 0
2026/08/10 06:40:49 [notice] 1#1: worker process 38 exited with code 0
2026/08/10 06:40:49 [notice] 1#1: signal 29 (SIGIO) received
2026/08/10 06:40:49 [notice] 1#1: signal 17 (SIGCHLD) received from 36
2026/08/10 06:40:49 [notice] 1#1: worker process 29 exited with code 0
2026/08/10 06:40:49 [notice] 1#1: worker process 36 exited with code 0
2026/08/10 06:40:49 [notice] 1#1: signal 29 (SIGIO) received
2026/08/10 06:40:49 [notice] 1#1: signal 17 (SIGCHLD) received from 29
2026/08/10 06:40:49 [notice] 1#1: signal 17 (SIGCHLD) received from 31
2026/08/10 06:40:49 [notice] 1#1: worker process 31 exited with code 0
2026/08/10 06:40:49 [notice] 1#1: signal 29 (SIGIO) received
2026/08/10 06:40:49 [notice] 1#1: signal 17 (SIGCHLD) received from 35
2026/08/10 06:40:49 [notice] 1#1: worker process 32 exited with code 0
2026/08/10 06:40:49 [notice] 1#1: worker process 35 exited with code 0
2026/08/10 06:40:49 [notice] 1#1: signal 29 (SIGIO) received
2026/08/10 06:40:49 [notice] 1#1: signal 17 (SIGCHLD) received from 33
2026/08/10 06:40:49 [notice] 1#1: worker process 33 exited with code 0
2026/08/10 06:40:49 [notice] 1#1: signal 29 (SIGIO) received
2026/08/10 06:40:49 [notice] 1#1: signal 17 (SIGCHLD) received from 40
2026/08/10 06:40:49 [notice] 1#1: worker process 40 exited with code 0
2026/08/10 06:40:49 [notice] 1#1: signal 29 (SIGIO) received
2026/08/10 06:40:49 [notice] 1#1: signal 17 (SIGCHLD) received from 34
2026/08/10 06:40:49 [notice] 1#1: worker process 34 exited with code 0
2026/08/10 06:40:49 [notice] 1#1: signal 29 (SIGIO) received
2026/08/10 06:40:49 [notice] 1#1: signal 17 (SIGCHLD) received from 30
2026/08/10 06:40:49 [notice] 1#1: worker process 30 exited with code 0
2026/08/10 06:40:49 [notice] 1#1: exit
```
`docker ps`: 현재 실행 중인 컨테이너 목록을 출력하는 명령어
```bash
$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
aa80ff79035c   nginx     "/docker-entrypoint.…"   15 minutes ago   Up 15 minutes   80/tcp    test-nginx
```
`docker stop 컨테이너`: 실행 중인 컨테이너를 중지하는 명령어
```bash
$ docker stop test-nginx
test-nginx
```
`docker ps -a`: 중지된 컨테이너까지 포함한 전체 컨테이너 목록을 출력하는 명령어
```bash
$ docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS                      PORTS     NAMES
a44205714d0b   ubuntu        "/bin/bash"              10 minutes ago   Exited (0) 10 minutes ago             hopeful_gauss
aa80ff79035c   nginx         "/docker-entrypoint.…"   18 minutes ago   Exited (0) 30 seconds ago             test-nginx
589d5d8b53c8   ubuntu        "/bin/bash"              24 minutes ago   Exited (0) 24 minutes ago             relaxed_moore
aeca63fc76ed   hello-world   "/hello"                 30 minutes ago   Exited (0) 30 minutes ago             interesting_napier
```

### 3-6. 컨테이너 로그 확인
`docker logs 컨테이너`: 컨테이너의 표준 출력·에러 로그를 확인하는 명령어
```bash
docker logs test-nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/08/10 05:44:41 [notice] 1#1: using the "epoll" event method
2026/08/10 05:44:41 [notice] 1#1: nginx/1.31.3
2026/08/10 05:44:41 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
2026/08/10 05:44:41 [notice] 1#1: OS: Linux 6.18.33.2-microsoft-standard-WSL2
2026/08/10 05:44:41 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2026/08/10 05:44:41 [notice] 1#1: start worker processes
2026/08/10 05:44:41 [notice] 1#1: start worker process 29
2026/08/10 05:44:41 [notice] 1#1: start worker process 30
2026/08/10 05:44:41 [notice] 1#1: start worker process 31
2026/08/10 05:44:41 [notice] 1#1: start worker process 32
2026/08/10 05:44:41 [notice] 1#1: start worker process 33
2026/08/10 05:44:41 [notice] 1#1: start worker process 34
2026/08/10 05:44:41 [notice] 1#1: start worker process 35
2026/08/10 05:44:41 [notice] 1#1: start worker process 36
2026/08/10 05:44:41 [notice] 1#1: start worker process 37
2026/08/10 05:44:41 [notice] 1#1: start worker process 38
2026/08/10 05:44:41 [notice] 1#1: start worker process 39
2026/08/10 05:44:41 [notice] 1#1: start worker process 40
2026/08/10 06:02:12 [notice] 1#1: signal 3 (SIGQUIT) received, shutting down
2026/08/10 06:02:12 [notice] 30#30: gracefully shutting down
2026/08/10 06:02:12 [notice] 32#32: gracefully shutting down
2026/08/10 06:02:12 [notice] 33#33: gracefully shutting down
2026/08/10 06:02:12 [notice] 36#36: gracefully shutting down
2026/08/10 06:02:12 [notice] 39#39: gracefully shutting down
2026/08/10 06:02:12 [notice] 37#37: gracefully shutting down
2026/08/10 06:02:12 [notice] 29#29: gracefully shutting down
2026/08/10 06:02:12 [notice] 29#29: exiting
2026/08/10 06:02:12 [notice] 33#33: exiting
2026/08/10 06:02:12 [notice] 37#37: exiting
2026/08/10 06:02:12 [notice] 39#39: exiting
2026/08/10 06:02:12 [notice] 34#34: gracefully shutting down
2026/08/10 06:02:12 [notice] 31#31: gracefully shutting down
2026/08/10 06:02:12 [notice] 39#39: exit
2026/08/10 06:02:12 [notice] 33#33: exit
2026/08/10 06:02:12 [notice] 38#38: gracefully shutting down
2026/08/10 06:02:12 [notice] 37#37: exit
2026/08/10 06:02:12 [notice] 35#35: gracefully shutting down
2026/08/10 06:02:12 [notice] 29#29: exit
2026/08/10 06:02:12 [notice] 34#34: exiting
2026/08/10 06:02:12 [notice] 35#35: exiting
2026/08/10 06:02:12 [notice] 31#31: exiting
2026/08/10 06:02:12 [notice] 36#36: exiting
2026/08/10 06:02:12 [notice] 38#38: exiting
2026/08/10 06:02:12 [notice] 32#32: exiting
2026/08/10 06:02:12 [notice] 35#35: exit
2026/08/10 06:02:12 [notice] 31#31: exit
2026/08/10 06:02:12 [notice] 34#34: exit
2026/08/10 06:02:12 [notice] 32#32: exit
2026/08/10 06:02:12 [notice] 36#36: exit
2026/08/10 06:02:12 [notice] 40#40: gracefully shutting down
2026/08/10 06:02:12 [notice] 40#40: exiting
2026/08/10 06:02:12 [notice] 40#40: exit
2026/08/10 06:02:12 [notice] 30#30: exiting
2026/08/10 06:02:12 [notice] 38#38: exit
2026/08/10 06:02:12 [notice] 30#30: exit
2026/08/10 06:02:12 [notice] 1#1: signal 17 (SIGCHLD) received from 29
2026/08/10 06:02:12 [notice] 1#1: worker process 29 exited with code 0
2026/08/10 06:02:12 [notice] 1#1: signal 29 (SIGIO) received
2026/08/10 06:02:12 [notice] 1#1: signal 17 (SIGCHLD) received from 32
2026/08/10 06:02:12 [notice] 1#1: worker process 31 exited with code 0
2026/08/10 06:02:12 [notice] 1#1: worker process 32 exited with code 0
2026/08/10 06:02:12 [notice] 1#1: worker process 35 exited with code 0
2026/08/10 06:02:12 [notice] 1#1: worker process 36 exited with code 0
2026/08/10 06:02:12 [notice] 1#1: worker process 37 exited with code 0
2026/08/10 06:02:12 [notice] 1#1: worker process 38 exited with code 0
2026/08/10 06:02:12 [notice] 1#1: worker process 39 exited with code 0
2026/08/10 06:02:12 [notice] 1#1: signal 29 (SIGIO) received
2026/08/10 06:02:12 [notice] 1#1: signal 17 (SIGCHLD) received from 30
2026/08/10 06:02:12 [notice] 1#1: worker process 30 exited with code 0
2026/08/10 06:02:12 [notice] 1#1: worker process 34 exited with code 0
2026/08/10 06:02:12 [notice] 1#1: signal 29 (SIGIO) received
2026/08/10 06:02:12 [notice] 1#1: signal 17 (SIGCHLD) received from 34
2026/08/10 06:02:12 [notice] 1#1: signal 17 (SIGCHLD) received from 40
2026/08/10 06:02:12 [notice] 1#1: worker process 40 exited with code 0
2026/08/10 06:02:12 [notice] 1#1: signal 29 (SIGIO) received
2026/08/10 06:02:12 [notice] 1#1: signal 17 (SIGCHLD) received from 33
2026/08/10 06:02:12 [notice] 1#1: worker process 33 exited with code 0
2026/08/10 06:02:12 [notice] 1#1: exit
```

### 3-7. 컨테이너 리소스 확인
`docker stats`: 실행 중인 컨테이너의 CPU·메모리·네트워크 등 리소스 사용량을 실시간으로 출력하는 명령어
```bash
$ docker stats --no-stream test-nginx
CONTAINER ID   NAME         CPU %     MEM USAGE / LIMIT   MEM %     NET I/O   BLOCK I/O   PIDS
aa80ff79035c   test-nginx   0.00%     0B / 0B             0.00%     0B / 0B   0B / 0B     0
```

### 3-8. 이미지/컨테이너 정리
`docker rm 컨테이너`: 중지된 컨테이너를 삭제하는 명령어
```bash
$ docker rm test-nginx
test-nginx
```
`docker rmi 이미지`: 더 이상 사용하지 않는 이미지를 삭제하는 명령어
```bash
$ docker rmi nginx
Untagged: nginx:latest
Deleted: sha256:8541484afbc9c8a5a8a99b379568ebbc957f658583ec9448fc43104229c03cf8
```
> 이미지를 참조하는 컨테이너가 남아있으면 `docker rmi`가 실패하므로, 먼저 `docker rm`으로 관련 컨테이너를 정리한 뒤 이미지를 삭제함

### 3-9. 컨테이너를 이미지로 저장 (docker commit)
`docker commit 컨테이너 이미지이름[:태그]`: 컨테이너의 현재 상태(파일 변경 등)를 새 이미지로 저장하는 명령어
```bash
$ docker run -it ubuntu bash
root@a1c2e4f6b8d0:/# apt-get update
root@a1c2e4f6b8d0:/# exit
$ docker commit a1c2e4f6b8d0 my-ubuntu:latest
sha256:...
$ docker images
REPOSITORY   TAG      IMAGE ID       CREATED         SIZE
my-ubuntu    latest   xxxxxxxxxxxx   5 seconds ago   ...MB
ubuntu       latest   678c6550cc43   ...             160MB
```
> `docker commit` 뒤에는 컨테이너 ID/이름과, 붙이고 싶은 `저장소이름:태그`를 순서대로 지정함 (태그를 생략하면 `latest`가 기본값). 이미지는 불변이지만, 컨테이너 안에서 변경한 내용을 `commit`으로 캡처해야 새 이미지에 반영됨
