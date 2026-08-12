# 04 컨테이너 실행/관리

## 명령어 요약

| 명령어 | 설명 |
| --- | --- |
| `docker run 이미지` | 이미지를 컨테이너로 실행 |
| `docker run -it 이미지 bash` | 터미널을 연결한 상태로 컨테이너에 진입해 대화형 셸 실행 |
| `docker run --name 이름 이미지` | 컨테이너에 이름 지정 (이미지 이름 지정과는 별개) |
| `docker run -d 이미지` | 컨테이너를 백그라운드(detached)로 실행하고 터미널 제어권을 바로 반환 |
| `docker attach 컨테이너` | 실행 중인 컨테이너의 메인 프로세스(PID 1)에 연결 |
| `docker exec -it 컨테이너 명령` | 실행 중인 컨테이너 안에서 별도의 새 프로세스 실행 |
| `docker commit 컨테이너 이미지이름[:태그]` | 컨테이너의 현재 상태를 이름을 지정해 새 이미지로 저장 |

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
> hello-world 이미지는 안내 메시지를 출력하고 바로 종료되는 프로그램

### 4-2. 컨테이너 실행 (ubuntu)
`docker run 이미지`: 이미지를 컨테이너로 실행하는 명령어
```bash
$ docker run ubuntu
```
> ubuntu 이미지의 기본 명령은 `bash` (`docker run ubuntu`는 사실상 `docker run ubuntu bash`와 같은 뜻)

> `-it` 없이 실행하면 터미널이 연결되지 않아 bash가 바로 종료되고, 컨테이너도 함께 종료됨

### 4-3. 컨테이너 내부 진입 및 명령 실행
`docker run -it 이미지 bash`: 터미널을 연결한 상태로 컨테이너에 진입해 대화형 셸을 실행하는 명령어.
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
`docker run -it --name 이름 이미지 bash`: 컨테이너에 이름을 지정할 수 있음
```bash
$ docker run -it --name my-container ubuntu bash
root@39f1021873e3:/# exit    
exit
```
> 지정하지 않으면 Docker가 무작위 이름을 자동으로 붙임

### 4-4. 컨테이너 백그라운드 실행
`docker run -d 이미지`: 컨테이너를 백그라운드(detached)로 실행하고 터미널 제어권은 바로 돌려주는 명령어.
```bash
$ docker run -dit --name my-container ubuntu bash
da29ea083a34ee0ef7354f0a296b283d5ca89108bcc04a9fa304ce3c5b20ad2a
```
> attach/exec를 실습하려면 컨테이너가 계속 살아있어야 함.

> 백그라운드 실행과 attach/exec의 관계에 대한 보충 설명은 [00 개념 노트](00-concepts.md#백그라운드-실행과-attachexec) 참고

### 4-5. 컨테이너 종료/유지 차이 (attach / exec)
`docker exec -it 컨테이너 명령`: 실행 중인 컨테이너 안에서 별도의 새 프로세스를 실행하는 명령어.
```bash
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS          PORTS     NAMES
c098e8b5472d   ubuntu    "bash"    47 minutes ago   Up 47 minutes             my-container
$ docker exec -it my-container bash
root@c098e8b5472d:/# exit
exit
```
> 나가도 메인 프로세스는 그대로 남아있어 컨테이너가 계속 유지됨
`docker attach 컨테이너`: 실행 중인 컨테이너의 메인 프로세스(PID 1)에 그대로 연결하는 명령어.
```bash
$ docker attach my-container
root@c098e8b5472d:/# exit
exit
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                      PORTS     NAMES
c098e8b5472d   ubuntu    "bash"        51 minutes ago   Exited (0) 19 seconds ago             my-container
2d2ff1dc3ccb   ubuntu    "/bin/bash"   2 hours ago      Exited (0) 2 hours ago                awesome_babbage
```
> 여기서 나가면(exit) 메인 프로세스가 끝나 컨테이너도 함께 종료될 수 있음
