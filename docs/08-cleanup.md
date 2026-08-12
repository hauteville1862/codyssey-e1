# 08 커스텀 이미지 삭제 및 정리

## 명령어 요약

| 명령어 | 설명 |
| --- | --- |
| `docker ps -a` | 중지된 컨테이너까지 포함해 정리 대상 컨테이너를 확인 |
| `docker stop 컨테이너` | 실행 중인 컨테이너를 먼저 중지 |
| `docker rm 컨테이너` | 이미지를 참조하는 컨테이너를 삭제 |
| `docker rmi 이미지이름[:태그]` | 빌드한 커스텀 이미지를 삭제 |
| `docker images` | 이미지가 목록에서 실제로 사라졌는지 최종 확인 |

> `codyssey-e1-web-container`(8080), `codyssey-e1-web-container-2`(8081) 두 컨테이너가 모두 `Up` 상태로 남아있으므로, 이 문서에서 순서대로 정리함

### 8-1. 정리 대상 컨테이너 확인
`docker ps -a`: 중지된 컨테이너까지 포함한 전체 목록을 출력하는 명령어. 이미지를 삭제하려면 그 이미지를 참조하는 컨테이너가 먼저 없어야 하므로, 삭제 전에 어떤 컨테이너가 남아있는지 확인
```bash
$ docker ps -a
CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS                      PORTS                                     NAMES
59f57aed6a25   ubuntu            "bash"                   22 minutes ago   Exited (0) 21 minutes ago                                             recursing_franklin
bf8bfab18437   codyssey-e1-web   "/docker-entrypoint.…"   2 hours ago      Up 2 hours                  0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   codyssey-e1-web-container-2
93b2ca033581   codyssey-e1-web   "/docker-entrypoint.…"   7 hours ago      Up 7 hours                  0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   codyssey-e1-web-container
c098e8b5472d   ubuntu            "bash"                   19 hours ago     Exited (0) 18 hours ago                                               my-container
```

### 8-2. 컨테이너 중지 및 삭제
`docker stop 컨테이너`: 실행 중인(`Up`) 컨테이너를 중지하는 명령어.
> `docker rm`은 기본적으로 실행 중인 컨테이너는 지우지 못하므로 먼저 중지 필요
> `recursing_franklin`, `my-container`(둘 다 ubuntu, 03·04번 실습에서 남은 컨테이너)는 이 커스텀 이미지와 무관하므로 이번 정리 대상에서 제외 — `codyssey-e1-web` 이미지를 참조하는 두 컨테이너만 정리함
```bash
$ docker stop codyssey-e1-web-container codyssey-e1-web-container-2
codyssey-e1-web-container
codyssey-e1-web-container-2
```
`docker rm 컨테이너`: 중지된 컨테이너를 삭제하는 명령어
```bash
$ docker rm codyssey-e1-web-container codyssey-e1-web-container-2
codyssey-e1-web-container
codyssey-e1-web-container-2
```
> 삭제 후 `docker ps -a`로 두 컨테이너가 목록에서 사라졌는지 재확인 권장
```bash
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS                      PORTS     NAMES
59f57aed6a25   ubuntu    "bash"    23 minutes ago   Exited (0) 22 minutes ago             recursing_franklin
c098e8b5472d   ubuntu    "bash"    19 hours ago     Exited (0) 18 hours ago               my-container
```

### 8-3. 커스텀 이미지 삭제
`docker rmi 이미지이름[:태그]`: 더 이상 사용하지 않는 이미지를 삭제하는 명령어
- 해당 이미지를 참조하는 컨테이너(중지 상태 포함)가 남아있으면 삭제가 실패하므로, 8-2에서 관련 컨테이너를 먼저 지운 뒤 진행
```bash
$ docker rmi codyssey-e1-web
Untagged: codyssey-e1-web:latest
Deleted: sha256:609140e712e6aa36cd56f1f832e6f75475fa20c9884e3a2bf1624744e2e3f392
```
- 삭제 후 `docker images`로 목록에서 실제로 사라졌는지 확인
```bash
$ docker images
                                                                                     i Info →   U  In Use
IMAGE           ID             DISK USAGE   CONTENT SIZE   EXTRA
ubuntu:latest   678c6550cc43        160MB         45.3MB    U   
```
