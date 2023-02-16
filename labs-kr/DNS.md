## DNS Zone Transfer 제한

### 배경
영역 전송은 기본 DNS 서버에 있는 영역 파일의 내용을 보조 DNS 서버로 복사하는 프로세스입니다. 영역 전송을 사용하면 기본 DNS 서버의 영역 파일을 보조 DNS 서버의 해당 영역 파일과 동기화하여 내결함성을 제공합니다. 보조 DNS 서버는 기본 DNS 서버에 장애가 발생해도 이름 확인을 계속 수행할 수 있습니다.
DNS 영역 전송은 일종의 DNS 트랜잭션입니다. 이것은 관리자가 여러 DNS 서버에서 DNS 데이터베이스를 복제하는 데 사용할 수 있는 많은 메커니즘 중 하나입니다. 영역 전송은 전송을 위해 TCP를 사용하며 클라이언트-서버 트랜잭션의 형식을 취합니다.
DNS Zone Transfer는 Zone에 등록된 도메인을 통해 시스템의 목적과 특성을 파악하고 시스템의 IP 목록을 파악하는 등 다양한 정보를 제공합니다.

### 목표
Zone 파일은 Master와 Slave 사이에서만 공유되어야 합니다. 그렇지 않을 경우 과도한 정보 유출을 유발할 수 있는 심각한 취약점이 될 수 있습니다. 특정 IP 주소로만 영역 전송을 허용하는 옵션으로 변경합니다.

### 설정 방법
/etc/named.conf 파일에서 allow-query 옵션을 변경하십시오.

### 잘못된 보안 설정으로 인한 피해
스크립트 또는 다수의 호스트를 이용하여 Zone Transfer에 지속적으로 접근할 경우 DoS 공격이 가능하며, 사용 IP 범위, 방화벽 등의 보안 장비 IP 정보가 노출될 수 있습니다.

### 교육 목표
DNS 서버에 저장된 도메인 정보를 승인된 DNS 서버가 아닌 외부인에게 유출하는 것은 보안상의 이유로 바람직하지 않습니다. 따라서 적절한 보안 설정을 통해 도메인 정보의 전송을 제한할 필요가 있습니다. DNS 도메인 정보가 노출되면 악의적인 사용자가 해당 정보를 이용하여 홈페이지 및 하위 URL 정보를 획득하고 웹 애플리케이션 구조를 예측할 수 있습니다. 영역 전송은 /etc/named.conf 구성을 통해 특정 서버(10.10.10.1)에서 지정할 수 있습니다.

### 시스템 정보
실습 시스템: Linux(Cent OS)
시스템 계정: root/root123
[참조]
/etc/named.conf 파일 수정

### 문제 해결
[설정]
[root@localhost ~]# python v-sys-19-l-setup.py
Stopping named:                       [  OK  ]
Starting named:                       [  OK  ]
DNS-server setting OK
--Setup OK!!!--

1. 허용 쿼리 수정
```
# vi /etc/named.conf
# allow-query { any; }; 🡪 allow-query { 10.10.10.1; };
```
 
 

2. 네임드 서비스 재시작
```
# service named restart
```

 
3. 답이 맞는지 확인
```
[root@localhost ~]# check
/etc/named.conf file modify is correct answer
success
```