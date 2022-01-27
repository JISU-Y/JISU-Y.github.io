---
layout: single
title: "[TIL] Javascript - 생성자 함수"
categories: Javascript
tag: [TIL, Javascript, 생성자 함수]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## Javascript 개념

### 생성자 함수

#### 생성자 함수란?

> new 연산자와 함께 호출하여 객체(인스턴스)를 생성하는 함수.
> <br>자바스크립트는 Object, String, Number, Boolean, Funtion, Array ... 등의 빌트인 생성자 함수(built-in constructor)를 제공한다.

<aside>
  💡 인스턴스 : 생성자 함수에 의해 생성된 객체
</aside>

<br>

객체 리터럴 이외에도 생성자 함수를 사용하여 객체를 생성할 수 있다.

생성자 함수는 정해져 있는 형식이 없어 보통 파스칼 컨벤션(생성자 함수는 첫 문자를 대문자로 명명하는 것)을 적용한다.

일반 함수를 정의하고 new 연산자와 함께 함수를 호출하면 생성자 함수로 동작한다.

- 객체 리터럴에 의한 객체 생성의 단점

  직관적이지만, 동일한 프로퍼티를 갖는 객체를 여러 개 생성하는 경우 같은 코드를 계속 작성해야 한다.

  ```jsx
  const circle1 = {
    radius: 5,
    getDiameter() {
      return 2 * this.radius;
    },
  };

  console.log(circle1.getDiameter()); // 10

  const circle2 = {
    radius: 10,
    getDiameter() {
      return 2 * this.radius;
    },
  };

  console.log(circle2.getDiameter()); // 20
  ```

- 생성자 함수에 의한 객체 생성의 장점

  클래스 처럼 프로퍼티 구조가 동일한 객체를 여러 개 생성하기 좋다.

  ```jsx
  // 생성자 함수
  function Circle(radius) {
    // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
    this.radius = radius;
    this.getDiameter = function () {
      return 2 * this.radius;
    };
  }

  // 인스턴스의 생성
  const circle1 = new Circle(5); // 반지름이 5인 Circle 객체를 생성
  const circle2 = new Circle(10); // 반지름이 10인 Circle 객체를 생성

  console.log(circle1.getDiameter()); // 10
  console.log(circle2.getDiameter()); // 20
  ```

### 생성자 함수의 인스턴스 생성 과정

생성자 함수는 함수 내부에서 인스턴스를 생성하고, 생성된 인스턴스를 초기화(프로퍼티 추가 및 초기값 할당)해야 한다.

1. 인스턴스 생성과 this 바인딩

   함수 코드가 실행되는 런타임 이전에 실행된다.

   인스턴스가 될 빈 객체 생성 → 객체를 this에 바인딩

   <aside>
     💡 바인딩: 식별자와 값을 연결하는 과정
     변수 선언 시 변수 이름(식별자)와 확보된 메모리 공간의 주소를 바인딩한다.
     생성자 함수에서는 this 식별자를 객체와 바인딩한다.
   </aside>

2. 인스턴스 초기화

   함수 코드가 실행되면 진행된다.
   this에 바인딩되어 있는 인스턴스를 초기화(프로퍼티/메서드 추가, 값 할당)
   초기화는 필수는 아니다.

3. 인스턴스 반환

   함수 내부 처리가 끝나면 this(인스턴스와 바인딩)가 암묵적으로 반환된다.

   <aside>
     💡 return에 다른 객체를 명시할 경우, this를 반환하지 않고 return에 명시한 객체가 반환.
     return에 원시 값을 명시할 경우, 원시 값을 무시하고 this를 반환.
     생성자 함수 내부에는 return문을 생략해야 한다.
   </aside>

   ```jsx
   function Circle(radius) {
     // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

     // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
     this.radius = radius;
     this.getDiameter = function () {
       return 2 * this.radius;
     };

     // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다
   }

   // 인스턴스 생성. Circle 생성자 함수는 암묵적으로 this를 반환한다.
   const circle = new Circle(1);
   console.log(circle); // Circle {radius: 1, getDiameter: ƒ}
   ```

### 내부 메서드 [[Call]], [[Construct]]

> 함수 객체에는 일반 객체의 내부 슬롯/메서드 뿐만 아니라 추가로
> 함수 객체를 위한 내부 슬롯([[Enviroment]], [[FormalParameters]]), 내부 메서드([[Call]], [[Construct]])가 있다.

함수가 호출되면 기본적으로 내부 메서드 Call이 호출되고,
new 연산자와 함께 함수가 호출되면 내부 메서드 Constructor가 호출된다.

- constructor와 non-constructor
  > 함수 객체는 [[Call]] 내부 메서드를 가지기 때문에 callable하다.
  > 하지만, 모든 함수 객체가 [[Construct]]를 가지지는 않는다.
  > [[Construct]]를 가지면 constructor, 없으면 non-constructor 라고 한다.

자바스크립트에서는 함수 정의 방식에 따라 constructor와 non-constructor를 구분한다.

- constructor: 함수 선언문, 함수 표현식, 클래스

  ```jsx
  // 함수 표현식
  function foo() {}
  // 함수 선언문
  const bar = function () {};
  // 프로퍼티 x의 값으로 할당된 함수 => 메서드 X
  const baz = {
    x: function () {},
  };

  // 모두 constructor
  new foo(); // -> foo {}
  new bar(); // -> bar {}
  new baz.x(); // -> x {}
  ```

- non-constructor: 메서드 정의, 화살표 함수

  ```jsx
  // 화살표 함수 정의
  const arrow = () => {};
  // 메서드 정의: ES6의 메서드 축약 표현만을 메서드로 인정한다.
  const obj = {
    x() {},
  };

  // 모두 non-constructor
  new arrow(); // TypeError: arrow is not a constructor
  new obj.x(); // TypeError: obj.x is not a constructor
  ```

### new.target

> new 연산자 없이 호출되는 것을 방지하기 위해 파스칼 컨벤션을 사용 이외에 위험성을 피하기 위해 new.target (ES6)를 지원한다.
> 함수 내부에서 암묵적으로 지역 변수와 같이 사용되며 메타 프로퍼티라고 한다.

new 연산자와 함께 생성자 함수로 호출된 경우 함수 내부의 new.target은 함수 자신을 가리킨다.
일반 함수로 호출된 경우 new.target은 undefined이다.

- 스코프 세이프 생성자 패턴 (scope-safe constructor)
  최신 문법 적용할 수 없을 시 스코프 세이프 생성자 패턴을 사용할 수 있다.
  this instanceof Constructor를 이용하여 확인한다.
