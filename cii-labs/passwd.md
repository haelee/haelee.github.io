## /etc/passwd 파일 소유자 및 권한 설정

왜 `root` 외의 일반 사용자가 `/etc/passwd` 파일을 수정할 수 없도록 설정(접근 권한이 `644` 이하)해야 하는지 알아봅시다.

### 실습 1 : UID 변경으로 루트 되기
1. `sudo` 명령을 사용할 수 없는 일반 사용자 `aaa` 생성
```
cju@wooam:~$ sudo adduser aaa
```

2. 누구나 `/etc/passwd` 파일을 수정할 수 있도록 변경
```
cju@wooam:~$ sudo chmod o+w /etc/passwd
```

3. 사용자 `aaa`로 전환
```
cju@wooam:~$ su aaa
```

4. `vi` (또는 `nano`) 편집기로 `/etc/passwd` 파일 열기
```
aaa@wooam:/home/cju$ vi /etc/passwd
```

5. `/etc/passwd`에서 `wooam`의 UID와 GID를 0으로 변경
```
aaa:0:0:,,,:/home/aaa:/bin/bash
```

6. 수정된 내용 저장 및 `vi` 나가기
> 참고 : `:wq!`

7. 다시 사용자 `aaa`로 전환
```
aaa@wooam:/home/cju$ su aaa
```

8. `id` 명령으로 UID 및 GID 확인
```
root@wooam:/home/cju# id
```

### 실습 2 : 패스워드가 없으면서 UID가 0인 사용자 만들기
1. `sudo` 명령을 사용할 수 없는 일반 사용자 `bbb` 생성
```
cju@wooam:~$ sudo adduser bbb
```

2. 누구나 `/etc/passwd` 파일을 수정할 수 있도록 권한 변경
```
cju@wooam:~$ sudo chmod o+w /etc/passwd
```

3. 사용자 `bbb`로 전환
```
cju@wooam:~$ su bbb
```

4. 패스워드가 없고 UID가 0인 사용자 `ccc` 추가
```
bbb@wooam:/home/cju$ echo "ccc:U6aMy0wojraho:0:0::/root:/bin/bash" >> /etc/passwd
```

5. 사용자 `ccc`로 전환
```
bbb@wooam:/home/cju$ su ccc
```

6. `id` 명령으로 UID 및 GID 확인
```
root@wooam:/home/cju# id
```

### 복구
1. 생성한 계정 삭제
```
cju@wooam:~$ sudo deluser --remove-all-files aaa bbb ccc
```

2. 다른 사용자가 `/etc/passwd` 파일을 수정할 수 없도록 권한 변경
```
cju@wooam:~$ sudo chmod o-w /etc/passwd
```
