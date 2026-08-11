# 04 보충 노트: 백그라운드 실행과 attach/exec

[← 04 컨테이너 실행/관리로 돌아가기](04-container.md)

## 포그라운드 vs 백그라운드

- **포그라운드(기본값)**: `docker run ubuntu bash`처럼 실행하면 터미널이 그 컨테이너에 붙잡힘. `-it` 없이 실행하면 입출력이 연결 안 돼 있어서 컨테이너의 메인 프로세스가 순식간에 끝나버리고, 그러면 컨테이너도 같이 종료됨 (4-2에서 다룬 내용).
- **백그라운드(-d, detached)**: `docker run -d 이미지`처럼 `-d` 옵션을 주면 컨테이너를 뒤에서 띄워놓고 터미널은 바로 프롬프트로 돌아옴. 컨테이너 ID만 출력되고 끝.

## 왜 attach/exec 실습에 -d가 필요한가

`docker attach`나 `docker exec`는 "현재 실행 중인" 컨테이너에 연결하는 명령어다. 그런데 지금까지 문서의 예시들(`docker run -it ubuntu bash` → `exit`)은 실행하자마자 상호작용하고 바로 종료해버리는 흐름이라, attach/exec를 시도할 시점엔 이미 컨테이너가 죽어있는 경우가 많다.

또, 포그라운드로 `-it` 실행한 터미널은 이미 그 세션을 붙잡고 있어서, 같은 컨테이너에 attach/exec를 "다른 터미널에서" 시도해보려면 애초에 백그라운드로 띄워야 한다.

## 실습용으로 컨테이너를 살려두는 방법

```bash
$ docker run -dit --name test-box ubuntu bash
```

- `-d`: 백그라운드 실행
- `-it`: tty(가상 터미널) 할당 + 표준입력 연결 유지 → bash가 입력을 기다리며 죽지 않고 계속 살아있음
- `-dit`는 사실 `-d -i -t`를 합친 것

이제 `docker ps`로 확인하면 `test-box`가 계속 `Up` 상태로 떠 있다.

## attach vs exec 비교 실습

**exec (별도 프로세스로 진입, 나가도 컨테이너 안 죽음)**
```bash
$ docker exec -it test-box bash
root@...:/# echo hi
hi
root@...:/# exit
$ docker ps   # test-box가 여전히 Up 상태
```

**attach (메인 프로세스 자체에 연결, 나가면 메인 프로세스=컨테이너도 종료될 수 있음)**
```bash
$ docker attach test-box
root@...:/# exit
$ docker ps   # test-box가 사라짐 (Exited)
```

> attach를 먼저 하면 컨테이너가 죽어서 그 뒤 exec를 실습할 대상이 없어지니, exec를 먼저 해보고 마지막에 attach로 종료시키는 순서를 추천한다.
