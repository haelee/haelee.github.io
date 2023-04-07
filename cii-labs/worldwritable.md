## World writable 파일 점검

World writable 파일이 있으면 어떤 문제가 발생할 수 있는지 알아봅시다.

### 실습 1 : World writable로 설정된 명령어 파일 바꿔치기

**[우분투] 먼저 시스템을 취약하게 만듭시다.**

1. 리눅스 명령어 중 하나를 골라 world writable로 변경합니다. 여기서는 `whoami` 명령어를  world writable로 변경합니다.
```
cju@wooam:~$ sudo chmod 777 /usr/bin/whoami
```

2. World writable로 변경된 명령어를 적당한 곳에 백업합니다. 여기서는 `/home/cju/` 디렉터리에 백업합니다.
```
cju@wooam:~$ cp /usr/bin/whoami .
```

**[칼리] 이제 world writable 파일을 이용해 봅시다!**

>> 공격자가 원격에서 우분투에 접속합니다. 여기서는 SSH를 통해 `aaa` 계정으로 접속했다고 가정합니다.

3. 먼저 world writable 파일을 찾아봅시다.
```
aaa@wooam:~$ find / -type f -perm 2
```

4. World writable로 설정된 명령어를 대신할 **악성(?)** 명령어를 만듭니다. 여기서는 언제나 `root`를 출력하는 **악성(?)** `whoami`를 만듭니다.
```
aaa@wooam:~$ echo "echo root" > whoami
aaa@wooam:~$ chmod 755 whoami
```

5. 바꿔칩시다!!!
```
aaa@wooam:~$ cp whoami /usr/bin/whoami
```

6. 바꿔친 명령어를 실행해 봅시다.
```
aaa@wooam:~$ whoami
root
```

**[우분투] 시스템을 복구합시다.**

7. 백업한 명령어를 다시 복구합니다.
```
cju@wooam:~$ cp ./whoami /usr/bin/whoami
```

8. `root` 외 다른 사용자가 쓸 수 없도록 변경합니다.
```
cju@wooam:~$ sudo chmod 755 /usr/bin/whoami
```
