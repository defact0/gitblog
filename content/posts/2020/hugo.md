+++
author = "defact0"
title = "Hugo를 이용하여 Gitblog 만들기"
date = "2020-03-25"
description = "How to make Gitblog"
tags = [
    "git",
]
categories = [
    "dev",
]
+++

**Hugo**는 jekyll, hexo 등과 같이 웹사이트를 쉽게 만들 수 있게 해주는 `static site generator`중 하나이다  
Hugo를 이용하여 나만의 Gitblog를 만들어 본다.

<!--more-->

## 1. Hugo는 뭔가요?

- **Hugo**는 jekyll, hexo 등과 같이 웹사이트를 쉽게 만들 수 있게 해주는 **static site generator**중 하나이다.  
- static site는 고정된 html을 그냥 뿌려주는 사이트이다.  
따라서 static site를 쓴다면 언제 들어간다고 해도 항상 같은 화면만 나온다.  
- Hugo는 **Go** 라는 언어로 만들어졌다.


## 2. Git 설치
Hugo를 작업한 결과물을 Github에 업로드 하기 위해서 필요하지만,  
Git bash를 쓰기 위해서라도 설치한다.

Git bash는 윈도우 OS에서도 리눅스 명령어를 실행 시킬 수 있는 프로그램이다.  
[git-scm](https://git-scm.com/) 에서 다운로드 받아 설치한다.


## 3. Hugo 설치
필자의 작업환경은 주로 windows 환경이고, 해당 OS 기준으로 작성을 한다.  

1. [Hugo releases](https://github.com/gohugoio/hugo/releases)에서 다운로드 한다.  
`hugo_0.68.1_Windows-64bit.zip`을 다운받았다.
2. 압축을 풀면 exe 파일과 텍스트 파일 두개가 나오는 데 필요한건 exe 파일이다.
3. `C:\Hugo\bin` 경로에 exe 파일을 위치 시키고, `시스템 환경 변수 편집 > 환경 변수 > Path` 에 등록하였다.


### 3-1. Hugo 새 프로젝트 생성
마우스 우클릭을 하면 **Git bash Here** 이라는 메뉴가 보이는데, 탐색기를 통해 블로그를 작업할 위치로 이동 후, **Git bash Here**를 실행 시킨다.

![gitbash](/img/2020/03/25/gitbash.png)

```shell
$ hugo new site test
```
test 이라는 폴더가 생성되면서 폴더 안에는 hugo 기본 파일이 구성되어 있다.


### 3-2. Hugo 테마 고르기

![hugothemepure](/img/2020/03/25/hugothemepure.png)

[Hugo Themes](https://themes.gohugo.io/)에 접속하여 마음에 드는 테마를 고른다.  
나는 [Hugo Theme Pure](https://themes.gohugo.io/hugo-theme-pure/)가 마음에 들어 해당 테마를 다운받기로 했다.

```shell
$ hugo new site defact0
$ cd defact0
$ git clone https://github.com/xiaoheiAh/hugo-theme-pure themes/pure
```

기존에 만들었던 test를 쓰려면 defact0 단어를 test으로 변경하여 명령을 실행하면 된다.

### 3-2. Hugo 테마 설정하기

테마를 적용하려면 설정파일을 작성해야 한다.
**config.toml** 파일이나 테마에 따라서 **config.yml**을 사용하기도 한다.
이 테마는 **config.yml**를 사용한다.

어떻게 할지 모르겠다면, `defact0\themes\pure\exampleSite` 경로로 이동 하면 사이트 예제를 볼 수 있다.
해당 파일을 몽땅 복사해서 `defact0` 경로로 붙여 넣기 하면 제작자가 설정한 hugo 테마 설정을 적용해서 볼 수 있다.

### 3-3. Hugo 사이트 실행해 보기

해당 테마의 설정파일은 config.yml이기 때문에 기본 생성된 config.toml 파일과 같이 있으면 오류가 발생한다.  
파일 확장자를 config.toml_ 형태로 변경 후, 아래 명령어를 실행한다.

```shell
$ hugo server
```

오류가 없다면 아래와 같은 화면을 볼 수 있다.

![hugoserver](/img/2020/03/25/hugoserver.png)

콘솔 내용을 보면, http://localhost:1313/ 이라는 정보를 알 수 있는데, 웹 브라우저에 해당 주소를 입력하면 아래와 같이 테마가 적용된 site가 접속된다.

![hugothemepure_init](/img/2020/03/25/hugothemepure_init.png)

hugo의 장점은 편집을 하면 실시간으로 브라우저에 반영된다.
작업을 하면 웹 브라우저에 화면을 바로바로 확인 할 수 있다.