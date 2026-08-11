# 04 컨테이너 실행/관리

## 명령어 요약

| 명령어 | 설명 |
| --- | --- |
| `docker run 이미지` | 이미지를 컨테이너로 실행 |
| `docker run -it 이미지 bash` | 터미널을 연결한 상태로 컨테이너에 진입해 대화형 셸 실행 |
| `docker commit 컨테이너 이미지이름[:태그]` | 컨테이너의 현재 상태를 이름을 지정해 새 이미지로 저장 |
| `docker attach 컨테이너` | 실행 중인 컨테이너의 메인 프로세스(PID 1)에 연결 |
| `docker exec -it 컨테이너 명령` | 실행 중인 컨테이너 안에서 별도의 새 프로세스 실행 |

### 4-1. 컨테이너 실행 (hello-world)
`docker run 이미지`: 이미지를 컨테이너로 실행하는 명령어
```bash
$ docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```
> hello-world 이미지는 메시지 한 줄을 출력하고 바로 종료되는 프로그램

### 4-2. 컨테이너 실행 (ubuntu)
`docker run 이미지`: 이미지를 컨테이너로 실행하는 명령어
```bash
$ docker run ubuntu
```
> ubuntu 이미지의 기본 명령은 `bash` (`docker run ubuntu`는 사실상 `docker run ubuntu bash`와 같은 뜻)
> `-it` 없이 실행하면 터미널이 연결되지 않아 bash가 바로 종료되고, 컨테이너도 함께 종료됨

### 4-3. 컨테이너 내부 진입 및 명령 실행
`docker run -it 이미지 bash`: 터미널을 연결한 상태로 컨테이너에 진입해 대화형 셸을 실행하는 명령어
```bash
$ docker run -it ubuntu bash
root@969bb9ddff00:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@969bb9ddff00:/# echo "Hello"
Hello
root@969bb9ddff00:/# exit
exit
```

### 4-4. 컨테이너를 이미지로 저장할 때 이름 지정
`docker commit 컨테이너 이미지이름[:태그]`: 컨테이너의 현재 상태(파일 변경 등)를 새 이미지로 저장하면서 이름을 지정하는 명령어
```bash
$ docker run -it ubuntu bash
root@969bb9ddff00:/# apt-get update
root@969bb9ddff00:/# exit
$ docker commit 969bb9ddff00 my-ubuntu:latest
sha256:...
$ docker images
REPOSITORY   TAG      IMAGE ID       CREATED         SIZE
my-ubuntu    latest   xxxxxxxxxxxx   5 seconds ago   ...MB
ubuntu       latest   678c6550cc43   ...             160MB
```
> `docker run --name`은 컨테이너의 이름을 지정하는 것이고, 이미지 자체에 이름을 붙이려면 `docker commit`(기존 컨테이너 기반) 또는 `docker build -t`(Dockerfile 기반)를 사용해야 함
> `docker commit` 뒤에는 컨테이너 ID/이름과, 붙이고 싶은 `저장소이름:태그`를 순서대로 지정함 (태그를 생략하면 `latest`가 기본값)

### 4-5. 컨테이너 종료/유지 차이 (attach / exec)
`docker attach 컨테이너`: 실행 중인 컨테이너의 메인 프로세스(PID 1)에 그대로 연결하는 명령어. 여기서 나가면(exit) 메인 프로세스가 끝나 컨테이너도 함께 종료될 수 있음
- `docker exec -it 컨테이너 명령`: 실행 중인 컨테이너 안에서 별도의 새 프로세스를 실행하는 명령어. 나가도 메인 프로세스는 그대로 남아있어 컨테이너가 계속 유지됨
```bash
(여기에 attach와 exec 각각 진입/종료 시 컨테이너 상태 비교 결과를 붙여넣어 주세요)
```
