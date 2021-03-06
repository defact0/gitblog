+++
author = "defact0"
title = "Gitbook을 만들어 보자"
date = "2019-08-05"
description = "How to make Gitbook"
tags = [
    "git",
]
categories = [
    "dev",
]
+++
GitBook은 git과 markdown이 결합된 `문서버전관리` 도구입니다.  
markdown문법을 사용하여 만드는 **Gitbook**에 대해 알아봅니다.

<!--more-->


# 1.Git 설치
사내에 설치된 Gitlab 서버와 연결하기 위해서 아래 링크를 통해 git을 설치합니다.<br>
https://git-scm.com/downloads

아래 사이트에 접속하여 nodejs를 설치 합니다.<br>
https://nodejs.org/en/
	
## Nodejs 버전 확인
```bash
$ node -v
```

![Gitbook_Ins_win_01](/img/2019/08/05/Gitbook_Ins_win_01.png)
	
# 2. Gitbook 설치(Windows)
```bash
$ npm install gitbook-cli -g
```
![Gitbook_Ins_win_02](/img/2019/08/05/Gitbook_Ins_win_02.png)

# 3.Gitbook 생성

## 3-1. Gitbook init

Gitbook을 생성한다.
명령어 위치 기준으로 test 이라는 폴더가 만들어 지며, <br>
test 폴더에는 `README.md` 파일과 `SUMMARY.md` 파일을 확인 할 수 있습니다.

```bash
$ gitbook init test
```
| 파일이름 | 설명 |
|:--------|:--------|
| <b>SUMMARY.md</b> | URL을 만들고 글의 순서와 목차를 관리 |
| <b>README.md</b> | 목차의 내용을 입력 |

![Gitbook_Ins_win_03](/img/2019/08/05/Gitbook_Ins_win_03.png)

![Gitbook_Ins_win_04](/img/2019/08/05/Gitbook_Ins_win_04.png)

## 3-2. Gitbook serve

아래와 같이 install 이후, serve를 실행한다.<br>
install은 `book.json`에 입력된 추가 기능을 설치하고<br>
serve를 통해 완성된 웹페이지를 출력한다.

| 파일이름 | 설명 |
|:--------|:--------|
| book.json | 깃북은 book.json파일이 세팅 파일이다.<br> 플러그인을 넣고, 지우고, 전역 변수 등을 설정할 수도 있다. |

gitbook에 추가한 plugin 이 있을 경우 다운로드 받는 명령
```shell
gitbook install
```
gitbook을 실행하고, 웹 브라우저를 확인 할 수 있게 웹 서비스를 실행한다.
```bash
$ gitbook serve
```

![Gitbook_Ins_win_05](/img/2019/08/05/Gitbook_Ins_win_05.png)

## 3-3. Gitbook 접속

![Gitbook_Ins_win_06](/img/2019/08/05/Gitbook_Ins_win_06.png)

- 인터넷 브라우저에 `http://localhost:4000`를 입력하면 아래와 같은 화면이 출력된다.
- 임시 웹서버를 중지하고 싶다면, `CTRL+C` 키를 CMD 창에 입력하면 된다.

# 4. 실행 스크립트
몇가지 실행과정을 스크립트로 만들어 활용 할 수 있다.

## 4-1. 실행 스크립트 내용

```shell
#!/bin/bash
clear
echo ""
echo "─────────────────────────────────────────────────────────────"
echo -e "\t\tGITBOOK LocalServer Start!!!"
echo "─────────────────────────────────────────────────────────────"
echo ""
echo "[INFO] Clean plugin..."
rm -rf node_modules
echo "[INFO] Clean gitbook..."
rm -rf _book
echo "[INFO] Getting plugin..."
gitbook install
echo "[INFO] Starting gitbook..."
gitbook serve
```
