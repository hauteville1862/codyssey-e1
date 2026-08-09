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

### 1-3. 폴더 생성
`mkdir`: 새로운 디렉토리(폴더)를 생성하는 명령어

`ls -ald`: 디렉토리 자체의 정보만 `-d` 상세 정보와 함께 `-l` 출력하는 명령어
```bash
% mkdir folder
% ls -ald folder
drwxr-xr-x  2 hauteville18620603  hauteville18620603  64  8  8 13:26 folder
```

### 1-4. 디렉토리 이동
`cd 경로`: 현재 작업 중인 디렉토리를 지정한 경로로 이동(변경)하는 명령어

`cd ..`: 한 단계 상위 디렉토리로 이동하는 명령어
```bash
(여기에 cd 실행 결과를 붙여넣어 주세요)
```

### 1-5. 파일 & 폴더 이동 / 이름 변경
`mv`: 파일이나 폴더를 다른 위치로 이동시키거나, 같은 위치에서 이름을 변경하는 명령어
```bash
(여기에 mv 실행 결과를 붙여넣어 주세요)
```
![스크린샷](../screenshots/file_folder_move.png)

### 1-6. 파일 & 폴더 복사
`cp`: 파일을 복사하는 명령어

`cp -r`: 폴더(디렉토리)를 하위 내용까지 재귀적으로 `-r` 복사하는 명령어
```bash
$ cp file file_copy
$ ls -al file file_copy
-rw-r--r-- 1 Yuhyun Lim 197121 0 Aug  9 22:12 file
-rw-r--r-- 1 Yuhyun Lim 197121 0 Aug  9 22:12 file_copy

$ cp -r folder folder_copy
$ ls -ald folder folder_copy
drwxr-xr-x 1 Yuhyun Lim 197121 0 Aug  9 22:12 folder
drwxr-xr-x 1 Yuhyun Lim 197121 0 Aug  9 22:12 folder_copy
```

### 1-7. 파일 & 폴더 삭제
`rm`: 파일을 삭제하는 명령어

`rm -r`: 폴더(디렉토리)와 그 안의 내용을 모두 삭제하는 명령어

`rmdir`: 비어 있는 폴더만 삭제하는 명령어
```bash
% rm file
% ls -al
total 0
drwxr-xr-x  2 hauteville18620603  hauteville18620603   64  8  2 16:14 .
drwxr-xr-x  5 hauteville18620603  hauteville18620603  160  8  2 16:11 ..

% rm -r folder
% ls -al
total 8
drwxr-xr-x   4 hauteville18620603  hauteville18620603  128  8  2 16:18 .
drwx------+  8 hauteville18620603  hauteville18620603  256  8  2 16:02 ..
drwxr-xr-x  12 hauteville18620603  hauteville18620603  384  8  2 16:02 .git
-rw-r--r--   1 hauteville18620603  hauteville18620603  640  8  2 16:02 README.md
```

(여기에 rmdir 실행 결과를 붙여넣어 주세요)
