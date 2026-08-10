# 03 Docker 설치 및 기본 운영

## 개념 요약

| 구분 | 의미 | 비유 |
| --- | --- | --- |
| 이미지(Image) | 컨테이너 실행에 필요한 파일·설정을 담은 불변 템플릿(빌드 결과물) | 설계도 |
| 컨테이너(Container) | 이미지를 실행한 인스턴스. 실행 중 상태가 변할 수 있으나 삭제하면 변경 내용은 사라짐 | 설계도로 지은 실제 건물 |

`docker build`로 이미지를 만들고, `docker run`으로 이미지를 컨테이너로 실행하며, 컨테이너 안에서의 변경은 `docker commit` 없이는 이미지에 반영되지 않음

### 3-1. Docker 버전 확인
`docker --version`: 설치된 Docker 클라이언트 버전을 출력하는 명령어
```bash
(여기에 docker --version 실행 결과를 붙여넣어 주세요)
```

### 3-2. Docker 데몬 동작 확인
`docker info`: Docker 데몬(엔진)이 정상적으로 동작 중인지, 컨테이너·이미지 개수 등 시스템 정보를 출력하는 명령어
```bash
(여기에 docker info 실행 결과를 붙여넣어 주세요)
```

### 3-3. 이미지 다운로드 및 목록 확인
`docker pull 이미지명`: 레지스트리(Docker Hub 등)에서 이미지를 내려받는 명령어
- `docker images`: 로컬에 저장된 이미지 목록을 출력하는 명령어
```bash
(여기에 docker pull, docker images 실행 결과를 붙여넣어 주세요)
```

### 3-4. 컨테이너 실행/중지 및 목록 확인
`docker ps`: 현재 실행 중인 컨테이너 목록을 출력하는 명령어
- `docker ps -a`: 중지된 컨테이너까지 포함한 전체 컨테이너 목록을 출력하는 명령어
- `docker stop 컨테이너`: 실행 중인 컨테이너를 중지하는 명령어
```bash
(여기에 docker ps, docker ps -a, docker stop 실행 결과를 붙여넣어 주세요)
```
> hello-world/ubuntu 컨테이너 실행 실습은 [06-list.md](06-list.md)에서 별도로 다룸

### 3-5. 컨테이너 로그 확인
`docker logs 컨테이너`: 컨테이너의 표준 출력·에러 로그를 확인하는 명령어
```bash
(여기에 docker logs 실행 결과를 붙여넣어 주세요)
```

### 3-6. 컨테이너 리소스 확인
`docker stats`: 실행 중인 컨테이너의 CPU·메모리·네트워크 등 리소스 사용량을 실시간으로 출력하는 명령어
```bash
(여기에 docker stats 실행 결과를 붙여넣어 주세요)
```

### 3-7. 이미지/컨테이너 정리
`docker rm 컨테이너`: 중지된 컨테이너를 삭제하는 명령어
- `docker rmi 이미지`: 더 이상 사용하지 않는 이미지를 삭제하는 명령어
```bash
(여기에 docker rm, docker rmi 실행 결과를 붙여넣어 주세요)
```
