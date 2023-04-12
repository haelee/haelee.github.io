## 접속 IP 및 포트 제한

### 실습 1 : 호스트 OS만 SSH 접속 허용

**[우분투] 방화벽(`ufw`)을 활성화하고 SSH만 허용합시다.**

1. 우분투 방화벽(`ufw`)을 활성화합니다.
```
cju@wooam:~$ sudo ufw enable
```

2. 외부에서 우분투 SSH 서비스 접속을 허용하도록 `ufw`를 설정합니다.
```
cju@wooam:~$ sudo ufw allow ssh
```

3. `ufw` 규칙을 확인합니다.
```
cju@wooam:~$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere                  
22/tcp (v6)                ALLOW       Anywhere (v6) 
```

**[윈도우] 윈도우에서 우분투 SSH 서비스로 접속합니다.**

4.
```
C:\Users\haele>ssh cju@192.168.xxx.xxx
```

**[칼리] 칼리에서 우분투 SSH 서비스로 접속합니다.**

5. 
```
┌──(kali㉿kali)-[~]
└─$ ssh cju@192.168.xx.xxx
```

**[우분투] 윈도우만 우분투 SSH 서비스로 접속할 수 있도록 제한합시다.**

6. SSH 서비스 접속을 허용하는 규칙을 삭제합니다.
```
cju@wooam:~$ sudo ufw delete allow ssh
```

7. 이제 윈도우만 SSH 서비스로 접속할 수 있도록 `ufw`를 설정합니다.
```
cju@wooam:~$ s sudo ufw allow from 192.168.102.1 to any port 22

```

8. `ufw` 규칙을 확인합니다.
```
cju@wooam:~$ sudo ufw status 
Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       192.168.xxx.xxx
```

**[윈도우]] 윈도우에서 우분투 SSH 서비스로 접속합니다.**

4.
```
C:\Users\haele>ssh cju@192.168.xxx.xxx
```

**[칼리] 칼리에서 우분투 SSH 서비스로 접속합니다.**

5. 
```
┌──(kali㉿kali)-[~]
└─$ ssh cju@192.168.xx.xxx
```