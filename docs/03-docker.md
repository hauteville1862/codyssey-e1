# 03 Docker 설치 및 기본 운영

## 개념 요약

| 구분 | 의미 | 비유 |
| --- | --- | --- |
| Dockerfile | 이미지를 어떻게 만들지 순서대로 적어둔 빌드 스크립트(텍스트 파일) | 설계 지침서(레시피) |
| 이미지(Image) | 컨테이너 실행에 필요한 파일·설정을 담은 불변 템플릿(빌드 결과물) | 설계도 |
| 컨테이너(Container) | 이미지를 실행한 인스턴스. 실행 중 상태가 변할 수 있으나 삭제하면 변경 내용은 사라짐 | 설계도로 지은 실제 건물 |

Dockerfile을 `docker build`로 빌드하면 이미지가 만들어지고, 이미지를 `docker run`으로 실행하면 컨테이너가 되며, 컨테이너 안에서의 변경은 `docker commit` 없이는 이미지에 반영되지 않음

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
`docker run -d --name 이름 이미지 명령`: 이미지를 컨테이너로 실행하는 명령어(`-d`는 백그라운드 실행)
```bash
(여기에 docker run, docker ps, docker stop, docker ps -a 실행 결과를 붙여넣어 주세요)
```
`docker ps`: 현재 실행 중인 컨테이너 목록을 출력하는 명령어
```bash
(여기에 docker run, docker ps, docker stop, docker ps -a 실행 결과를 붙여넣어 주세요)
```
> hello-world 이미지는 메시지 한 줄을 출력하고 바로 종료되는 프로그램이라, 실행 직후 `docker ps`를 확인하면 컨테이너가 이미 종료되어 빈 목록이 나오는 것이 정상임(실행 중인 컨테이너만 보여주는 `docker ps`의 특성). 종료된 컨테이너까지 보려면 `docker ps -a` 필요

`docker stop 컨테이너`: 실행 중인 컨테이너를 중지하는 명령어
```bash
(여기에 docker run, docker ps, docker stop, docker ps -a 실행 결과를 붙여넣어 주세요)
```
`docker ps -a`: 중지된 컨테이너까지 포함한 전체 컨테이너 목록을 출력하는 명령어
```bash
(여기에 docker run, docker ps, docker stop, docker ps -a 실행 결과를 붙여넣어 주세요)
```

### 3-6. 컨테이너 로그 확인
`docker logs 컨테이너`: 컨테이너의 표준 출력·에러 로그를 확인하는 명령어
```bash
(여기에 docker logs 실행 결과를 붙여넣어 주세요)
```

### 3-7. 컨테이너 리소스 확인
`docker stats`: 실행 중인 컨테이너의 CPU·메모리·네트워크 등 리소스 사용량을 실시간으로 출력하는 명령어
```bash
(여기에 docker stats 실행 결과를 붙여넣어 주세요)
```

### 3-8. 이미지/컨테이너 정리
`docker rm 컨테이너`: 중지된 컨테이너를 삭제하는 명령어
- `docker rmi 이미지`: 더 이상 사용하지 않는 이미지를 삭제하는 명령어
```bash
(여기에 docker rm, docker rmi 실행 결과를 붙여넣어 주세요)
```
