+++
author = "defact0"
title = "MariaDB 관련 설정"
date = "2019-12-06"
description = "How to use MariaDB"
tags = [
    "database",
]
categories = [
    "DB",
]
+++
**MariaDB**는 오픈 소스의 `관계형 데이터베이스 관리 시스템(RDBMS)`이다  
MariaDB 설정에 대해 알아봅니다.

<!--more-->

# 1. mysql 외부 접속 허용하기

MariaDB(MySQL)을 설치하면 기본적으로 외부 접속이 불가능하게 되어 있다.

## 1-1. 접근IP 허용

```bash
use mysql
CREATE USER abc@'192.168.100.%' IDENTIFIED BY '패스워드';
GRANT ALL PRIVILEGES ON mysql.* TO abc@'192.168.100.%';
FLUSH PRIVILEGES;
```

## 1-2. 포트 허용

```bash
firewall-cmd --add-port=3306/tcp --permanent
firewall-cmd --reload
```

# 2. MariaDB 암호화적용

>Linux 환경에서 MariaDB가 설치되어 있는 상태에서 진행 합니다.


## 2-1. 키 파일 관리경로 생성
`/etc/my.cnf.d/encryption` 경로에 키 파일 생성

```bash
mkdir /etc/my.cnf.d/encryption
cd /etc/my.cnf.d/encryption
```

## 2-2. encryption_key_id를 위한 키 생성
아래 명령을 실행하면 `keyfile`안에 암호화 키 내용이 입력된다.
<br>`keyfile`은 평문파일 입니다. 파일 암호화를 시켜야 한다.

```bash
sudo openssl rand -hex 32 >> /etc/my.cnf.d/encryption/keyfile
```
`keyfile`를 열어, 아래와 `이후`의 내용과 같이 `1;`를 추가한다.

- 이전 : `a7addd9adea9978fda19f21e6be987880e68ac92632ca052e5bb42b1a506939a`
- 이후 : `1;a7addd9adea9978fda19f21e6be987880e68ac92632ca052e5bb42b1a506939a`

`1;`는 MariaDB SQL 문장에서 `encryption_key_id=1` 으로 사용된다.

## 2-3. keyfile 파일 암호화
`keyfile`는 에디터로 열어 볼 수 있으므로 이를 보호하기 위해서는 파일 자체에 암호화를 시켜야 한다.
<br> 아래 명령어를 실행 하면 `keyfile.enc` 파일을 얻을 수 있다.

```bash
sudo openssl rand -hex 128 > /etc/my.cnf.d/encryption/keyfile.key
sudo openssl enc -aes-256-cbc -md sha1 \
   -pass file:/etc/my.cnf.d/encryption/keyfile.key \
   -in /etc/my.cnf.d/encryption/keyfile \
   -out /etc/my.cnf.d/encryption/keyfile.enc
```

## 2-4. DB 설정파일 수정

```bash
vim /etc/my.cnf.d/server.cnf
```
### 설정 내용
```bash
[mariadb]
plugin_load_add = file_key_management.so
loose_file_key_management_filename = /etc/my.cnf.d/encryption/keyfile.enc
loose_file_key_management_filekey = FILE:/etc/my.cnf.d/encryption/keyfile.key
loose_file_key_management_encryption_algorithm = AES_CBC
innodb-encrypt-tables = ON
innodb_default_encryption_key_id=1
```
옵션 설명

    - plugin_load_add : 암호화 플러그인 로드
    - loose_file_key_management_filename : 암호화 키 파일
    - loose_file_key_management_filekey : 암호화된 DB 테이블 암호화 키 파일
    - loose_file_key_management_encryption_algorithm : DB 암호화 알고리즘
    - innodb-encrypt-tables : 테이블 자동 암호화 옵션
    - innodb_default_encryption_key_id : 설정한 id로 테이블 암호화

## 2-5. MariaDB 재구동

`server.cnf` 파일의 내용이 변경적용하기 위해서는 반드시 DB 재시작이 필요하다.
<br>`server.cnf` 내용에 문제가 있을 경우, DB 구동이 안되며 반드시 설정파일을 다시 수정해야 한다.

- 중지 : `systemctl stop mariadb`
- 시작 : `systemctl start mariadb`
- 상태 : `systemctl status mariadb.service`

## 2-6. DB 테스트
터미널에서 `mysql -uroot -p` 입력 후 MariaDB에 접속한다.
<br> 아래 명령을 입력하여 테스트 할 database를 생성한다.

```bash
[(none)]> show databases;
[(none)]> create database abc;
[(none)]> create user 'abc'@'localhost' identified by '패스워드';
[(none)]> create user 'abc'@'%' identified by '패스워드';
[(none)]> grant select, insert, update, delete, create, drop, alter on abc.*to 'abc'@'localhost'; 
[(none)]> grant select, insert, update, delete, create, drop, alter on abc.*to 'abc'@'%'; 
[(none)]> flush privileges;
```
## 2-7. 암호화 테이블 생성

```SQL
create table table1 (id int, value varchar(100)) encrypted=yes encryption_key_id=1;
```
**[참고]** `server.cnf`에 `innodb-encrypt-tables`과 `innodb_default_encryption_key_id` 옵션이 설정되어 있다면, `encrypted=yes encryption_key_id=1`을 추가 입력하지 않아도 된다.

#### 실행결과
정상적인 실행 결과는 아래와 같다.<br>만일 오류가 발생하면, 다시 설정한다.
```bash
MariaDB [abc]> show tables;
Empty set (0.000 sec)

MariaDB [abc]> create table table1 (id int, value varchar(100)) encrypted=yes encryption_key_id=1;
Query OK, 0 rows affected (0.020 sec)

MariaDB [abc]> show tables;
+-------------------+
| Tables_in_abc |
+-------------------+
| table1            |
+-------------------+
1 row in set (0.000 sec)
```

**암호화 설정**
```bash
MariaDB [abc]> insert table1 values(1, "aaaa");
Query OK, 1 row affected (0.013 sec)

MariaDB [abc]> select * from table1;
+------+-------+
| id   | value |
+------+-------+
|    1 | aaaa  |
+------+-------+
1 row in set (0.000 sec)
```

**암호화 설정해제**
```bash
MariaDB [abc]> show tables;
+-------------------+
| Tables_in_abc |
+-------------------+
| table1            |
+-------------------+
1 row in set (0.000 sec)

MariaDB [abc]> select * from table1;
ERROR 1932 (42S02): Table 'abc.table1' doesn't exist in engine
MariaDB [abc]>
```


## 2-8. 기존 테이블 암호화
`table2`이라는 테이블이 암호화 없이 운영되고 있었다면, 아래와 같은 명령어로 수정할 수 있다.

```SQL
ALTER TABLE table2 ENCRYPTED=YES ENCRYPTION_KEY_ID=1;
```

## 2-9. 암호화 테이블 복호화
table `table2`의 암호화를 비활성화 시킵니다. 만약 전에 암호화가 되어있었다면 복호화합니다.

```SQL
ALTER TABLE table2 encrypted=NO;
```

## 참고
- https://mariadb.com/kb/ko/data-at-rest-encryption/
- https://mariadb.com/kb/en/file-key-management-encryption-plugin
- https://semode.tistory.com/333