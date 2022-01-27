---
layout: single
title: "[TIL] Javascript - Event Loop / React - useState"
categories: Javascript
tag: [TIL, Javascript, Event Loop, React, useState, 비동기]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## Javascript

### Event Loop

#### 브라우저 구조

![](https://images.velog.io/images/jisu129/post/51611753-b222-48a2-a23e-29eb3669489d/EventLoop.png)

Javascript 엔진은 저 메모리 힙과 콜 스택을 가지고 있다. 그리고 그것을 이용해서 자바스크립트로 쓰여진 코드를 해석하고 실행한다.

- 메모리 힙 : 구조화되지 않은 넓은 메모리 영역 (변수가 어디에 저장이 되어있는지/실행 컨텍스트가 참조하고 있는 것들이 저장되어 있는 곳)
- 콜 스택: 짜놓은 코드들의 실행 컨텍스트들이 쌓여서 하나씩 실행이 되는 곳. Call 이 Stack으로 쌓여져 있는 곳. 콜은 바로 Function을 호출하는 것을 뜻함. 콜 해주면 펑션이 실행되는 곳이다.
  <br>=> stack을 보면 어디 코드가 실행되고 있는지를 알 수 있다.
  <br>콜 스택이 하나이기 때문에 자바스크립트는 싱글 스레드 언어다라고 하는 것이다.

흰색 배경은 바로 브라우저를 뜻하는 것이다.

브라우저 안에 Javscript 엔진(크롬의 경우 V8)이 있고, Web에서 제공하는 API들(DOM api/document, AJAX/XMLHttpRequest, Timeout/setTimeout)과 바로 오늘 알아볼 Event Loop와 Callback queue라는 것이 있다.
<br>이것들 또한 브라우저 혹은 런타임(node JS)에 내장되어 있는 기능 중 하나라고 보면 된다.

- Callback queue: 콜백 펑션(다른 함수에 인자로 전달되어진 함수)를 쌓아놓는 자리.
- Event Loop: 콜 스택과 콜백큐를 모니터하면서 콜 스택이 비어있으면 콜백 큐에서 이벤트를 하나 던져주어 실행되게 하는 역할을 한다.

**문제는 어떻게 싱글 스레드 언어인 자바스크립트에서 비동기적인 처리가 가능하게 될까? 라는 것이다.**

중요한 것은, 자바스크립트만으로는 비동기를 처리할 수 없다고 보면 될 것 같고, 브라우저나 런타임 내장 기능인 **이벤트 루프가 비동기 처리를 가능하게 해준다**고 보면 된다.

#### 간단한 예제와 Event Loop의 동작

예제로 한번 이해를 해보겠다.

```
console.log('Hi');

setTimeout(function cb1() {
    console.log('call back1');
}, 5000);

console.log('Bye');
```

아주 간단한 코드이다.

결과는 우리 모두 다 알고 있을 것이다.

    Hi
    Bye
    (5초 뒤)
    call back1

그렇다면 이런 비동기 처리가 어떻게 되었는지 그림으로 살펴보자.

1. state clear
   ![](https://images.velog.io/images/jisu129/post/f22fd660-2c03-43c4-951c-f1861c54e8fc/1_EL.png)
   처음 상태로 브라우저 콘솔, 콜 스택, 콜백 큐 등 모두 비어있는 상태이다.

2. console.log('Hi') 스택에 추가
   ![](https://images.velog.io/images/jisu129/post/6d0b3995-278f-46b1-b0e1-509b897bec39/2_EL.png)
   console.log('Hi')가 call stack에 들어가게 된다.

3. console.log('Hi') 실행
   ![](https://images.velog.io/images/jisu129/post/30c30786-dfe2-4068-8142-d6529fd6ac81/3_EL.png)
   console.log('Hi')가 실행되어 브라우저 콘솔에 Hi라고 뜬다.

4. console.log('Hi') 제거
   ![](https://images.velog.io/images/jisu129/post/fb6c4539-513f-41aa-a82e-55bfbf6586e1/4_EL.png)
   console.log('Hi')가 실행되었으므로 콜 스택에서 제거된다.

5. setTimeout(function cb1() {...}, 5000) 스택에 추가
   ![](https://images.velog.io/images/jisu129/post/ac4b273c-5f53-4d57-8192-4373a374838c/5_EL.png)
   setTimeout(function cb1() {...}, 5000) 콜 스택에 추가된다.

6. setTimeout(function cb1() {...}, 5000) 실행
   ![](https://images.velog.io/images/jisu129/post/2a9c43e1-592d-4b64-afbb-25b819dd935f/6_EL.png)
   setTimeout(function cb1() {...}, 5000)가 실행된다. setTimeout은 브라우저에서 제공하는 api이므로 web api가 알아서 시간을 세준다. <br>(자바스크립트 실행 엔진은 브라우저에 수 세달라고 안에 다시 받을 함수만 담아서 요청한다.)

7. setTimeout(function cb1() {...}, 5000) 제거
   ![](https://images.velog.io/images/jisu129/post/825937c4-0cd1-4d6e-9c27-fceb6a74ab30/7_EL.png)
   자바스크립트 엔진 입장에서는 setTimeout이라는 api 요청은 끝났기 때문에 할 일 끝. <br>따라서 콜 스택에서 제거된다.

8. console.log('Bye') 스택에 추가
   ![](https://images.velog.io/images/jisu129/post/c07e4608-0e94-4610-8256-83518224f075/8_EL.png)
   console.log('Bye') 가 콜 스택에 추가된다.

9. console.log('Bye') 실행
   ![](https://images.velog.io/images/jisu129/post/0d5361ed-6b51-499f-abae-64a524f52cd4/9_EL.png)
   console.log('Bye') 실행되어 브라우저 콘솔에 Bye라고 뜬다.

10. console.log('Bye') 제거
    ![](https://images.velog.io/images/jisu129/post/a290ae27-c2a1-4bab-b6e3-6769edc41bdb/10_EL.png)
    console.log('Bye')가 실행되었으므로 콜 스택에서 제거된다.
    <br>이 와중에도 web api는 timer를 통해 요청받은 시간을 세고 있다...

11. 콜백 큐에 cb1 추가
    ![](https://images.velog.io/images/jisu129/post/a99ae2e2-e29c-4f50-94a4-32a50412aed9/11_EL.png)
    Web api가 5000ms를 다 세고 나서 cb1이라는 요청 받을 때 온 함수를 콜백 큐에다가 집어넣는다.

12. 이벤트 루프가 cb1을 콜 스택에 추가
    ![](https://images.velog.io/images/jisu129/post/0f0adfa7-2694-4df9-9137-e694a06b100a/12_EL.png)
    이벤트 루프가 콜백 큐에 추가된 함수를 콜 스택에 추가한다.<br>(이벤트 루프는 콜 스택도 확인하여 비어있을 때 콜백 큐에서 함수를 꺼내서 스택에 추가한다.)

13. cb1 함수 실행
    ![](https://images.velog.io/images/jisu129/post/f50b8b06-0b89-462a-a308-27e086f5e359/13_EL.png)
    콜 스택에 있는 cb1이라는 함수를 실행한다. <br>그 안에는 console.log('call back1')이 있으므로 해당 실행 컨텍스트를 또 콜 스택에다 추가한다.

14. console.log('call back1') 실행
    ![](https://images.velog.io/images/jisu129/post/b4df6075-e236-46b7-80f7-ee440149211d/14_EL.png)
    콜 스택에 console.log('call back1')가 가장 위에 있으므로 실행되어 브라우저 콘솔에 call back1가 뜨게 된다.

15. console.log('call back1') 제거
    ![](https://images.velog.io/images/jisu129/post/d071d9e3-4078-4eb9-b034-18ef990c9e50/15_EL.png)
    console.log('call back1')가 실행되었으므로 콜 스택에서 제거된다.

16. cb1 함수 제거
    ![](https://images.velog.io/images/jisu129/post/2ffeb6d6-7ff7-4842-953a-cf0accdfddc1/16_EL.png)
    cb1는 이미 실행되었으므로 콜 스택에서 제거된다.

#### Tricky part

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 또 하나 중요 포인트:
<br>그럼 setTimeout에 지정해둔 콜백 함수와 시간이 있으면, 특정 시간이 지나면 무조건 콜백 함수가 "실행"되는 것일까?
<br>
<br>
<strong>는 아님.</strong>
<br>
그저 web api에서 전달 받은 시간을 다 세면 콜백 큐에 넣어주는 것이지, 실행이 되는 것이 아니다.
</aside>

<br>

- 예시 1

  ```jsx
  setTimeout(myCallback, 1000);
  ```

  이런 코드가 있다면, 1000ms 뒤에 (1초 뒤에) myCallback이라는 함수를 실행시켜줘 라기보다.
  <br>1초 뒤에 내가 준 함수(콜백 펑션)를 콜백 큐에 추가해줘 라는 것이다.
  <br>그러니 만약 콜 스택에서 굉장히 오래 걸리는 작업을 하고 있다고 한다면, 바로 1초 뒤에 "실행"되지 않는 것이다.(콜백 큐에 저장되어 실행되기를 기다리고 있는 것)

  그럼 이건?

- 예시 2

  ```jsx
  setTimeout(myCallback, 0);
  ```

  어림없다. <br>0초 뒤에 바로 실행가능한 것이 아니라 0초 뒤에 콜백 큐에 추가되는 것이다. <br>이미 콜 스택에서는 이 setTimeout이 나오면 web api에 요청을 하고 콜 스택을 비워버리고 다음 실행을 넘어간다. <br>따라서 0초라고 해도 다음 라인이 먼저 실행되게 된다.

  만약 그래서 콜백안에 콜백안에 콜백이 있다면..?

- 예시 3
  ```jsx
  listen("click", function (e) {
    setTimeout(function () {
      ajax("https://api.example.com/endpoint", function (text) {
        if (text == "hello") {
          doSomething();
        } else if (text == "world") {
          doSomethingElse();
        }
      });
    }, 500);
  });
  ```
  이것을 콜백 지옥이라고 부르는데(nested callbacks / callback hell),
  <br>document addEventListner인 DOM api니까 안에 담겨진 내용과 같이 web apis로 보내고,
  그럼 스택은 비워졌으니 web apis가 저 할일(버튼 클릭)이 끝나면 큐로 넘겨주어서 스택에는 setTimeout이 추가되고, 그럼 얘를 또 web apis로 보내고, 500ms 기다리는 도중에 연산이 조금이라도 늦은 게 실행되면 더더 늦어지고,
  스택 비워져서 setTimeout 안에 있던 콜백 펑션 ajax가 스택에 추가되자마자 또 web apis로 요청하고....
  <br>원하는 요청이 제 시간에 오지 않아 에러를 일으킬 수 있고, 딱봐도 성능에 좋지 않다.

따라서 이를 해결하기 위한 방법으로 Promise, 최근에 나온 async, await 가 있다.

[참고 및 출처]
https://blog.sessionstack.com/how-javascript-works-event-loop-and-the-rise-of-async-programming-5-ways-to-better-coding-with-2f077c4438b5
https://www.youtube.com/watch?v=zi-IG6VHBh8

## React 관련

### useState

#### useState는 동기 인가 비동기인가

**useState는 비동기적으로 동작한다.**

이벤트 핸들러 내에 setState가 여러번 호출이 된다면, 일괄적으로 업데이트하고 렌더링한다.

state1 set하고 state2 set 하고 동기적으로 처리된다면 느려질 것이기 때문이다.

```jsx
import React, { useState } from "react";

function App() {
  const [num, setNum] = useState(1);

  async function plus() {
    setNum(num + 1);
    setNum(num + 1);
    setNum(num + 1);
  }

  async function minus() {
    setNum(num - 1);
  }

  return (
    <div className="App">
      <h1>{num}</h1>
      <button onClick={plus}>PLUS</button>
      <button onClick={minus}>MINUS</button>
    </div>
  );
}

export default App;
```

setNum이 세번 사용이 되었다고 해서 num에다가 3이 더해지는 것은 아니다.
<br>한 이벤트 핸들러 내에서는 비동기적으로 동작하기 때문에, 1만 증가하게 된다.
<br>이 동일한 state를 업데이트 하는 경우 react는 이 동작들을 batch 처리한다.
<br>batcting이란, setState를 하나로 병합한 후 최종적으로 한번만 setState 해주는 것이다.

state가 변경이 되면 re-render를 하게 되는데, 계속 렌더링이 일어나면 성능에 좋지 않으므로 state가 변경된 후 반영하는 것은 대기열에 넣은 후 한꺼번에 반영하도록 되어 있다.

따라서 개발자가 의도하지 않은 값을 참조하게 될 수도 있다.

<br>

- 해결 방법:

  1. 최신 state 값을 참조하고 싶으면 업데이트 후에 하면 된다.
     <br>따라서 useEffect, componentDidUpdate를 사용해서 최신 state 값을 볼 수 있다.

  2. 함수형 업데이트
     <br>setState가 비동기적으로 수행될 때, 값을 set 하지 말고, 업데이트된 최신의 state와 함께 함수를 전달하는 방법이 있다.
     <br>여러번 전달받는 함수들은 큐에 저장되어 순서대로 실행된다. <br>따라서 큐에서 먼저 수행된 함수의 결과로 반영된 state값이 다음 수행할 함수의 인자로 들어가게 되므로, 항상 최신의 state를 유지하게 된다.<br>
     <br>이렇게 바꾸란 얘기
     `jsx async function plus() { setNum(num => num + 1) setNum(num => num + 1) setNum(num => num + 1) } `

[참고 및 출처]
https://baegofda.tistory.com/220
https://garve32.tistory.com/39

### Git

git online repo가 연결된 것이 있는데 다른 repo를 또 만들어서 연결하고 싶으면??
그때는 git remote set -url origin [옮길 주소] 를 하고 push 하면 된다!
