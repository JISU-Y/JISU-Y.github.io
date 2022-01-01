---
layout: single
title: "[TIL] React - DOM 과 Virtual DOM"
categories: web
tag: [TIL, React, DOM, Virtual DOM]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## React

### DOM / 가상 DOM

DOM은 항상 애매모호하게 알고 있는 개념이다. 이번 기회에 조금 명확하게 해야겠다.

1. DOM
   > Document(HTML문서) Object Model의 줄임말

공식 정의는

> HTML이나 XML 문서를 실체로 나타내는 API

일단 정의만 보면 도통 무슨 말인지 와닿지가 않는다. (~~물론 설명봐도 잘 이해 안됨~~)

먼저, DOM은 어디선가 들어봤고, 웹 개발을 하다보면 듣게 되는 용어이다.

HTML 요소들(input, button, div 등등)을 "부품"이라고 본다면
<br>그것을 우리가 마크업해서 "주문서"를 보내면 <br>브라우저가 그대로 "실제 제품"(input은 숫자/텍스트 등을 입력받고, 버튼은 클릭 이벤트를 감지한다든지 등등의 완전한 기능을 가진)을 생산하는 것이라고 볼 수 있다.

그러니까, vscode에서는 그냥 한낱 꺾쇠랑 문자가 든 문자열 파일이었지만
<br>이를 브라우저에 보내게 되면 브라우저는 그 내용을 토대로 **실제로 사용할 수 있는 오브젝트**들로 만들어 주는 것이다.

그래서 DOM이 이 input, button, div 하나하나가 DOM이라는 건가??

는 아님.

<br>

HTML 요소들 하나하나가 아니라 **HTML 문서의 그 전체 구조에 맞춰서 제품들(input, button, div ...)이 배치되고, 여기에 개발자가 추가적으로 명령을 보내서 속성이나 디자인, 배치 등을 조작할 수 있도록 된 상태**이다.
<br>그러니까 "HTML 언어로 설계된 웹 페이지가 브라우저 안에서 화면에 나타나고 이벤트에 반응하고 값을 입력받는 등의 **기능들을 수행할 객체들로 실체화된 형태**"라고 봐야 한다.

그래서 이 DOM을 사용해서 원하는 대로 화면에 나타내주고, 필요하면 개발자가 자바스크립트로 이 DOM을 조작하여 기능을 만들어 낼 수도 있는 것이다.

<br>

그렇다고 DOM이 특정 언어에 종속된 것은 아니다.
자바스크립트가 만들어 낸 객체가 아니라, 자바스크립트가 **제어해야 할 대상**이라는 것이다.

어떤 언어로든 특정 라이브러리만 있으면 DOM을 제어할 수 있는 것은 바로 **DOM이 api를 가지고 있기 때문**이다.

DOM은 어떤 브라우저가 만들든 대동소이하다.
<br>(크롬에서 만든 DOM의 input value를 가져올 때 input.value() 이렇게 했다가, 파이어폭스에서 만든 DOM은 input value를 input.v() 이렇게 하는 건 아니니까)

DOM은 트리구조이다.
<br>html 태그안에 head/body 태그가 있고, 또 body안에는 div 등등이 있기 때문에 DOM은 트리 구조로 되어 있는 것이다.
<br>DOM의 모든 요소들(HTML, DIV, input 등등)은 모두 node로 부터 상속을 받는다.
<br>그냥 html element들은 모두 다 노드이다라고 이해해버리자. (노드의 기능들을 모두 가지고 있다는 것)

노드의 기능들이 무엇이냐면??
<br>맨날 HTML 요소 가지고 구현했던 기능들 거의 모두가 노드의 기능들이라고 보면 된다.

- textContent, childNode, parentNode, appendChild, removeChild 등등

또 node는 클릭 이벤트가 가해지는 EventTarget이기 때문에(node는 EventTarget으로부터 상속받기 때문에), 요소들이 모두 addEventListener 등의 기능 또한 가지고 있는 것이다.

이러한 노드의 기능들이 개발자들이 자바스크립트로 각 요소들을 조작할 수 있게 되는 것이다.

각 요소마다 고유 속성이나 기능들도 있다. (a태그는 href, img는 src 등 각 태그들이 반환하는 고유의 속성들이 있다.)

#### CSSOM

CSS는 CSSOM(CSS Object Model)이 생성된다.
<br>이것 또한 트리형태이며 브라우저는 DOM 트리랑 CSSOM 트리를 융합해서 우리가 보는 화면들을 만들어 내는 것이다.

#### BOM

BOM(Browser Object Model)도 있다. 브라우저를 다룰 수 있게 해주는 api
<br>window 안에 location, navigation, screen 등등의 기능들이 BOM에서 제공하는 api들인 것이다.

2. Virtual Dom (가상 DOM)

가상 DOM이 뭔지 알려면 일단 React랑 Vue 즉, SPA(Single Page Application)가 왜 생겨났는지 알아야 한다.

예전에 좋아요 버튼 하나 누를려고 웹 페이지 전체가 교체되어 깜빡대는 현상이 있었는데, 이는 UX 측면에서 아주 좋지 않았다.

그래서 SPA(react / vue)는 데이터들이 조금씩 바뀌어도 페이지 전체가 리로딩되는 현상때문에 생긴 것 보완하려고 js를 이용해서 html를 필요한 부분만 바꿔줌으로써(DOM을 조작해서) 깔끔한 UX를 만들기 위해 생겨난 것이다.

jQuery는 div 같은 요소들 하나하나 선택해서 class를 바꾸든 뭘 추가하든 해서 직접 코드로 DOM을 조작했던 것이기 때문에 현재는 잘 쓰지 않는다.

react / vue는 템플릿으로 HTML 요소를 코딩해두고, 그 안에 바꾸려고 하는 데이터만 연결해 넣으면 그 데이터들에 맞춰 알아서 화면이 잘 바뀌게 하는 구조이다.

이건 뭐 react가 그냥 해줄까?? 직접 DOM을 조작해서???
<br>는 이것도 아님.
<br>직접 DOM을 조작하면 js랑 뭐가 다름

그러니까, react/vue는 저 과정에서 Virtual DOM이란 개념을 도입하게 된다.
<br>Virtual DOM은 DOM 구조를 흉내낸 자바스크립트 객체(이제 진짜 js가 만든 객체임)인 것이다.

그래서 가상 DOM을 왜 도입했는데?

봐보자. 이미 브라우저에서 겁나 많은 양의 실제 DOM을 다 만들어놨는데 자바스크립트에서 명령이 와서 갑자기 몇개를 바꾼다고 하면..?
<br>이렇게 실제 DOM을 다 갈아치운다고 하면 리소스가 많이 들 것이다.

그래서 가상돔을 이용해서 배치들을 미리 짜둔다음에 실제 DOM과 비교해서(이걸 DIFFING이라고 한다) 바뀐 부분만 적용이 될 수 있도록 한 것이다.
