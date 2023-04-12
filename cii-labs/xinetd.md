## /etc/(x)inetd.conf 파일 소유자 및 권한 설정

### 실습 1 : 호스트 OS만 SSH 접속 허용

**[우분투] xinetd를 설치합니다.**

1.
```
cju@wooam:~$ sudo apt install xinetd
```

2.
```
cju@wooam:~$ vi /etc/xinetd.conf
```

3. 
```
cju@wooam:~$ ls /etc/xinetd.d/
chargen  chargen-udp  daytime  daytime-udp  discard  discard-udp  echo  echo-udp  servers  services  time  time-udp
```

4.
```
cju@wooam:~$ vi /etc/xinetd.d/daytime

```

5.
```
cju@wooam:~$ sudo systemctl restart xinetd

```

6.
```
cju@wooam:~$ sudo ss -tna
State  Recv-Q Send-Q   Local Address:Port   Peer Address:Port Process
LISTEN 0      128            0.0.0.0:22          0.0.0.0:*
LISTEN 0      128          127.0.0.1:631         0.0.0.0:*
LISTEN 0      4096     127.0.0.53%lo:53          0.0.0.0:*
LISTEN 0      128               [::]:22             [::]:*
LISTEN 0      64                   *:13                *:*
LISTEN 0      128              [::1]:631            [::]:*
```


**[칼리] 칼리(또는 윈도우)에서 우분투 Daytime 서비스로 접속합니다.**

7. 
```
┌──(kali㉿kali)-[~]
└─$ telnet cju@192.168.xx.xxx 13
```
