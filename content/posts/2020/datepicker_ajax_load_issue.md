+++
author = "defact0"
title = "datepicker load 문제"
date = "2020-03-25"
description = "How to make Gitblog"
tags = [
    "web",
]
categories = [
    "web",
]
+++
회사에서 datepicker를 이용하여 웹 개발을 하던 중 발생한 문제에 대해 어떻게 해결했는지 기록한 문서이다.

<!--more-->

## 문제상황
datepicker를 사용하여 회원정보를 수정하는 페이지를 만들었는데  
정보를 수정하고 나서 변경된 내용을 jqeury의 load 함수를 통해 다시 출력하다보니  
datepicker가 동작안하는 문제가 발생했다.

## 해결방안
구글 검색을 통해 알아봤는데 이런 사례가 꽤 있는거 같았고, 해결방안은 딱히 있는거 같지 않았다.
여러 연구를 통해 datepicker를 건드리지 않고 다시 출력하고 싶은 부분만 새로고침하는 것으로 수정하였다.

```javascript
$("#list_contents").load("/test/member/view #list_contents", {"seq" : "${usrVO.usrSeq}"});
```

이렇게 하면 api_contents이라는 id를 가진 div 영역만 새로고침 된다.

```jsp
<input type="text" name="datepicker" id="datepicker" class="datepicker select" value="${datepicker}" />

<div id="api_contents">
    <span>새로고침 되는 내용</span>
</div>
```
