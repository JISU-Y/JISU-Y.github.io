---
layout: single
title: "[TIL] javascript 개념"
categories: web
tag: [TIL, javascript, var, let, const, hoisting, closure]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## 오늘 한 것

오늘은 이제 알고리즘은 그만풀기로하고, 너무 시간도 많이 잡아먹고 어차피 여기서 원하던 것은 해당 언어에 익숙해지는 것을 원했던 것이니 그 정도는 되었다고 생각하기 때문에 우리 팀은 다른 것을 하기로 했다.

각자 주제를 정해서 20분 정도 발표를 하는 것!
나는 클로저에 대해 공부하고 발표했다. 와 처음에는 이거 어떻게 이해하고 알려주지라는 생각이었는데, 책임감을 가지고 이해하다보니 많이 알아보기도 하고 이해할때까지 붙잡고 있던 것이 도움되었다.

내일도 이런 유익한 시간을 가지기로 함!!

<hr>

## 오늘 배운 것

### Javascript 특징 및 개념

#### 변수와 호이스팅(Hoisting)

호이스팅은 **변수와 함수 선언이 파일의 맨 위로 끌어올려진 것처럼 동작**하는 것을 말한다.

> 자바스크립트 엔진은 코드를 실행하기 전 실행 가능한 코드를 형상화하고 구분하는 과정을 거친다.<br>그 과정에서 모든 선언(var, let, const, function, class)을 메모리에 먼저 저장한다. <br>코드 실행하기 전에 이러한 동작을 하기 때문에 선언보다 참조, 호출이 먼저 있어도 오류가 나지 않는다(자세히 말하자면 var만)

- 변수 생성 단계

1.  선언 단계(Declaration phase)
    - 변수를 실행 컨텍스트의 변수 객체에 등록한다.
    - 변수 객체는 스코프가 참조하는 대상
2.  초기화 단계(Initialization phase)
    - 변수 객체에 등록된 변수를 위한 공간을 메모리에 확보
    - var라면 원래부터 undefined로 초기화되어 있었겠지만, let은 이때 undefined로 초기화
3.  할당 단계(Assignment phase)
    - 변수에 실제 값을 할당

- 변수 호이스팅

  - 자바스크립트의 모든 선언에는 호이스팅이 일어난다.
  - let, const, class를 이용한 선언문은 호이스팅이 적용되지 않는 것처럼 보인다.
  - let 키워드로 선언된 변수는 스코프 시작에서 변수 선언까지 TDZ(Temporal Dead Zone;일시적사각지대)에 빠지기 때문이다.

  \* var : var로 선언이 되면 undefined로 바로 초기화되어 메모리에 저장되기 때문에, 호이스팅이 적용되면 어디서든 값에 접근할 수 있게 된다.
  <br>\* let, const : 하지만 이 둘은 초기화가 되지 않는 상태로 메모리에 저장이 되므로 호이스팅이 적용되나 할당을 해주지 않으면 선언 라인 위에서 사용할 수 없다.(const는 초기화 안하면 선언이 안되지만)

[참고 및 출처] https://hanamon.kr/javascript-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85%EC%9D%B4%EB%9E%80-hoisting/

- 추가 정보

  ```jsx
  const c = { a: 1, b: 10 };
  c.a = 100;
  console.log(c); // a: 100, b:10
  ```

  객체를 생성하여 const c를 선언하여 할당했다.
  그러고 나서 c의 a값을 100으로 재할당해준다면??
  놀랍게도 c의 a값은 바뀌어 있다.

  이것은 const c를 메모리를 저장하고, c.a가 저장되는 곳은 const c가 저장되어 있는 메모리가 아니라 c가 가리키는 다른 메모리 공간이기 때문이고, 그 공간은 const가 아니기 때문에 값을 고칠 수 있게 된다고 한다.!!!

#### 클로저(closure)

함수와 선언된 환경(어휘적 환경/Lexical environment)과의 조합.
함수가 생성될 당시의 외부 변수를 기억한다.

```jsx
if (1) {
  let f = function () {
    let x = 123;
  };

  f(); // 함수 실행
  console.log(x); // 에러 발생
  // if scope 안에 정의된 function f 안에 x가 선언되어 있기 때문에
  // 외부 scope(if scope)에서는 x를 참조할 수 없다.
  // 함수가 실행된 이후에 x의 값은 메모리에서 사라진다. (자바스크립트에서 알아서 없애줌)
}
```

여기서 함수가 실행된 이후에는 당연히 x의 값을 참조할 수 없다.

(내부 스코프에서 선언된 변수이므로 if 스코프 안에 x가 없으면 전역으로 가서 찾고 없으면 에러 발생)
<br>**그런데 저 x값을 사용하고 싶다면???**

```jsx
if (1) {
  let f = function () {
    let x = 123;
    let innerF = function () {
      return x;
    };
    return innerF;
  };

  let a = f(); // f 함수 실행 (변수 x 저장하고, innerF를 return) / a에 함수 innerF를 할당
  console.log(a()); // a 함수 실행 // 123 나옴
  // innerF는 여기서(밖에서) 접근이 불가능하다.
  // 하지만 return을 해줌으로써 안에 있는 data들을 볼 수 있게 된다.
  // 위에 처럼 함수 실행이 끝났다 하더라도 메모리에서 사라지지 않는다.
}
```

일반적으로 x는 그 스코프 내에서만 사용할 수 있다. 하지만 같은 스코프(f 스코프) 내에서 x를 사용하여(당연히 사용가능) return 해주는 function을 정의하고 그 function을 return 해준다.

그렇다면 변수 a를 선언하는 줄에 f 함수가 실행되고, a에는 f 함수에서 return된 innerF를 할당하게 된다.

이제는 함수 실행이 끝났으니까 x를 사용할 수 있을까?

여전히 x는 사용할 수 없다.(x라는 이름으로)
<br>하지만 x는 전처럼 메모리에서 사라지지 않는다. <br>(innerF에서 사용되어 어딘가로 반환되어 사용될 수 있는 값이므로)
<br>따라서 a 함수(innerF)가 실행되면 그 스코프 (innerF 함수 스코프)에 없던 x가 어디있는지 찾다가 <br>외부 스코프(f 함수 스코프)에 선언된(lexical 환경에 있던) x가 있으므로 x의 값을 제대로 return할 수 있게 된다.
<br>(그래서 console엔 123이 찍힘)

- 다른 예)

```jsx
// 전역 Lexical 환경
// makeAdder: function
// add3: 초기화X
function makeAdder(x) {
  return function (y) {
    // 이 함수는 y를 가지고 있고, 상위함수인 makeAdder의 매개변수 x에도 접근 가능
    return x + y;
  };
}

// makeAdder Lexical 환경
// x: 3
// 만약 여기 lexical 환경에 변수가 없으면 외부로, 또 없으면 전역으로 간다.
const add3 = makeAdder(3); // 매개변수 y를 가지는 함수 할당
// 익명함수(return된 함수) Lexical 환경
console.log(add3(2)); // 5
// add3 함수가 생성되고나서 3에는 접근 불가능한 것이 아니라
// lexical 환경에 올라가있는 3을 이용해서 더하는 연산을 완성할 수 있다.

const add10 = makeAdder(10); // add3과는 다른 lexical 환경을 가짐

console.log(add10(5)); // 15
console.log(add3(2)); // 5 // add3 환경에는 계속 매개변수 x에 3이라는 값이 저장되어 있기 때문에 makeAdder에 또 10을 집어넣어도 add3은 영향을 받지 않는다.
```

따라서 **클로저(Closure)**는

- 함수와 Lexical environment의 조합
- 함수가 생성될 당시의 외부 변수를 기억, 생성된 이후에도 접근 가능
  라고 정리할 수 있다.

\* counter 함수를 이용해서 closure와 아닌 것의 차이

- Closure (X)

  ```jsx
  let num = 0;

  function makeCounter() {
    return num++;
  }

  makeCounter();
  console.log(num); // 1
  makeCounter();
  console.log(num); // 2
  num = 90;
  makeCounter();
  console.log(num); // 91
  ```

  이처럼 num은 전역변수에 선언되었기 때문에 아무데서나 접근할 수 있고 counter 중간에 값이 바뀔 가능성이 있다.

- Closure

  ```jsx
  function makeCounter() {
    let num = 0; // 은닉화 // 얘는 외부에서 수정 불가능

    return function () {
      return num++;
    };
  }

  let counter = makeCounter();

  // 따로따로 접근하는데도 변수를 기억하고 있다.
  console.log(counter()); // 0
  console.log(counter()); // 1
  console.log(counter()); // 2
  ```

  밖의 스코프에서 따로따로 접근을 하고 있지만, makeCounter에서는 변한 값을 계속 기억하고 있다.
  <br>또한, makeCounter 안의 num은 외부에서 수정이 불가능하기 때문에 은닉화된 것이고, <br>이처럼 특정 데이터를 스코프 안에 가두어 둔 채로 계속 사용할 수 있게 하는 폐쇄성을 갖는다.(데이터 보호)

* 추가 예
  ```jsx
  let globalFunc;
  {
    let x = 10;
    globalFunc = function (y) {
      // globalFunc 함수는 클로저다.
      return (x = x + y);
    };
  }
  globalFunc(5); // 15;
  globalFunc(5); // 20;
  globalFunc(5); // 25;
  ```
  이렇게 먼저 전역변수로 선언을 한 뒤, function 정의를 {} 스코프 안에서 해주면 함수를 return한 형태가 아니더라도 closure 형태를 만들 수 있다.

[참고 및 출처] https://hanamon.kr/javascript-%ED%81%B4%EB%A1%9C%EC%A0%80/
[참고 및 출처] https://www.youtube.com/watch?
[참고 및 출처] v=tpl2oXQkGZs&list=PLZKTXPmaJk8JZ2NAC538UzhY_UNqMdZB4&index=11
https://www.youtube.com/watch?v=Um-CJHNc5Pw
