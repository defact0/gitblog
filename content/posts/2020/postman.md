+++
author = "defact0"
title = "Postman 테스트"
date = "2020-02-21"
description = "postman test"
tags = [
    "postman",
]
categories = [
    "web",
]
+++

Spring으로 파일 다운로드 기능을 만들었는데 보안상에 문제점이 하나 발견되었다.  
`A파일은 A사용자만 받을 수 있어야 하는데, B사용자도 A파일을 받을 수 있는 상황`이 발생하였다.  
해당증상을 개선하고 검증하는 방법을 기술한다.  
`postman`이라는 프로그램을 사용한다.

<!--more-->

![1](/img/2020/02/21/1.png)

구글 로그인을 하였고, 이미 왼쪽에 작업한 내용이 저장되어 있다.

## 웹사이트 로그인

정상적인 값과 프로토콜을 설정하여 정상 로그인 세션을 얻는다.

![2](/img/2020/02/21/2.png)<br>

1. POST : 선택
1. URL : 로그인처리하는 주소 입력
1. Body > x-www-form-urlencoded 선택
1. Body > key(bizId) vlaue(bize) 입력
1. Body > key(pwd) vlaue(123123) 입력
1. [Send]버튼 클릭
1. 하단 > Body > Preview 선택, 로그인된 화면 확인 가능


## 검증 테스트

문제가 되었던 시나리오를 실행해 본다.

![3](/img/2020/02/21/3.png)<br>

1. POST : 선택
1. URL : 파일 다운로드 주소 입력
1. Body > x-www-form-urlencoded 선택
1. Body > key(fileSeq) vlaue(285) 입력
1. Body > key(id) vlaue(SE0004000012020021200000) 입력
1. [Send]버튼 클릭
1. 하단 > Body > Preview 또는 Pretty 선택, 개선된 결과 확인
