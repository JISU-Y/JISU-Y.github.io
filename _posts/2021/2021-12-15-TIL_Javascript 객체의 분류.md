---
layout: single
title: "[TIL] Javascript - 객체의 분류"
categories: web
tag: [TIL, Javascript, 객체, 표준 빌트인 객체, 호스트 객체]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## Javascript 개념

### 객체의 분류

1. 객체의 분류

- **표준 빌트인 객체** (Bulit-in objects/native objects/global objects)

  > **ECMAScript 사양에 정의된 객체**
  > 어플리케이션 전역의 공통 기능을 제공한다.

  ECMAScript 사양에 정의되어 있는 객체이므로 자바스크립트 **실행 환경(nodeJS, 브라우저)과 관계없이 언제나 사용**할 수 있다.
  자바스크립트는 Object, String, Number, Boolean, Symbol, Date, Math, RegExp, Array, Map/Set, Funtion, Promise, JSON 등 40여 개의 표준 빌트인 객체를 제공한다.
  전역 객체(Gloabl object ≠ Global objects/표준 빌트인 객체)의 프로퍼티로 제공한다.
  → 전역 변수처럼 어디서든 별도의 선언 없이 사용이 가능하다.

- **호스트 객체** (Host object)

  > 자바스크립트 **실행 환경에서 추가로 제공**하는 객체

  - **브라우저** 환경

    Web API를 호스트 객체로 제공

    ex) DOM, BOM, Canvas, XMLHttpRequest, fetch, requestAnimationFrame, SVG 등등

  - **Node.JS**

    Node.JS 고유 API를 호스트 객체로 제공

- **사용자 정의 객체** (User-defined object)

  > 사용자가 직접 정의한 객체

  기본적으로 제공되는 객체가 아닌 객체

2. 원시값과 래퍼 객체

   문자열, 숫자, 불리언 등의 원시값이 있지만 표준 빌트인 생성자 함수로 String, Number, Boolean 등이 존재한다.

   왜 일까?

   **원시값에서도 편하게 프로퍼티나 메서드를 사용할 수 있도록** 도와주기 때문이다.

   ```jsx
   const str = "hello";

   console.log(str.length); // 5
   console.log(str.toUpperCase()); // HELLO
   ```

   str은 객체가 아니라 문자열 즉 원시값이다.
   그렇기 때문에 프로퍼티나 메서드를 가질 수 없는데도 마치 프로퍼티나 메서드가 있는 것 처럼 동작한다.

   **원시값을 마침표 표기법(혹은 대괄호 표기법)으로 접근하면 자바스크립트 엔진이 일시적으로 원시값을 연관된 객체로 변환**해주기 때문이다.

   이렇게 임시적으로 생성된 객체를 **래퍼 객체(wrapper object)**라고 한다.

   위 처럼 str이 .length를 만나면 String 생성자 함수의 인스턴스가 생성되고,
   [[StringData]] 내부 슬롯에 str 문자열이 할당된다.
   처리가 종료되면 내부 슬롯에 있던 원래의 문자열을 원시값으로 되돌리고,
   래퍼 객체는 가비지 컬렉션의 대상이 된다.

3. 전역 객체 (Gloabl object)

   > 코드가 실행되기 이전 단계에서 자바스크립트 엔진에 의해 가장 먼저 생성되는 특수한 객체. 어떤 객체에도 속하지 않는 **최상위 객체**이다.

   <aside>
     💡 표준 빌트인 객체를 global objects 라고도 부르는데, 이 전역 객체와는 다른 개념임을 주의!
   </aside>

- 전역 객체의 특징

  - **전역 객체는 표준 빌트인 객체, 호스트 객체, 선언된 변수와 전역 함수를 프로퍼티로 갖는다**.
  - 전역 객체는 의도적으로 생성할 수 없다. (생성자 함수가 주어지지 않는다.)
  - 런타임 환경에 따라 전역 객체의 이름은 달라진다.
  - 브라우저 환경 ⇒ window
  - Node.JS 환경 ⇒ global
  - 프로퍼티를 참조할 때 window/global을 생략할 수 있다.

  참고) globalThis : ES11에 도입된 식별자로서 환경과 관계없이 전역 객체를 가리킬 수 있도록 통일한 식별자이다.

  ```jsx
  // 브라우저 환경
  globalThis === this; // true
  globalThis === window; // true
  globalThis === self; // true
  globalThis === frames; // true

  // Node.js 환경
  globalThis === this; // true
  globalThis === global; // true
  ```

- 빌트인 전역 프로퍼티와 메서드

  > **전역 객체가 가지고 있는 프로퍼티와 메서드**

  식별자(window/global)를 생략할 수 있어 전역 변수와 함수처럼 사용할 수 있다.

  - 빌트인 전역 프로퍼티
    - Infinity : 무한대를 나타내는 숫자값 Infinity를 갖는다.
    - NaN: Not-a-Number. 숫자가 아님을 나타내는 값 NaN을 갖는다.
    - undefined: 원시 타입 undefined를 값으로 갖는다.
  - 빌트인 전역 함수

    - eval

      문자열을 인수로 전달받아 표현식 여부를 판단,
      **표현식이면** 런타임에 평가하여 **값을 생성**
      표현식이 **아니라면** 그 문자열 코드를 런타임에 **실행**

      ```jsx
      eval("1 + 2;"); // 3 (표현문이므로 값을 생성)
      eval("var x = 5;"); // undefined (표현문이 아니므로 값 생성 하지 않음)

      // var x = 5 가 런타임에 실행되었으므로 변수 x가 선언
      console.log(x); // 5
      ```

    - eval 함수 내에서 var를 사용할 경우, 기본의 스코프를 런타임에 동적으로 수정한다.

      ````jsx
      const x = 1

      function foo() {
        // 런타임 중 foo 함수의 스코프를 동적으로 수정한다.
        eval('var x = 2') // 원래 이 위치에 존재하던 코드처럼 동작
        console.log(x) // 2
      }

      foo()
      console.log(x) // 1
          ```

      - eval 함수 내에서 let, const 혹은 strict mode가 적용된 경우, 기존 스코프를 수정하지 않고 eval 함수 자체 스코프를 생성하여 동작

      ```jsx
      const x = 1
      const y = 10

      function foo() {
        'use strict'

        // 기존 스코프 수정하지 않고, 자체 스코프 생성하여 동작
        eval('var x = 2; console.log(x);') // 2

        eval('const y = 20; console.log(y);') // 20
        console.log(x) // 1
        console.log(y) // 10
      }

      foo()
      console.log(x) // 1
      ````

      eval 함수를 통해 입력받은 콘텐츠를 실행하는 것은 보안에 매우 취약하다.

      자바스크립트 엔진에 의해 최적화가 실행되지 않아서 처리 속도 느리다.

      **eval 함수 사용 금지**

      \- isFinite: 전달받은 인수가 유한수이면 true, 무한수이면 false를 반환
      (인수가 숫자 타입이 아닐 경우 타입 변환 후 검사)
      NaN은 false를 반환, null은 true를 반환
      \- isNaN: 숫자인지 아닌지를 확인하여 불리언 타입으로 반환
      (인수가 숫자 타입이 아닐 경우 타입 변환 후 검사)
      \- parseFloat: 문자열을 실수로 해석하여 반환
      \- parseInt: 문자열을 정수로 해석하여 반환
      \- encodeURI / decodeURI: URI를 받아 인코딩/디코딩
      \- encodeURIComponent / decodeURIComponent: URI 구성요소를 받아 인코딩/디코딩

  - 암묵적 전역
    > 선언 없이 식별자에 값을 할당했을 때, 전역 객체의 프로퍼티가 되고, 그것을 사용 시 자바스크립트 엔진이 window.y로 해석하여 전역 변수처럼 동작되게 하는 것.

  ```jsx
  var x = 10; // 전역 변수

  console.log(y); // ReferenceError: y is not defined

  function foo() {
    y = 20; // 선언하지 않는 식별자에 값을 할당
  }

  foo();
  console.log(x + y); // 30
  ```

  y = 20은 곧 window.y = 20이 된다.
  선언 없이 값을 할당하면, 전역 객체의 프로퍼티가 되기 때문이다.
  따라서, x+y가 실행될 때 자바스크립트 엔진은 알아서 x + window.y로 해석하여 동작한다.

  이를 **암묵적 전역**이라고 한다.

  그러나 변수가 아니므로 호이스팅은 발생하지 않는다.
  delete 연산자로 전역 객체의 프로퍼티를 삭제할 수 있다.
