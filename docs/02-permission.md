# 02 파일 및 디렉토리 권한

## 개념 요약

```
-  rwx  r-x  r-x
│   │    │    │
│   │    │    └─ 기타(other) 권한
│   │    └────── 그룹(group) 권한
│   └────────── 소유자(owner) 권한
└──────────── 파일 종류(- 파일, d 디렉토리)
```

| 기호 | 의미 | 값 |
| --- | --- | --- |
| `r` | 읽기(read) | 4 |
| `w` | 쓰기(write) | 2 |
| `x` | 실행/진입(execute) | 1 |

`chmod 숫자 대상` 또는 `chmod u/g/o+rwx 대상`으로 권한을 변경함 (예: `chmod 755 파일`)

| 표기(기호) | 표기(숫자) | 의미 |
| --- | --- | --- |
| `rwxr-xr-x` | `755` | 소유자는 읽기·쓰기·실행, 그룹·기타는 읽기·실행만 |
| `rw-r--r--` | `644` | 소유자는 읽기·쓰기, 그룹·기타는 읽기만 |
| `r--------` | `400` | 소유자만 읽기 가능(쓰기·실행 불가) |

> `w`가 없으면 소유자도 내용을 수정할 수 없고, `x`는 파일에서는 "실행 권한", 디렉토리에서는 "진입(통과) 권한"을 의미함

### 2-1. 권한 확인
`ls -al`: 파일과 디렉토리의 권한을 `-rwxr-xr-x` 형식(맨 앞은 파일 종류, 이후 3자리씩 소유자·그룹·기타)으로 출력하는 명령어
- `r=4`, `w=2`, `x=1`을 더해 `rwx=7`, `r-x=5`, `r--=4`처럼 숫자(8진수)로도 표현할 수 있음
```bash
$ mkdir folder
$ ls -ld folder
drwxr-xr-x 1 Yuhyun Lim 197121 0 Aug 10 12:59 folder
```
`d`(디렉토리) + `rwx`(소유자) + `r-x`(그룹) + `r-x`(기타) → `755`

### 2-2. 파일 실행 권한 변경
`chmod 권한 파일`: 파일의 권한을 숫자(8진수) 또는 기호(`u`/`g`/`o` + `rwx`)로 변경하는 명령어
- 파일에서 `x`(실행 권한)가 없으면 `./파일명`으로 직접 실행할 수 없고, `x`를 추가하면 실행 가능한 상태로 바뀜 (예: 644 → 755)
```bash
$ chmod 644 folder/test.sh
$ ls -l folder/test.sh
-rw-r--r-- 1 Yuhyun Lim 197121 44 Aug 10 13:08 test.sh
$ ./folder/test.sh
hello from folder script
$ chmod 755 folder/test.sh
$ ls -l folder/test.sh
-rwxr-xr-x 1 Yuhyun Lim 197121 44 Aug 10 13:08 test.sh
```
> 환경B(Windows+git bash)는 NTFS라 실행 비트가 실제로 강제되지 않아 644 상태에서도 스크립트가 실행됨. 리눅스/macOS(환경A)에서는 `Permission denied`로 거부됨

### 2-3. 파일 쓰기 권한 테스트
`chmod 400 파일`: 소유자에게 읽기 권한만 남기고 쓰기·실행 권한을 모두 제거하는 명령어
- `w`(쓰기 권한)가 없으면 파일의 소유자라도 내용을 수정하거나 저장할 수 없음
```bash
$ chmod 400 folder/test.sh
$ ls -l folder/test.sh
-r-xr-xr-x 1 Yuhyun Lim 197121 44 Aug 10 13:08 test.sh
$ echo "extra line" >> folder/test.sh
bash: folder/test.sh: Permission denied
$ chmod 755 folder/test.sh
```

### 2-4. 디렉토리 진입 권한
디렉토리에서 `x`는 "진입(통과) 권한"을 의미함
- `r`만 있고 `x`가 없으면 `ls`로 파일 목록은 보이지만, `cd`나 `cat`으로 내부 파일에는 접근할 수 없음
```bash
$ chmod 644 folder
$ ls -ld folder
drwxr-xr-x 1 Yuhyun Lim 197121 0 Aug 10 13:08 folder
$ cat folder/test.sh
#!/bin/bash
echo "hello from folder script"
$ chmod 755 folder
```
> 환경B(Windows+git bash)는 디렉토리 진입 비트도 강제되지 않아 644 상태에서도 `cat`이 성공함. 실제 `Permission denied`는 환경A(macOS/Linux)에서 확인 가능
