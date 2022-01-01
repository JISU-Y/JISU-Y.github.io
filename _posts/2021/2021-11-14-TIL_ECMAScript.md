---
layout: single
title: "[TIL] ECMAScript"
categories: web
tag: [TIL, ECMAScript, Javascript]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## 오늘 배운 것

Javascript ES가 무엇이냐? ES5와 ES6의 차이는 무엇이냐..?

영상 몇개를 봐보니.. 조금 헷갈리는 용어들이긴하다.

Javascript와 ECMAscript의 차이는 무엇인지.. 뭐가 뭐를 포함하는 범위인건지..????
<br>ES5,6 등등은 버전을 뜻하는 것으로 알고는 있는데 정확히 어떤 것이 차이인지 이번기회에 알아보기로 했다.

1. Javascript / ECMAscript

- ECMAscript
  <br>ECMA international이란 정보통신에 대한 표준을 제정하는 비영리 표준화 기구에서 이 javascript라는 컴퓨터 언어의 표준을 제정하는 것이다.
  <br>javascript 언어 이외에 C#, JSON 포맷 또한 기술에 대한 표준을 이 기구에서 관리하고 있다고 한다.

  그 표준 중 **ECMA-262**라는 기술 규격이 스크립트 언어에 대한 명세를 담고 있고,
  <br>그것에 의해 정의된 범용 스크립트 언어가 바로 **ECMAscript**인 것이다.
  <br>(보통 ECMAscript specification/사양 이라고 한다.)

- Javascript
  <br>따라서 Javascript는 그 스크립트 언어의 사양을 따르는 언어라고 생각하면 된다.

  그렇기 때문에, 자바스크립트 엔진을 담당하는 브라우저들(Chrome,Mozilla,Opera, firefox 등등)이 얼마나 최신 사양의 ECMA를 준수하는지를 살펴봐야한다.(특정 기능 지원률이 얼마나 되는지 사이트에 나와있음)

2. Babel(바벨)
   위에서 말한 브라우저들 중에 만약에 특정 기능을 지원하지 않는 브라우저가 있다면??

   그것이 아니더라도 사실 브라우저는 많고 모든 브라우저가 똑같은 ECMA 사양을 준수하고 있는 것은 아니므로 (특히 예전의 Internet Explorer) 바벨이 나오게 된 것이다.

   바벨은 ES6 사양으로 작성된 Javascript 코드를 동일한 ES5 코드로 바꿔주기 때문에 (대부분의 브라우저가 모두 지원이 되는) 브라우저 간의 호환성을 높이기 위해 사용된다.

[출처] https://wormwlrm.github.io/2018/10/03/What-is-the-difference-between-javascript-and-ecmascript.html
