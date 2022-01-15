---
layout: single
title: "[TIL] Javascript - 비동기 통신 apis"
categories: web
tag: [TIL, Javascript, 비동기, 객체]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## Javascript 개념

### 비동기 통신 APIs

#### AJAX

> Asyncronous Javascript and XML | Javascript의 비동기 통신

클라이언트와 서버 간 데이터를 주고 받을 때 HTTP 통신을 사용한다.

그 중에서도 AJAX는 변경하고 싶은 부분(필요한 부분)만 서버에 **비동기적으로 요청**하여 특정 데이터(JSON, XML 형식)을 받아 부분적으로 화면을 갱신하게 해주는 기술이다.

#### **XMLHttpRequest**

> XMLHttpRequest 객체를 이용한 Ajax의 구현

- 특징  
  Ajax 기술의 핵심적인 구성 요소로서 객체에서 제공하는 프로퍼티와 메서드 등을 이용해서 HTTP 통신을 가능하게 해준다.
- 구현 방법

```jsx
const xhr = new XMLHttpRequest(); // 객체 생성

xhr.open("GET", "https://jsonplaceholder.typicode.com/todos/1"); // http 요청 초기화
xhr.setRequestHeader("content-type", "application/json"); // http 요청 헤더 설정
xhr.send(); // http 요청 전송

// HTTP 요청이 성공했을 경우 발생하는 이벤트 핸들러
xhr.onload = () => {
  if (xhr.status === 200) {
    console.log(JSON.parse(xhr.response));
  } else {
    console.error("Error", xhr.status, xhr.statusText);
  }
};
```

- 장/단점
  - 장점
    - 별도의 dependency가 필요하지 않다.
  - 단점
    - 요청할 때의 코드가 장황하고 가독성이 좋지 않다.
    - 크로스 브라우징 기능이 없기 때문에 브라우저 호환성이 떨어진다.
    - 콜백 지옥이 발생할 가능성이 있다.
      <br>→ 응답 데이터/에러를 받을 때 콜백 함수를 이용하기 때문에, 받은 데이터를 이용해서 또 비동기 통신을 해야 할 때 콜백 지옥 발생

#### **jQuery - $.ajax**

> jQuery 라이브러리에서 제공하는 API

- 특징
  jQuery 라이브러리를 import 해서 사용해야 한다.
  <br>XMLHttpRequest 객체를 이용한 통신의 불편함, 호환성을 보완하여 등장했다.
  <br>콜백 함수를 이용하여 비동기 통신을 구현한다.

- 구현 방법

```jsx
$.ajax({
  url: "https://jsonplaceholder.typicode.com/todos/1", // 요청 서버 URL 주소
  data: {}, // 서버로 보낼 데이터 (메서드에 따라 선택)
  type: "GET", // HTTP 요청 메서드(GET, POST, PUT, DELETE ...)
  dataType: "json", // 서버에서 보내줄 데이터의 타입
  success: function onData(data) {
    // 서버 응답이 성공하였을 때 실행되는 함수
    console.log(data);
  },
  error: function onError(error) {
    // 서버 응답이 실패하였을 때 실행되는 함수
    console.error(error);
  },
});
```

- 장/단점
  - 장점
    - 요청을 직관적으로 작성할 수 있다.
    - 브라우저 호환성을 지원한다.
    - 지원하는 기능이 많다.
      → jQuery의 deferred 객체, when 등 Promise 와 비슷한 기능을 지원하여 콜백 지옥에서 벗어날 수 있다.
  - 단점
    - jQuery 라이브러리를 사용해야 한다.
      <br>→ 비동기 통신만을 위해 사용하는 것이라면 무거운 라이브러리를 사용하기가 부담.

#### **fetch api**

- 특징
  > ES6 부터 도입된 Javascript 내장 API
  > Promise 기반으로 만들어져 있다.

응답 형태가 Response 객체이기 때문에 처리를 해주어야 한다.
→ res.json(), res.text() 등

- 구현 방법

```jsx
const post = (url, payload) => {
  return fetch(url, {
    method: "POST",
    headers: { "content-Type": "application/json" },
    body: JSON.stringify(payload),
  });
};

const url = "https://getmyinfo.com/posts/1";
post(url, { payload: "payload" }).then((res) => console.log(res));
```

- 장/단점
  - 장점
    - Promise 기반으로 동작하므로 콜백 지옥이 없다.
    - 라이브러리 추가 없이 이용할 수 있다.
    - xhr 객체 대비 가독성이 조금 더 좋아진다.
    - React Native의 잦은 업데이트에도 잘 적용된다.
      <br>→ 아직 안정화가 되지 않은 프레임워크의 지속적인 update에도 에러 발생 확률이 낮다.
  - 단점
    - 내장 API로 브라우저 간 호환성 지원이 없다.
    - 기능이 부족하다. (response timeout 등)
    - API 요청 취소가 불가능하다.

#### axios

- 특징
  > node.js와 브라우저를 위한 HTTP 통신 라이브러리.

Promise 기반으로 동작한다.
<br>React, Vue.js에서 많이 사용된다.

- 구현 방법

```jsx
axios({
  method: "post",
  url: "https://getmyinfo.com/posts/1",
  data: { payload: "payload" },
});
```

- 장/단점
  - 장점
    - 코드가 간단하고, 가독성이 좋다.
    - Promise 기반으로 동작하므로 콜백 함수의 걱정이 없다.
    - 크로스 브라우징 기반으로 브라우저 호환성이 좋다.
    - 자동으로 JSON 데이터 형식으로 변환된다.
    - fetch api 보다 기능이 많다
      <br>→ 요청 취소 기능, response timeout 기능, HTTP 요청 가로채기 기능/interceptors
    - XSRF 해킹 기법에 비교적 안전하다. (보안성)
  - 단점
    - 라이브러리를 설치해야 한다.

[참고 및 출처]
https://ghost4551.tistory.com/139
https://velog.io/@kysung95/%EA%B0%9C%EB%B0%9C%EC%83%81%EC%8B%9D-Ajax%EC%99%80-Axios-%EA%B7%B8%EB%A6%AC%EA%B3%A0-fetch#ajax
https://ko.javascript.info/fetch
