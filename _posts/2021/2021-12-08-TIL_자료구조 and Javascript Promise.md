---
layout: single
title: "[TIL] 자료구조 및 알고리즘 - Queue / Javascript - Promise"
categories: web
tag: [TIL, Javascript, React, ajax]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## 자료구조 및 알고리즘

### 자료구조 및 알고리즘

#### 자료구조 및 알고리즘이란?

자료구조 : 데이터 구조, 효율적으로 데이터를 관리할 수 있도록 한 자료 구조

알고리즘 : 문제를 풀기 위한 절차 / 방법
어떤 문제에 대해 특정 입력을 넣어서 원하는 출력을 얻을 수 있도록 하는 프로그래밍.

알고리즘의 좋고 나쁨을 판단하는 것에 이제 얼마나 시간이 걸리냐, 얼마나 메모리를 잡아먹느냐에 따라 달렸다.
<br>내가 쓰는 알고리즘의 시간, 공간 복잡도를 알고 있어야 한다.

어떤 자료구조, 알고리즘을 쓰느냐에 따라 성능이 차이가 많이 난다.

#### Queue

- 가장 먼저 넣은 데이터를 가장 먼저 꺼낼 수 있는 구조
- FIFO(First-In, First-Out) 또는 LILO(Last-In, Last-Out) 방식

1. 큐 데이터 in/out
   Enqueue: 큐에 데이터를 넣는 기능
   Dequeue: 큐에 데이터를 꺼내는 기능

2. 큐의 종류
   Queue(): 가장 일반적인 큐 자료 구조
   LifoQueue(): 나중에 입력된 데이터가 먼저 출력되는 구조 (스택 구조라고 보면 됨)
   PriorityQueue(): 데이터마다 우선순위를 넣어서, 우선순위가 높은 순으로 데이터 출력

3. 큐의 구현 (파이썬)

   1. 일반 queue (FIFO)

      ```jsx
      import queue

      data_queue = queue.Queue() # 큐 클래스로 큐 생성 (일반적인 FIFO Queue)

      data_queue.put("funcoding")
      data_queue.put(1)

      data_queue.qsize() # 2

      # get하면 가장 먼저 넣었던 것이 가장 먼저 나옴
      data_queue.get() # 'funcoding'

      data_queue.qsize() # 1

      data_queue.get() # 1
      ```

   2. LifoQueue (LIFO)

      ```jsx
      import queue
      data_queue = queue.LifoQueue()

      data_queue.put("funcoding")
      data_queue.put(1)

      data_queue.qsize() # 2

      data_queue.get() # 1 # 나중에 들어갔던 1이 먼저 나옴
      ```

   3. Priority Queue

      ```jsx
      import queue

      data_queue = queue.PriorityQueue() # 많이 쓰임

      data_queue.put((10, "korea")) # 튜플? (우선순위, 데이터)
      data_queue.put((5, 1))
      data_queue.put((15, "china"))

      data_queue.qsize() # 3

      # 우선순위가 가장 높은 것을 가장 먼저 내보낸다.
      # 따라서 가장 높은 우선순위는 1이 우선순위 5로 가장 높기 때문에 가장 먼저 나옴
      data_queue.get() (5, 1)
      ```

## Javascript

### Javascript 개념

#### 비동기 - Promise

javascript는 비동기 처리를 위해 콜백 함수를 사용한 패턴을 많이 사용한다.

하지만, 이런 전통적인 방식의 콜백 패턴은 콜백 헬이 생길 수 있으며 콜백 헬이 발생하면 가독성이 떨어지고, 디버깅이 힘들어진다.

이에 따라 ES6에서는 비동기 처리를 위해 Promise라는 패턴을 도입하게 되었다.

비동기 처리 시적을 명확하게 표현할 수 있다는 것이 장점.

**1. 콜백 헬(Callback hell)**
<br>Promise를 보기에 앞서 왜 Promise가 도입되었는지 알아보도록한다.
<br>콜백 함수를 사용해서 구현하는 비동기 프로그래밍이 그렇게 좋으면 계속 썼을 것 아닌가???

일단, 콜백 함수로 비동기 동작(예제는 모두 XMLHttpRequest 객체를 활용한 서버 요청)을 구현 해보자.

(1) 비동기 함수를 통해 서버에 요청해서 응답을 받아서 값을 반환해보자.

```jsx
const get = (url) => {
  const xhr = new XMLHttpRequest(); // xhr 객체 생성
  xhr.open("GET", url); // HTTP 초기화
  xhr.send(); // HTTP 전송

  // onload 이벤트 핸들러는 응답이 온 경우에 실행
  xhr.onload = () => {
    if (xhr.status === 200) {
      // 서버에서 온 응답이 정상이 경우 JSON형태인 response를 JS 객체로 파싱(역직렬화)하여
      // return 해서 값을 받아보자.
      return JSON.parse(xhr.response);
    } else {
      console.error(xhr.status);
    }
  };
};

const response = get("https://datafromserver.com/posts/1");
console.log(response); // undefined
```

get이 비동기 함수인 이유: 비동기적으로 동작하는 onload 이벤트 핸들러가 있기 때문에

get 함수가 호출이 되면, 안에서 XMLHttpRequest 객체를 생성하고, HTTP 초기화 하고, 전송하고,
그러고 비동기 이벤트 핸들러인 onload를 만난 순간 **이벤트 핸들러 프로퍼티에 이를 바인딩**한다.

이렇게 되면 get 함수는 하는 일이 다 끝났기 때문에 종료된다.(콜 스택에서 제거)

그런데 여기서, 종료될 때 get은 return문이 없기 때문에 (저기 위에 return 문은 get의 return문이 아니라 onload의 return문) 디폴트 값으로 undefined가 반환된다.

따라서 console에 response를 찍으면 undefined가 찍힌다.

<br>
뭐야, return은 안된다 이거지. 그러면 전역으로 변수 선언해서 거기다가 넣어볼까?

(2) 상위 스코프에 변수를 선언하여 그 변수에 비동기 결과를 할당해보자.

```jsx
let todos;

const get = (url) => {
  const xhr = new XMLHttpRequest(); // xhr 객체 생성
  xhr.open("GET", url); // HTTP 초기화
  xhr.send(); // HTTP 전송

  xhr.onload = () => {
    if (xhr.status === 200) {
      todos = JSON.parse(xhr.response);
    } else {
      console.error(xhr.status);
    }
  };
};

get("https://datafromserver.com/posts/1");
console.log(todos); // undefined
```

역시나 undefined가 반환된다.

onload 이벤트 핸들러는 비동기로 동작하기 때문에,
동작이 끝나면 콜백 큐에서 대기하고 있으므로 언제나 다른 콜 스택이 비어있을 때 그제서야 처리될 수 있다.

그러니까, 밑에 console.log 까지 실행이 다 된 후에야 onload의 내용들을 실행할 수 있는 것이다.

그러니 console.log 100개를 찍어봐도 그 모든 console.log가 끝나야 onload의 내용을 실행할 수 있으므로 어디서든 undefined가 나오게 될 것이다.

이렇게 비동기 함수는 처리 결과를 반환할 수도 없고, 상위 스코프 변수에 할당할 수도 없다.

그래서 **비동기 함수의 처리에 대한 후속 처리는 비동기 함수 내부에서 수행**해야 한다.

(3) 콜백 함수를 이용한 비동기 함수의 결과 처리

```jsx
const get = (url, successCallback, failureCallback) => {
  const xhr = new XMLHttpRequest(); // xhr 객체 생성
  xhr.open("GET", url); // HTTP 초기화
  xhr.send(); // HTTP 전송

  xhr.onload = () => {
    if (xhr.status === 200) {
      // 여기서 바로 값을 반환하거나, 변수에 할당하거나 하지 말고
      // 이 결과를 처리해줄 함수에다가 인자로 넣어서 전달한다.
      successCallback(JSON.parse(xhr.response));
    } else {
      failureCallback(xhr.status);
    }
  };
};

get("https://datafromserver.com/posts/1", console.log, console.error);
```

각각 success, failure 일 때 console.log 함수, console.error 함수가 콜백되어 함수가 실행된다.

여기까진 OK일 수 있다.
그런데 만약 비동기 처리된 값을 또 비동기 함수에 넣어주면??

그러고 싶을 때 있지 않을까? 가령 posts/1에서 받은 데이터 중 하나를 가지고 또 다른 요청을 한다든지....

그러면 어떻게 될까??

(4) 진짜 콜백 헬 (callback hell)

```jsx
const get = (url, callback) => {
  const xhr = new XMLHttpRequest(); // xhr 객체 생성
  xhr.open("GET", url); // HTTP 초기화
  xhr.send(); // HTTP 전송

  // onload 이벤트 핸들러는 응답이 온 경우에 실행
  xhr.onload = () => {
    if (xhr.status === 200) {
      callback(JSON.parse(xhr.response));
    } else {
      console.error(xhr.status);
    }
  };
};

get(`https://getmyinfo.com/posts/1`, (data) => {
  const userId = data.userId; // 받은 data의 userId
  console.log(userId);
  // post에서 가져온 userId를 이용해서 user정보를 획득
  get(`https://getmyinfo.com/users/${userId}`, (userInfo) => {
    console.log(userInfo);
  });
});
```

저 url에는 posts에서 user의 Id 목록이 쭉 있다.
Id 목록에서는 어떤 데이터가 있고, 그 안에는 userId라는 데이터가 들어있다.

그리고 url의 users에는 userId를 이용해서 user 정보들을 얻을 수 있다.

이를 이용해서 처음 posts/1에서 Id를 이용해서 정보 가져오고, 그 가져온 정보 안에서 userId를 가지고 또 user의 정보를 가져오는 요청을 한 것이다.

실행되는 함수를 보면 get이라는 비동기 함수 안에 또 get이라는 비동기 함수가 있다.

여기서 안에 있는 get 함수는 첫번째 요청이 성공했을 경우에만 동작하는 콜백 함수인 것이다(인자로 전달).

그럼 벌써부터 가독성이 좋지 않고, 실수를 유발하는 경우가 많아진다.
또한 디버깅이 쉽지 않다.

**이를 극복하기 위해 나온 것이 ES6에서 도입된 Promise이다.**

**2. 프로미스(Promise)**

(1) 프로미스란?

> Promise는 비동기 처리 상태와 처리 결과를 관리하는 객체로, 호스트 객체가 아닌 ECMAScript 사양에 정의된 표준 빌트인 객체이다.

- Promise의 생성
  <br>new 연산자를 통해 Promise 객체를 생성할 수 있다.
  <br>Promise 생성자 함수는 비동기 처리를 수행할 콜백 함수(resolve/성공 시, reject/실패 시)를 인수로 전달 받는다.

- Promise의 상태
  <br>pending: 비동기 처리가 아직 수행되지 않은 상태
  <br>fulfilled: 비동기 처리가 수행된 상태(성공) -> resolve 함수 호출
  <br>rejected: 비동기 처리가 수행된 상태(실패) -> reject 함수 호출

기본적으로 Promise 생성 직후는 pending 상태이고, 처리가 되면 settled 상태가 되어 그 결과에 따라 fulfilled, rejected로 상태가 변경된다.

(2) 앞의 get 함수를 promise로 구현

```jsx
const promiseGet = (url) => {
  // Promise를 생성하고 반환
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.send();

    xhr.onload = () => {
      if (xhr.status === 200) {
        resolve(JSON.parse(xhr.response));
      } else {
        reject(new Error(xhr.status));
      }
    };
  });
};

promiseGet("https://getmyinfo.com/posts/1");
```

promiseGet 또한 비동기 함수이므로 비동기 처리 결과는 안에서 처리 해주어야 한다.
<br>여기서는 promise 객체를 생성해서, 인수로 전달받은 콜백 함수 resolve, reject가 비동기 처리 결과를 수행한다.

그래서 promiseGet 함수를 통해서 비동기 처리를 하고, promise를 반환 받았는데, 어떻게 결과를 알 수 있을까?

**후속 메서드 then, catch, finally를 이용하여 처리**한다.

이 후속 메서드 또한 프로미스를 반환하고, 비동기로 동작한다.

(3) 프로미스 체이닝

```jsx
promiseGet("https://getmyinfo.com/posts/1")
  .then((res) => console.log(res)) // resolve의 결과값을 console로 찍는다.
  .catch((err) => console.log(err)) // error 났을 때 catch
  .finally(() => console.log("Finally")); // promise 상태가 fulfilled, rejected에 상관없이 무조건 호출
```

이를 프로미스 체이닝이라고 한다.

(4) 프로미스 체이닝을 통해 콜백 헬 해결

```jsx
const promiseGet = (url) => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.send();

    xhr.onload = () => {
      if (xhr.status === 200) {
        resolve(JSON.parse(xhr.response));
      } else {
        reject(new Error(xhr.status));
      }
    };
  });
};

promiseGet("https://getmyinfo.com/posts/1")
  .then(({ userId }) => promiseGet(`https://getmyinfo.com/users/${userId}`))
  .then((userInfo) => console.log(userInfo))
  .catch((err) => console.error(err))
  .finally(() => console.log("성공했든 안했든 일단 비동기 처리는 다 끝남"));
```

사실 Promise도 콜백 패턴을 사용하는 것이긴 하다.

프로미스는 프로미스 체이닝을 통해 비동기 처리 결과를 전달받아 후속처리를 하므로 비동기 처리를 위한 콜백 패턴에서 발생하던 콜백 헬이 발생하지 않는다.

Promise 또한 콜백 패턴을 사용하는 것은 맞으므로 가독성이 아주 좋지는 않다.

따라서 ES8에서 도입된 async/await을 통해 이 문제를 해결할 수 있다.

(5) 마이크로태스크 큐

```jsx
setTimeout(() => console.log(1), 0);

Promise.resolve()
  .then(() => console.log(2))
  .then(() => console.log(3));
```

// 2
<br>// 3
<br>// 1

모두 다 비동기로 동작하기 때문에 1 2 3이 나올 것 같지만 아니다.

프로미스의 후속처리 메서드의 콜백 함수는 태스크 큐(일반적인 콜백 큐)가 아니라 **마이크로태스크 큐에 저장**되기 때문이다.

태스크 큐와 마이크로태스크 큐는 별도의 큐이며 **마이크로태스크 큐의 우선순위가 더 높다**.

그러니까, 이벤트 루프는 콜 스택이 비면 마이크로태스크 큐에서 대기하고 있는 콜백 함수를 가져와서 실행해준다.

따라서 콜백 큐에 있는 콜백 함수는 콜 스택이랑 마이크로태스크 큐가 모두 비어있어야 가져가서 실행될 수 있다.

(6) fetch api

> XMLHttpRequest 객체와 마찬가지로 HTTP 요청 전송 기능을 제공하는 클라이언트 사이드 Web api 이다.
> <br>fetch 함수에는 HTTP 요청을 전송할 URI와 HTTP 요청 메서드, HTTP 요청 헤더, 페이로드 등을 설정한다.

fetch는 Response 객체(HTTP 응답)를 래핑한 Promise 객체를 반환한다.
<br>따라서, Promise로 반환이 되므로 체이닝을 이용해서 후속 처리를 해줄 수 있다.

fetch api의
<br>첫번째 인자는 **url**,
<br>두번째 인자로 **(HTTP 요청 메서드, HTTP 요청 헤더, 페이로드) 객체**
<br>를 전달한다.
<br>두번째 인자없이 url만 전송하면 GET 요청이 된다.

```jsx
const promise = fetch(url [, options])

const post = (url, payload) => {
  return fetch(url, {
    method: 'POST',
    headers: {'content-Type': 'application/json'},
    body: JSON.stringify(payload)
  })
}
```
