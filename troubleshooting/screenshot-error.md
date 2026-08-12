## 문제 1 - 스크린샷을 첨부했을 때 이미지가 제대로 보이지 않는 오류 해결

### 1-1 문제
스크린샷 파일을 만들고 이미지를 docs 내 문서에 첨부했을 때, 아래와 같이 이미지가 보이지 않는 오류가 발생함.
```bash
![스크린샷](img/파일&폴더_이동.png)
```
![스크린샷](../img/screenshot-error1.png)

### 1-2 가설
#### 가설1
> 상대경로가 아닌 절대경로로 경로를 썼기 때문에 오류가 난 건가?

➡ img 폴더에서 우클릭 후 '상대 경로 복사'를 사용했지만 해결되지 않음.
#### 가설2
> 파일명에 특수문자나 한글이 들어가 있기 때문인가?

➡ 파일명을 알파벳만으로 바꾸어 보았지만 해결되지 않음.
#### 가설3
> 스크린샷 이미지와 첨부 파일 모두 폴더 안에 들어가 있기 때문에 폴더 밖에 있을 때와는 경로를 다르게 써야 하나?

➡ 확인
### 1-3 확인
파일 경로 앞에 상위 디렉토리로 이동하는 .. 을 넣어 상대 경로를 수정함.
```bash
![스크린샷](../img/file_folder_move.png)
```

### 1-4 해결 
![스크린샷](../img/file_folder_move.png)


<br>

## 문제 2 - 컨테이너 내부에서 docker 명령 실행 시 "command not found" 오류

### 2-1 문제
`docker commit`으로 컨테이너를 이미지로 저장하는 실습([03 Docker 설치 및 기본 운영 - 3-9](../docs/03-docker.md#3-9-컨테이너를-이미지로-저장-docker-commit)) 중, `docker run -it ubuntu bash`로 컨테이너 안에 들어간 상태에서 `docker commit`과 `docker ps`를 그대로 입력했더니 아래처럼 실패함.
```bash
root@59f57aed6a25:/# docker commit
bash: docker: command not found
root@59f57aed6a25:/# docker ps
bash: docker: command not found
```

### 2-2 가설
#### 가설1
> PATH 설정이 안 되어 있어서 `docker` 실행 파일을 못 찾는 건가?

➡ `which docker`로 확인해보니 컨테이너 안에는 `docker` 실행 파일 자체가 존재하지 않음. PATH 문제가 아님.

#### 가설2
> 컨테이너는 호스트와 분리된 별도의 파일시스템이라, 호스트에 설치된 Docker Desktop(=docker CLI)이 컨테이너 내부에는 애초에 설치되어 있지 않은 게 아닐까?

➡ 확인

### 2-3 확인
`exit`으로 컨테이너를 빠져나와 호스트 프롬프트(`$`)로 돌아온 뒤 같은 명령을 다시 입력함.
```bash
root@59f57aed6a25:/# exit
exit
$ docker commit 59f57aed6a25
sha256:7a15949a5cea04ae8c523917eb9b9e714827a6ddbeae548fb3feac5adc58bf15
```
호스트에서는 정상적으로 동작함 → 가설2가 맞음. `docker` 명령은 컨테이너 안에 설치되는 프로그램이 아니라, 컨테이너를 관리하는 호스트 쪽 도구라서 컨테이너 내부에서는 애초에 실행할 수 없는 게 정상.

### 2-4 해결
컨테이너 내부에서는 실습에 필요한 실제 변경 작업(예: `apt-get update`)만 수행하고, `docker commit`/`docker ps`/`docker build`처럼 이미지·컨테이너 자체를 다루는 명령은 반드시 컨테이너를 빠져나와 호스트에서 실행하는 순서로 정리함.