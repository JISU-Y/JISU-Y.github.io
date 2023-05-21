---
layout: single
title: "[Javascript] Javascript Generator"
categories: Javascript
tag: [Javascript, generator]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# 제너레이터

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 제너레이터는 자바스크립트 처음 배울 때도 뭔지 몰랐는데, 지금도 잘 모르겠는 개념이다.

아마 Redux-saga에서 사용한 것이 아니면 코드 단에서 잘 활용할 일이 없어서 그런 것 같다.

이번에 헷갈렸던 개념을 잡고 가보자.

</aside>

## 제너레이터란?

> 함수 실행을 중지했다가 필요한 시점에 재개할 수 있도록 하는 특수한 함수  
> `es6`에서 도입.

### 일반 함수와는 무엇이 다를까?

- 함수 호출자에게 **함수 실행 제어권 양도**
  - 일반 함수
    함수 호출 시 제어권이 함수에게 넘어감.  
     따라서 함수 코드는 일괄적으로 모두 실행.  
     (한번 시작하면 멈출 수 없어~~)
  - 제너레이터 함수
    **함수 호출자가 제어 가능**.  
     (일시 중지 혹은 재개)
    ⇒ 이러한 제어권을 함수가 독점하지 않고 함수 호출자도 양도 받게 되는 것.  
     (그래서 `yield` 키워드 사용)
- 제너레이터 **함수 - 함수 호출자 상태 주고 받음**
  - 일반 함수
    매개변수를 통해 함수 외부 값 주입 받음.  
     이후 함수 내부 로직에 따라 실행하고 그 결과를 외부로 반환.  
     (중간에 외부와 주고 받을 수 없음)
  - 제너레이터 함수
    호출자와 양방향으로 함수 상태 주고 받을 수 있음.  
     (yield 키워드 앞에 변수 할당, 제너레이터 객체 내에 value 프로퍼티)
- **제너레이터 객체 반환**
  - 일반 함수
    함수 로직에 따른 결과 값을 반환.
  - 제너레이터 함수
    함수 코드 실행이 아닌 `이터러블`이면서 `이터레이터`인 제너레이터 객체 반환.  
     제너레이터 객체를 통해 value와 함수가 동작 완료 여부를 알 수 있음.

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
👉 <strong>이터레이션 인터페이스 (Iteration Interface)</strong> 용어 정리

_ECMAScript 명세_

- **이터러블 (Iterable)**
  `@@iterator`(`Symbol.Iterator`)를 프로퍼티로 포함하는 객체.  
  `Symbol.Iterator`: Iterator 객체를 반환하는 함수.
- **이터레이터 (Iterator)**
  이터레이터 리절트 객체를 반환하는 `next` 메서드를 가지고 있는 객체.  
  `const iterResult = 뭐뭐뭐.next()`
- **이터레이터 리절트 (Iterator Result)**
`done`과 `value` 프로퍼티를 갖는 객체.  
 Iterator 끝에 도달하면 done은 true, 도달하지 않으면 false를 반환.  
 ⇒ 이를 이용하여 순회에 이용
</aside>

## 제너레이터 함수의 정의

- `function*` 키워드로 선언. (애스터리스크(`*`))
- 함수 표현식으로만 정의 가능
- 화살표 함수로 정의 불가능
  - `const getFunc = ()* ⇒ {}`
- new 로 호출 불가능

## 제너레이터 호출

> 제너레이터 함수를 호출하면 **제너레이터 객체**를 생성하여 반환

### 제너레이터 객체

- **이터러블**: (`Symbol.iterator`) 메서드 상속 받음
- **이터레이터**: `value`, `done` 프로퍼티를 갖는 이터레이터 리절트 객체를 소유  
  제너레이터 객체는 추가적으로 `return`, `throw` 메서드를 갖기도 한다.

**제너레이터 객체의 메서드들 호출하면**

- `next` 메서드 호출

  - 제너레이터 함수의 `yield` 표현식까지(마다) 코드 실행
  - 이터레이터 리절트 객체 반환

    ```tsx
    function* fn() {
      yield "yield 키워드 뒤에 전달한 값";
    }
    const generator = fn();

    const genNextValue = generator.next();
    // { value: “yield 키워드 뒤에 전달한 값”, done: false }
    const genFinalValue = generator.next();
    // { value: undefined, done: true }
    ```

- `return` 메서드 호출
  - 인수로 전달받은 값을 value 프로퍼티, done은 true가 되어 이터레이터 리절트 객체 반환
  - 이터레이터 리절트 객체 반환
    ```tsx
    const genReturnedValue = generator.return("인수로 전달받은 값");
    // { value: “인수로 전달받은 값”, done: true }
    ```
- `throw` 메서드 호출

  - 인수로 전달받은 에러 발생
  - 이터레이터 리절트 객체 반환

    ```tsx
    funtion* fn() {
    	try {
    		yield "yield 키워드 뒤에 전달한 값"
    	}
    	catch(error) {
    		console.log(error)
    	}
    }
    const generator = fn()

    const getThrownValue = generator.throw("Error")
    // { value: undefined, done: true }
    ```

❗이터러블하다고 하는 문자열도 next 메서드가 존재

```tsx
const helloString = "HELLO";

const iteratorMethodOfString = helloString[Symbol.iterator]();

iteratorMethodOfString.next(); // { value: "H", done: false }
iteratorMethodOfString.next(); // { value: "E", done: false }
iteratorMethodOfString.next(); // { value: "L", done: false }
iteratorMethodOfString.next(); // { value: "L", done: false }
iteratorMethodOfString.next(); // { value: "O", done: false }
iteratorMethodOfString.next(); // { value: undefined, done: true }
```

## 제너레이터 일시 중지 및 재개

> `yield` / `next` 메서드를 통해 일시 중지(suspend) 및 재개(resume) 가능

제너레이터는 `next` 메서드를 호출하면 모든 코드를 실행하는 것이 아니라 `yield` 표현식까지만 실행.

```tsx
const genFunc = function* () {
	try {
		yield 1;
		yield 2;
		yield 3;
	}
	catch {
		console.log(error)
	}
}

const generator = genFunc() // 제너레이터 객체 생성 (제너레이터 함수를 호출하면 객체 반환)

0console.log(generator.next()) // 1 X // { value: 1, done: false }
console.log(generator.next()) // 2 X // { value: 2, done: false }
console.log(generator.next()) // 3 X // { value: 3, done: true }
```

외부에서 값 입력받기 / 가져오기

```tsx
function* genFunc() {
  const num1 = yield "첫번째 숫자 가져오기"; // 할당 X / yield만 하고 나감
  console.log(num1); // undefined

  const num2 = yield "두번째 숫자 가져오기";
  console.log(num2);

  return num1 + num2;
}

const gen = genFunc();

console.log(gen.next().value);
// "첫번째 숫자 가져오기"
console.log(gen.next(1).value);
// num1 콘솔 찍힘 // 값: 1
// "두번째 숫자 가져오기"
console.log(gen.next(2).value);
// num2 콘솔 찍힘 // 값: 2
// 3 // done: true
```

제너레이터 내부에서 제너레이터 불러오기

- (일괄적으로 불러오기)

```tsx
function* genFunc1() {
  yield "W";
  yield "O";
  yield "R";
  yield "L";
  yield "D";
}

function* genFunc2() {
  yield "Hello, ";
  yield* genFunc1();
  yield "!";
}

console.log(...genFunc2()); // Hello, W O R L D !
// 이터러블이므로 ...spread 사용 가능
// spread 사용 시 순회하므로 모든 데이터 꺼내옴.
// yield* 는 순회가능한 모든 객체를 넣을 수 있고 일괄적으로 다 가져옴
```

## 제너레이터의 활용

### 비동기 처리

프로미스를 사용한 비동기처리를 동기처럼 구현 가능

프로미스 후속 처리 메서드 then catch finally 없이 비동기 처리 결과 반환 가능하도록 구현

ex) redux-saga ⇒ 라이브러리 자체를 알아보지는 않겠음.

### 지연 평가를 이용한 배열 순회 시간 단축 → 배열 자체를 다 안 만듦.

**제너레이터는 값을 미리 만들지 않기 때문에 공간 절약 및 속도도 빨라진다.**

- 1부터 특정 수까지 (여기서는 100)를 가지는 배열에서 첫번째와 두번째 5의 배수만 배열로 꺼내오는 로직 ( `[5, 10]` )

  ```tsx
  // 일반 함수
  function makeArray(n) {
    let i = 1;
    const arr = [];
    while (i < n) arr.push(i++);
    return arr;
  }

  // 제너레이터 함수
  function* makeArrayGenFunc(n) {
    let i = 1;
    while (i < n) yield i++;
  }

  // 배열에서 첫번째와 두번째 5의 배수의 배열을 반환해주는 함수
  function getfirstAndSecondFiveMultiples(iter) {
    const resultArr = [];
    for (const item of iter) {
      // 제너레이터 함수 같은 경우는 순회하면서 조건 검사
      if (item % 5 == 0) resultArr.push(item); // 1, 2, 3, 4, 5, 6, 7 ....
      else if (resultArr.length == 2) break; // 결국엔 1 ~ 10 까지 밖에 안 만들어짐.
    }
    return resultArr;
  }

  console.log(getfirstAndSecondFiveMultiples(makeArray(100))); // [5, 10]
  console.log(getfirstAndSecondFiveMultiples(makeArrayGenFunc(100))); // [5, 10]
  ```

- 일반 함수는 기본적으로 `makeArray`는 1부터 100을 배열에 미리 다 담아두고 (이미 100번 돌음)
  그 배열을 `getfirstAndSecondFiveMultiples` 함수 인자로 전달하여 계산하여 반환
- 제너레이터는 미리 값을 생성하는 것이 아닌 `getfirstAndSecondFiveMultiples` 내부 로직 중
  `for … of` 문을 통해 `yield`에 있는 값들을 하나씩 가져온다.
  ⇒ 그렇기 때문에 100까지 돌 필요도 없이 10까지만 `iter`가 돌고 이미 5, 10 배열이 만들어지므로 빠르다.
  이를 **지연평가**라고 함. (값이 필요할 때 생성되는 것)

      ex) 이를 이용해 피보나치 수열 생성함수를 만들기도 한다고 한다.

### 참고 링크

모던 자바스크립트 책 (p. 868)

[제너레이터 활용](https://bbaktaeho-95.tistory.com/80)

[redux-saga 참고](https://react.vlpt.us/redux-middleware/10-redux-saga.html)

<hr>
