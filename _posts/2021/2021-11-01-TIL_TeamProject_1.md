---
layout: single
title: "[TIL] Vanilla.JS Team Project - 1일차"
categories: Project
tag: [TIL, VanillaJS, html, css, bootcamp]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## Team project

일단 우리 조는 식단 추천 웹 페이지를 만들기로 했다.

어차피 완성하는 것이 중요하기에 아이디어를 깊게 생각할 필요는 없을 것 같았다.

미니 프로젝트이기도 하니..

공공데이터에서 "추천 식단"이라는 api를 사용하기로 했으나 json 형식을 지원하지 않는다.
따라서 xml 형식의 response를 json 형식으로 바꿔주는 작업을 또 해야한다.

일단 나는 main page를 맡아서 프론트와 api 요청하여 data를 화면에 나타내는 것까지 하기로 했다.

오늘은 대강의 html css 마크업만 빠르게 진행했다!

내일은 api를 사용해서 데이터를 나타내보려고 한다.

<hr>

ajax와 rest의 차이점

AJAX란 Asynchronous JavaScript and XML.

(출처: https://parkjh7764.tistory.com/11 [HwanE Develop Blog])

기본적으로 비동기는 여러가지 일이 동시 다발적으로 발생했을 때 동시다발적으로 업무 처리할 수 있는 것.

동기는 한 업무 처리했다가 또 한 업무를 처리하므로 비효율적이다.

그래서 ajax를 사용하는 이유는 요청하는 동안 끊기지 않고 다른 업무를 수행할 수 있기 때문이다.

기존의 HTTP 프로토콜은 클라이언트인 브라우저에서 서버에 있는 데이터를 가져오기 위해 요청(request)를 하면 서버 쪽에서 응답(response)을 받는 동안 연결이 끊기게 되어 있었다.

요청을 보낸 후 응답을 받을 때까지 기다리는 것은 시스템 효율에 좋지 않다.

Rest와 ajax의 차이점을 설명한 글
https://ko.natapa.org/difference-between-ajax-and-rest-1096

AJAX와 REST의 주된 차이점은 Ajax는 일련의 기술이다.

페이지를 다시로드 할 필요없이 UI의 일부분을 동적으로 업데이트하는 방법입니다. 반면에 REST는 일종의 소프트웨어 아키텍처입니다. 이것은 사용자가 서버에 정보를 요청하는 방법입니다.

흠 rest에 관해 공부가 더 필요할 듯하다...
