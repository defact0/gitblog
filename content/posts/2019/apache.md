+++
author = "defact0"
title = "Apache(httpd) 설치와 설정"
date = "2019-12-10"
description = "How to use Apache(httpd)"
tags = [
    "ws",
    "web",
]
categories = [
    "WEB",
]
+++
아파치 HTTP 서버(영어: `Apache HTTP Server`)는 아파치 소프트웨어 재단에서 관리하는 HTTP 웹 서버이다.  
**Apache(httpd)** 설정에 대해 알아봅니다.

<!--more-->


# Apache(httpd)

[Apache] --> [Tomcat Connectors] --> [Tomcat]

- /etc/httpd/conf/workers.properties
- /etc/httpd/conf/httpd.conf
- /etc/httpd/conf/httpd-vhosts.conf

## 1. 필요 패키지 설치

```bash
yum install -y perl
yum install -y httpd*
wget http://mirror.navercorp.com/apache/tomcat/tomcat-connectors/jk/tomcat-connectors-1.2.46-src.tar.gz
tar zxvf tomcat-connectors-1.2.46-src.tar.gz
./configure --with-apxs=/usr/bin/apxs
make && make install
chcon -u system_u -r object_r -t httpd_modules_t /etc/httpd/modules/mod_jk.so
```

## 2. 설정하기

### worker 설정

`vi /etc/httpd/conf/workers.properties`

```bash
worker.list=worker1

worker.worker1.type=ajp13		    # AJP1.3 프로토콜을 사용
worker.worker1.host=localhost		# 톰캣은 local에서 돌고 있습니다.
worker.worker1.port=8080	        # 연결할 톰캣의 포트 번호
worker.tomcat.lbfactor=1
```

### Apache 설정

`vi /etc/httpd/conf/httpd.conf`

주석처리된 아래 내용을 찾아 수정 한다.

#### ServerName

```bash
#ServerName www.exampel.com:80
ServerName 127.0.0.1:80
```
#### User & Group

```bash
 <IfModule !mpm_netware_module>
	User nobody
	Group nobody
 </IfModule> 
```
#### DirectoryIndex

```bash
# DirectoryIndex 부분에 시작페이지 파일을 작성하면 된다.
DirectoryIndex index.html index.jsp
```

### Directory 내용추가

아래 위치를 찾아
```bash
# Further relax access to the default document root:
<Directory "/var/www/html">
```
이렇게 수정한다.
```bash
<Directory "/home/macerti/apache-tomcat-9.0.29/webapps/ROOT">     
    AllowOverride none
    Require all granted
</Directory>
```


#### VirtualHost
```bash
# mod_jk.so 추가
LoadModule jk_module /etc/httpd/modules/mod_jk.so

<VirtualHost *:80>
 ServerName localhost
  # 확장자 jsp, json, xml, do를 가진 경로는 woker tomcat으로 연결하는 구문입니다.     
  JkMount /*.jsp worker1
  JkMount /*.json worker1
  JkMount /*.xml worker1
  JkMount /*.do worker1
</VirtualHost>
```

#### mod_jk.conf 생성

`vi /etc/httpd/conf.modules.d/mod_jk.conf`

```bash
<IfModule mod_jk.c>
    # 워커 설정파일 위치
    JkWorkersFile conf/workers.properties
    # 공유 메모리파일 위치 반드시 Selinux 보안때문에 run에 위치 필수     
    JkShmFile run/mod_jk.shm     
    # log 위치     
    JkLogFile logs/mod_jk.log     
    # 로그레벨 설정     
    JkLogLevel info     
    # 로그 포맷에 사용할 시간 형식을 지정한다.     
    JkLogStampFormat "[%y %m %d %H:%M:%S] " 
</IfModule>
```

 
## 3. 방화벽 포트 허용

```bash
firewall-cmd --add-port=80/tcp --permanent
firewall-cmd --reload
``` 


----

## 아파치 톰캣 연동
<https://victorydntmd.tistory.com/225>

<https://wikidocs.net/16277>

## 로드 밸런스 내용
<https://goddaehee.tistory.com/77>