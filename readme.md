# Gitblog

Hugo 이용해 만든 블로그 입니다.  
https://defact0.github.io/  
>Hugo 는 Go로 작성된 정적 사이트 생성기이다.  
2013년 Steve Francia에 의해 처음 개발된 휴고는 현재의 수석 개발자 Bjørn Erik Pedersen의 도움을 받아 기능, 성능 면에서 큰 향상을 이루었다.

# 관리하기
## 1. hugo 소스 push

```bash
git add *
git commit -m "message"
git push origin master
```

## 2. hugo 작업물 push

웹에서 볼 수 있게 정적 웹 페이지로 변환하는 명령입니다.  

```bash
hugo
```

public 이라는 폴더가 생성되고, 그 안에 있는 파일을 git push 하면 됩니다.

```bash
cd public/
git init
git remote add origin https://github.com/defact0/defact0.github.io.git
git add *
git commit -m "publish"
git push -u origin +master
```