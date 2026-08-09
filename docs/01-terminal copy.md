# 01 터미널 기본명령어 
### 1-1. 파일의 현재 위치
`pwd`: 현재 내가 작업 중인 폴더(디렉토리)의 전체 경로를 보는 명령어
```bash
% pwd
/Users/hauteville18620603/Desktop/codyssey-e1
```
### 1-2. 파일 생성
`touch`: 파일의 접근 시간과 수정 시간을 현재 시각으로 갱신하거나, (파일이 존재하지 않을 경우) 파일을 새로 생성하는 명령어

`ls`: 현재 디렉토리에 포함된 파일 및 하위 디렉토리의 목록을 출력하는 명령어

`ls -al`: 숨김 파일(.으로 시작하는 파일)을 포함한 모든 항목을 `-a` 권한·소유자·크기·수정 시각 등의 상세 정보와 함께 `-l` 출력하는 명령어
```bash
% touch file
% ls -al file
-rw-r--r--  1 hauteville18620603  hauteville18620603  0  8  8 13:26 file
```

#### 1-3. 폴더 생성
```bash
% mkdir folder
% ls -ald folder
drwxr-xr-x  2 hauteville18620603  hauteville18620603  64  8  8 13:26 folder
```
#### 1-4. 파일 & 폴더 이동
![스크린샷](../screenshots/file_folder_move.png)


### 파일 & 폴더 삭제
```bash
% rm 파일
% ls -al
total 0
drwxr-xr-x  2 hauteville18620603  hauteville18620603   64  8  2 16:14 .
drwxr-xr-x  5 hauteville18620603  hauteville18620603  160  8  2 16:11 ..

% rm -r 폴더
% ls -al
total 8
drwxr-xr-x   4 hauteville18620603  hauteville18620603  128  8  2 16:18 .
drwx------+  8 hauteville18620603  hauteville18620603  256  8  2 16:02 ..
drwxr-xr-x  12 hauteville18620603  hauteville18620603  384  8  2 16:02 .git
-rw-r--r--   1 hauteville18620603  hauteville18620603  640  8  2 16:02 README.md
```

