---
layout: single
title: "[TIL] React - JSX | postCSS"
categories: React
tag: [TIL, React, JSX, git, postCSS]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## git

git 처음 push 할 때 항상 까먹어서 쳐본다

git 생성
<br>git init
<br>github 에서 repo 생성
<br>repo 생성하면 git 명령어들 다 알려줌.
<br>나는 근데 대충

git push
<br>git remote add origin [repo주소]
<br>git add .
<br>git commit -m "commit message"
<br>git push origin main
<br>이런 식으로 한다.

추가로, 작년?부터 BLM movement 때문이었는지는 모르겠지만, 어쨌든 흑인 인권과 관련하여 default branch를 master라고 하지 않고 main으로 바꾸었다.

그런데 이제 local에서 git init을 하게되면 master로 되어 있기 때문에 만약 하던대로 push 해버리면 github에서 pull request가 떠버린다. (main에는 아무것도 없어 merge 할 수도 없음)

그래서 올리기 전에
<br>git branch -M main

혹시 모르니까
<br>git status
<br>or
<br>git checkout main

이후
<br>git push -u origin main
<br>으로 한다.

이제, 커밋 메세지도 조금 더 신경 써야겠다. 멘토님이 너무 짧다고 하셨으니.. 그래도 예전 커밋들 다 적어놓은 게 어디야... 진짜 클날뻔..

## React

### PostCSS

CSS 전처리기 중 하나. (사실 POST CSS는 후처리기이다.)

모듈화와 다양한 플러그인을 가진 것이 장점이다.

CRA에 기본적으로 포함되어 있을 정도로 사용률이 높다.

style을 줄 때,
<br>만약 각각의 컴포넌트마다 같은 클래스명을 사용하면
나중에 나온 컴포넌트의 속성으로 덮어씌어진다.
<br>따라서 모듈화를 위해 원래는 BEM 규칙을 따라 클래스 명을 유일하도록 변경했었는데,
<br>postcss에서는 module로 해서 모듈화를 할 수 있다.
<br>(알아서 모듈화 시켜서 classname을 변경해준다.)

### JSX

#### JSX란

javascript 요소인데 html요소 같이 생긴 것을 JSX라고 부름

**자바스크립트 코드 위에서 html 요소 처럼 사용할 수 있게 만든 것**이다.

#### HTML과 JSX 차이

html 요소는 class / jsx 는 className

html 에서는 onclick / jsx는 onClick

html은 마크업 언어, jsx는 엄밀히 말하면 자바스크립트 언어이다

따라서 jsx는 비즈니스 로직(중괄호를 이용해서 변수 등을 나타낼 수 있다), 자바스크립트 코드를 사용할 수 있다.
<br>나중에 바벨(트랜스파일러)이 jsx를 변환해주는 것이다.
<br>jsx는 형제 노드를 가질 수 없다.
한 가지 노드로만 감싸져야 한다.
<br>(그냥 묶어줄 때는 div 태그 남발하지 말고 그냥 프래그먼트<></> 사용하기)

만약, 브라우저에서 요소를 확인했을 때,
<br>동작할 때 DOM 요소가 엄청 많이 변한다 => 매우 잘못하고 있는 것!
<br>VDOM이 있으므로 바뀐 부분만 렌더가 되어야 하는데
잘못짜서 실제 돔 요소가 많이 바뀌는 것일 수 있음
