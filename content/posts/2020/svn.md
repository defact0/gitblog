+++
author = "defact0"
title = "CentOS7에 SVN 구축하기"
date = "2020-03-01"
description = "How to make Gitblog"
tags = [
    "linux",
]
categories = [
    "dev",
]
+++
CentOS에 SVN을 구축하여 사용하는 방법에 대해 기록한 문서이다.

<!--more-->

## 1. GCP에 서버 생성이후 SSH 등록하기
PC에 <b>PuTTY Key Generator</b>이 설치되어 있어야 key를 생성할 수 있다.

1. 유틸리티를 실행 한뒤, <b>Genrate</b>버튼을 눌러 실행한다.
1. 키가 만들어 지면 Key comment에 [USERNAME]을 입력한다.<br>[참고] username은 os 사용자 기본 계정이름이다.
1. 위 Key 내용을 복사해둔다
1. 그 후 아래 Save private key를 누르면 SSH에 접속할때 사용할 ppk파일이 생성이 되고 ppk 파일이 생성되면 같은 폴더에 Save public key 를 이용해 public key 도 생성을 해둔다
1. 그후 GCP 사이트에서 아까와 마찬가지로 메타데이터 SSH 탭에 key 내용을 복사해 넣는다
```
ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAiJdwDrkICCBVzVrsD5MM5aqXxxXlHizaBOa68VP2T9EpMf4yeLRuvRj7g+hgqk03P+oCEAqKF6XXfWLpWVDltwDJY9VkpK1F6E8AkW6Y8xLbjuJooFs2+QouGtMorr7DzdJe1dgvqvBXQsVcaUv5snEFmq1Hp6weVsiZZy56fYXvRQNJ8yzR+V6e4PbHHE7hqvDh6mzJTmqYzQDS5H4aS0x8CKcsnaFrOKHVfPd2xxgerQE8HVQTNVwaZX7F7vfMHRGnVroWF0syuFan3SlyL0SOUqzwmdSbh4YQVrH8CWVB2CvbRHeBn2q9cb1uWZUW3wJES6W64T3SapZfVb2YIw== team
```

### 1-1. PuTTY에서 접속하기

1. HOSTNAME : team@34.84.254.xx
1. PuTTY Configuration > Connection > SSH > Auth > 생성한.ppk 선택
1. 접속

### 1-2. root패스워드 변경

- passwd : 패스워드

```shell
$ sudo passwd
Changing password for user root.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```
## 2. SVN 프로그램 설치
https://goddaehee.tistory.com/81

```shell
# yum -y install subversion
# svn --version
svn, 버젼 1.7.14 (r1542130)
    Apr 11 2018, 02:40:28에 컴파일 됨
```

### 2-1. svn 디렉토리 만들기 (/svnrepo)
```shell
# mkdir /home/svn
```

### 2-2. svn 생성
```shell
# svnadmin create --fs-type fsfs /home/svn
# chmod -R g+ws /home/svn
```

### 2-3. svnserve.conf 설정
```shell
# cd /svnrepo/conf
# cp svnserve.conf svnserve.conf_bak
# vi svnserve.conf
```
svnserve.conf 내용
```
[general]
anon-access = none
auth-access = write
password-db = passwd
authz-db = authz
realm = team
```

### 2-4. svnserve 수정
```shell
# cp /etc/sysconfig/svnserve /etc/sysconfig/svnserve_bak
# vi /etc/sysconfig/svnserve
```
svnserve 내용
```
OPTIONS="--threads --root /home/svn"
```

### 2-5. passwd 설정(접속계정)
```shell
# cp /svnrepo/conf/passwd /svnrepo/conf/passwd_bak
# vi /svnrepo/conf/passwd
```
```
[users]
aaa = 패스워드
bbb = 패스워드
ccc = 패스워드
```
### 2-6. authz 설정

```shell
# cp /svnrepo/conf/authz /svnrepo/conf/authz_bak
# vi /svnrepo/conf/authz
```
authz 내용
```
[/]
aaa = rw
bbb = rw
ccc = rw
```
### 2-7. /etc/sysconfig/selinux 설정 수정
svn /format Permission denied 증상 해결 법
```
# chcon -R -t svnserve_content_t /home/svn
```

### 2-9. OS 방화벽 포트 허용

```shell
# firewall-cmd --permanent --zone=public --add-port=3690/tcp
# firewall-cmd --reload
```

### 2-8. svnserve 시작/중지
```shell
# service svnserve start
# service svnserve stop
```

## 3. GCP 방화벽 설정

- 메뉴 : VPC 네트워크 > 방화벽 규칙

- 이름 : default-allow-svn
- 설명 : svn 포트허용
- 대상 : 네트워크의 모든 인스턴스
- 소스필터 : IP 범위 (0.0.0.0/0)
- 프로토콜 및 포트 : 지정된 프로토콜 및 포트, TCP 선택 : 3690

만들기 버튼을 클릭하여 생성


## 4. SVN 유틸리티 다운로드

https://tortoisesvn.net/ 에서 다운로드 받은 후 설치

- 접속 주소 : svn://34.84.254.xx:3690
- 계정/패스워드 입력창이 나오면 성공



## 5. SELinux가 활성화되어있는 경우, SVN 저장소 접근이 안됩니다.
이 경우, CentOS 7 SeLinux 를 disabled 합니다.
sestatus 로 SELinux 의 상태를 확인할 수 있습니다.

1. `vi /etc/selinux/config`
1. `SELINUX=disabled`
1. 재부팅
1. 상태 확인 `sestatus`