- hello-world
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
> 실행 직후 `docker ps`를 확인하면 컨테이너가 이미 종료되어 빈 목록이 나오는 것이 정상
- ubuntu
```bash
$ docker run ubuntu
```
> ubuntu 이미지의 기본 명령은 `bash` (docker run ubuntu는 사실상 docker run ubuntu bash와 같은 뜻)
> `-it` 없이 실행하면 터미널이 연결되지 않아 bash가 바로 종료되고, 컨테이너도 함께 종료됨