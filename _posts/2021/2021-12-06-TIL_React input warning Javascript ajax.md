---
layout: single
title: "[TIL] React - input warning / Javascript - ajax"
categories: Javascript
tag: [TIL, Javascript, React, ajax]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## React

### Error 처리

#### Input Warining

```
index.js:1 Warning: A component is changing a controlled input to be uncontrolled. This is likely caused by the value changing from a defined to undefined, which should not happen. Decide between using a controlled or uncontrolled input element for the lifetime of the component.
```

위와 같은 에러가 뜨길래 구글링을 해보았다.

에러는 input 쪽에서 발생했다.

찾아보니 input value에 undefined로 들어갈 경우 발생한다고 한다.

```jsx
<input
  className={`${styles.todoInput} ${styles.edit}`}
  type="text"
  placeholder="Update your item"
  value={postTodo.todoText ?? ""}
  name="text"
  onChange={(e) => setPostTodo({ ...postTodo, todoText: e.target.value })}
  ref={inputRef}
/>
```

따라서 postTodo.todoText가 undefined일 경우에는 string 값을 넣어주었다(add랑 update 둘다)

## Javascript

### 비동기 - ajax

#### ajax란?

> Asyncronous Javascript and XML의 약자로 자바스크립트를 사용하여 브라우저가 비동기 방식으로 데이터를 요청하고, 서버가 응답한 데이터를 수신해서 웹 페이지를 동적으로 갱신하는 프로그래밍 방식

AJAX는 XMLHttpRequest 객체(브라우저에서 제공하는 web api)를 기반으로 동작한다.
<br>최신 문법에는 fetch를 이용해서 요청하고, Promise가 오면 then을 이용해서 응답 처리를 하기도 한다. (추가로 async await도 있다.) 혹은 라이브러리를 사용할 수도 있다.(jQuery의 ajax, React나 Vue에서는 axios 등)

그러니까 비동기적으로 HTTP 요청을 해서(web api 이용) 콜백 함수를 다시 받는 그 방식, 그것을 ajax 라고 한다.

#### Ajax가 등장하면서 생긴 장점

![](https://images.velog.io/images/jisu129/post/ba23ecb1-5173-4bef-a2fc-3587bb83f252/mpaLifeCycle.png)

이전의 웹페이지는 html태그로 이루어진 완전한 HTML을 서버로부터 전송받아 웹 페이지 전체를 처음부터 다시 렌더링하는 방식으로 동작했다.

따라서 화면이 전환되면 서버로부터 새로운 HTML을 전송받아 전체적으로 다시 렌더링했다. (화면 깜빡거림)

하지만 Ajax가 도입이 되면서
![](https://images.velog.io/images/jisu129/post/7c6a324a-d436-4387-b14f-a0349528e91e/spaLifeCycle.png)

변경이 필요한 데이터만 비동기 방식으로 요청/전송받아 변경이 필요한 부분만 렌더링하는 방식을 도입할 수 있게 되었다. SPA의 시작이 되었다.

#### JSON

JSON이란?

> JSON(Javascript Object Notation)은 클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷
> <br>자바스크립트의 객체 리터럴과 유사하게 키와 값으로 구성되어 있는 텍스트이다.

- JSON.stringify

  객체 혹은 배열을 JSON 포맷의 문자열로 변환해주는 메서드이다.
  <br>서버에 객체를 전송할 때 객체를 문자열화 해주어야 한다.(직렬화/Serializing)

  ```jsx
  const obj = {
    name: "Lee",
    age: 20,
    alive: true,
    hobby: ["traveling", "tennis"],
  };

  const json = JSON.stringify(obj);
  console.log(typeof json); // string
  console.log(json);
  // {"name":"Lee","age":20,"alive":true,"hobby":["traveling","tennis"]}
  ```

  들여쓰기 가능 / 세번째 인자로 들여쓰기 수 전달

  ```jsx
  const prettyJson = JSON.stringify(obj, null, 2);
  console.log(typeof prettyJson); // string
  console.log(prettyJson);
  // {
  //     "name": "Lee",
  //     "age": 20,
  //     "alive": true,
  //     "hobby": [
  //       "traveling",
  //       "tennis"
  //     ]
  // }
  ```

  필터링 가능 / replacer 함수를 두번째 인자로 받아 원하는 값을 필터링할 수 있다.

  ```jsx
  function filter(key, value) {
    return typeof value === "number" ? undefined : value; // undefined를 반환값으로 두면 반환하지 않겠다는 것
  }

  const strFilterObject = JSON.stringify(obj, filter, 2);
  console.log(typeof strFilterObject); // string
  console.log(strFilterObject); // value 값이 숫자형인 프로퍼티는 직렬화하면서 제외.
  // {
  //     "name": "Lee",
  //     "alive": true,
  //     "hobby": [
  //       "traveling",
  //       "tennis"
  //     ]
  // }
  ```

- JSON.parse

  JSON 포맷의 문자열을 객체로 변환해주는 메서드이다.
  <br>서버로 부터 받는 데이터는 JSON 형식의 문자열이다. <br>따라서 코드 내에서 객체로 사용하려면 변환해주어야 한다.(역직렬화/Deserializing)

  ```jsx
  const obj = {
    name: "Lee",
    age: 20,
    alive: true,
    hobby: ["traveling", "tennis"],
  };
  const json = JSON.stringify(obj);
  console.log(typeof json); // string
  console.log(json);
  // json 은 json 형식의 문자열이다.

  // parse
  const parsed = JSON.parse(json);
  console.log(typeof parsed); // object
  console.log(parsed); // { name: 'Lee', age: 20, alive: true, hobby: [ 'traveling', 'tennis' ] }
  ```

#### XMLHttpRequest

> Web에서 제공하는 API 중 하나로 브라우저 주소창/HTML 태그를 통해서만 HTTP 요청 전송 기능을 사용했었지만, 자바스크립트를 사용해서 HTTP 요청을 할 수 있도록 만든 객체이다.

이 객체를 통해서 **자바스크립트 코드에서 HTTP 요청이 가능**해졌다.
<br>XMLHttpRequest 객체의 다양한 **메서드와 프로퍼티를 이용해 HTTP 요청과 응답 처리**를 하게 된다.

- HTTP 요청 전송

  ```jsx
  const xhr = new XMLHttpRequest(); // 객체 생성
  xhr.open("GET", "/users"); // http 요청 초기화
  xhr.setRequestHeader("content-type", "application/json"); // http 요청 헤더 설정

  xhr.send(); // http 요청 전송
  ```

  \- open
  <br>서버에 전송할 HTTP 요청을 초기화한다.
  서버에게 요청의 종류와 목적을 알리는 방법이다. 주로 5가지 요청메서드(GET, POST, PUT, PATCH, DELETE 등) 을 사용하여 CRUD를 구현한다.

  \- send
  <br>open 메서드로 초기화된 요청을 서버에 전송한다.
  메서드에 따라 데이터(payload)를 전달할 수 있는데, 이때 json 문자열을 전달해야한다. (Stringify 메서드를 통해 직렬화 해아한다.)

  \- setRequestHeader
  <br>HTTP 요청의 헤더 값을 설정하는 메서드이다.
  content-type 설정을 통해 전송할 데이터의 타입 정보를 표현한다.
  text/html , application/json 등

- HTTP 응답 처리

  요청 후에 서버가 전송한 응답을 처리하려면 객체가 발생시키는 이벤트를 캐치해야 한다.
  <br>XMLHttpRequest 객체가 가지고 있는 프로퍼티(이벤트 핸들러 프로퍼티)를 이용해서 응답 처리를 하면 된다.

  ```jsx
  const xhr = new XMLHttpRequest();

  xhr.open("GET", "https://jsonplaceholder.typicode.com/todos/1");

  xhr.send();
  // send 했으니까 서버는 응답을 반환

  // onreadystatechange HTTP 요청의 현재 상태를 나타내는 readyState가 변경될 때마다 발생하는 이벤트
  xhr.onreadystatechange = () => {
    if (xhr.readyState !== XMLHttpRequest.DONE) return;

    if (xhr.status === 200) {
      console.log(JSON.parse(xhr.response));
    } else {
      console.error("Error", xhr.status, xhr.statusText);
    }
  };
  ```

  send하면 서버는 응답을 반환하게 되지만, 언제 응답이 도달할지는 알 수 없다.
  <br>이때, HTTP의 상태는 계속 변하므로 readyStateChange 이벤트를 통해 현재 상태를 계속 확인할 수 있다.

  응답이 완료되면 HTTP 요청에 대한 응답 상태를 확인하고(status 프로퍼티) 정상/에러를 판단할 수 있다.
  <br>만약 정상적으로 응답이 왔다면 response 프로퍼티를 통해 서버가 전송한 데이터를 가져올 수 있다.

  ```jsx
  const xhr = new XMLHttpRequest();

  xhr.open("GET", "https://jsonplaceholder.typicode.com/todos/1");

  xhr.send();

  // HTTP 요청이 성공했을 경우 발생 readyState가 XMLHttpRequest.DONE인지 확인할 필요 없다.
  xhr.onload = () => {
    if (xhr.status === 200) {
      console.log(JSON.parse(xhr.response));
    } else {
      console.error("Error", xhr.status, xhr.statusText);
    }
  };
  ```

  readyStateChange가 번거로울 시 onload 이벤트를 통해 확인할 수도 있다.
  <br>onload는 HTTP 요청이 성공적으로 완료된 경우에 발생하는 이벤트이다.
