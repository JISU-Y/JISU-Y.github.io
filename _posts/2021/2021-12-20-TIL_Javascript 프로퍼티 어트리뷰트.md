---
layout: single
title: "[TIL] Javascript - 프로퍼티 어트리뷰트"
categories: Javascript
tag: [TIL, Javascript, 프로퍼티 어트리뷰트]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## Javascript 개념

### 프로퍼티 어트리뷰트

### 내부 슬롯과 내부 메서드

> 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 ECMAScript 사양에서 사용하는 pseudo property, pseudo method 이다.
> <br>흔히 보는 이중대괄호 [[...]] 안에 감싼 이름들이 내부 슬롯과 내부 메서드

자바스크립트 엔진에서는 동작하지만, 개발자가 직접 접근할 수 없다. (간접적으로만 제공)

모든 객체는 [[Prototype]]이라는 내부 슬롯을 갖는다.
내부 슬롯은 직접 접근할 수는 없지만 간접적으로 \_\_proto\_\_를 통해 간접적으로 접근할 수 있다.

```jsx
const o = {};

// 내부 슬롯은 자바스크립트 엔진의 내부 로직이므로 직접 접근할 수 없다.
o.[[Prototype]] // -> Uncaught SyntaxError: Unexpected token '['
// 단, 일부 내부 슬롯과 내부 메서드에 한하여 간접적으로 접근할 수 있는 수단을 제공하기는 한다.
o.__proto__ // -> Object.prototype
```

### 프로퍼티 어트리뷰트

프로퍼티 어트리뷰트란?

> 객체 프로퍼티의 상태를 나타내주는 내부 슬롯이다.
> [[value]], [[writable]], [[enumerable]], [[configurable]]가 있다.

자바스크립트 엔진은 객체의 프로퍼티를 생성할 때, 이 프로퍼티 어트리뷰트를 기본값으로 자동 정의해준다.

- 프로퍼티 디스크립터 객체

  내부 슬롯이기 때문에, 직접 .[[Value]]로 접근할 수는 없다.

  따라서, 간접적으로 접근할 수 있도록 Object.getOwnPropertyDescriptor 메서드를 제공한다.

  getOwnPropertyDescriptor: (객체 이름, 프로퍼티 키) 를 인수로 전달하여 **프로퍼티 디스크립터 객체를 반환**해준다.

  getOwnPropertyDescriptors: (객체 이름) 를 인수로 전달하여 모든 프로퍼티의 프로퍼티 디스크립터 객체들을 반환해준다.

  ```jsx
  const person = {
    name: "Lee",
  };

  // 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 반환한다.
  console.log(Object.getOwnPropertyDescriptor(person, "name"));
  // {value: "Lee", writable: true, enumerable: true, configurable: true}

  // 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체들을 반환한다.
  console.log(Object.getOwnPropertyDescriptors(person));
  /*
  {
    name: {value: "Lee", writable: true, enumerable: true, configurable: true},
    age: {value: 20, writable: true, enumerable: true, configurable: true}
  }
  */
  ```

### 데이터 프로퍼티와 접근자 프로퍼티

#### 데이터 프로퍼티

> 키와 값으로 구성된 일반 프로퍼티
> <br>데이터 프로퍼티 어트리뷰트는 자바스크립트 엔진이 프로퍼티 생성 시 기본값(value 제외 모두 true)으로 자동 정의한다.

- [[value]]: 프로퍼티 값을 반환하거나 재할당한다. 프로퍼티가 없을 시 동적으로 생성하여 값을 할당한다.
- [[writable]]: 프로퍼티 값의 변경 가능 여부를 Boolean 값으로 나타낸다.
- [[enumerable]]: 프로퍼티의 열거 가능 여부를 Boolean 값으로 나타낸다.
- [[configurable]]: 프로퍼티의 재정의 가능 여부를 Boolean 값으로 나타낸다.

#### 접근자 프로퍼티

> 객체의 데이터 프로퍼티의 값을 읽거나 설정할 때 사용하는 접근자 함수로 구성된 프로퍼티. 자체적인 값은 없다.

- [[Get]]: 접근자 프로퍼티로 데이터 프로퍼티 값을 읽을 때 호출되는 접근자 함수. 프로퍼티 값을 반환 (getter)
- [[Set]]: 접근자 프로퍼티로 데이터 프로퍼티 값을 저장할 때 호출되는 접근자 함수. 프로퍼티 값을 저장 (setter)
- [[enumerable]]: 프로퍼티의 열거 가능 여부를 Boolean 값으로 나타낸다.
- [[configurable]]: 프로퍼티의 재정의 가능 여부를 Boolean 값으로 나타낸다.

```jsx
const person = {
  // 데이터 프로퍼티
  firstName: "Ungmo",
  lastName: "Lee",

  // fullName은 접근자 함수로 구성된 접근자 프로퍼티
  // getter 함수
  get fullName() {
    // 데이터 프로퍼티를 읽어서 값을 반환
    return `${this.firstName} ${this.lastName}`;
  },
  // setter 함수
  set fullName(name) {
    // 인수로 받은 데이터를 이용해서 데이터 프로퍼티에 저장
    [this.firstName, this.lastName] = name.split(" ");
  },
};

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
console.log(person.firstName + " " + person.lastName); // Ungmo Lee

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
person.fullName = "Heegun Lee";
console.log(person); // {firstName: "Heegun", lastName: "Lee"}

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
console.log(person.fullName); // Heegun Lee

// firstName은 데이터 프로퍼티다.
// 데이터 프로퍼티 어트리뷰트: [[Value]], [[Writable]], [[Enumerable]], [[Configurable]]
let descriptor = Object.getOwnPropertyDescriptor(person, "firstName");
console.log(descriptor);
// {value: "Heegun", writable: true, enumerable: true, configurable: true}

// fullName은 접근자 프로퍼티다.
// 접근자 프로퍼티 어트리뷰트: [[Get]], [[Set]], [[Enumerable]], [[Configurable]]
descriptor = Object.getOwnPropertyDescriptor(person, "fullName");
console.log(descriptor);
// {get: ƒ, set: ƒ, enumerable: true, configurable: true}
```

### 프로퍼티 정의

> 프로퍼티 어트리뷰트를 명시적으로 정의하거나 기존 프로퍼티 어트리뷰트를 재정의하는 것

메서드를 이용하여 프로퍼티 어트리뷰트를 정의한다.

- Object.defineProperty:
  (객체 이름, 프로퍼티 키, 프로퍼티 디스크립터 객체) 를 인수로 전달하여 사용한다.
  디스크립터 객체에 프로퍼티를 누락시키면 value는 undefined, 나머지는 false로 정의된다.
- Object.defineProperties:
  (객체 이름, 프로퍼티 디스크립터 객체) 를 인수로 전달하여 모든 프로퍼티의 프로퍼티 어트리뷰트를 한꺼번에 정의할 수 있다.

```jsx
const person = {};

/* defineProperty */
// 데이터 프로퍼티 정의
Object.defineProperty(person, "firstName", {
  value: "Ungmo",
  writable: true,
  enumerable: true,
  configurable: true,
});

Object.defineProperty(person, "lastName", {
  value: "Lee",
});

let descriptor = Object.getOwnPropertyDescriptor(person, "firstName");
console.log("firstName", descriptor);
// firstName {value: "Ungmo", writable: true, enumerable: true, configurable: true}

// 디스크립터 객체의 프로퍼티를 누락시키면 undefined, false가 기본값
descriptor = Object.getOwnPropertyDescriptor(person, "lastName");
console.log("lastName", descriptor);
// lastName {value: "Lee", writable: false, enumerable: false, configurable: false}

// 접근자 프로퍼티 정의
Object.defineProperty(person, "fullName", {
  // getter 함수
  get() {
    return `${this.firstName} ${this.lastName}`;
  },
  // setter 함수
  set(name) {
    [this.firstName, this.lastName] = name.split(" ");
  },
  enumerable: true,
  configurable: true,
});

descriptor = Object.getOwnPropertyDescriptor(person, "fullName");
console.log("fullName", descriptor);
// fullName {get: ƒ, set: ƒ, enumerable: true, configurable: true}

/* defineProperties */
// 한꺼번에 정의
Object.defineProperties(person, {
  // 데이터 프로퍼티 정의
  firstName: {
    value: "Ungmo",
    writable: true,
    enumerable: true,
    configurable: true,
  },
  lastName: {
    value: "Lee",
    writable: true,
    enumerable: true,
    configurable: true,
  },
  // 접근자 프로퍼티 정의
  fullName: {
    // getter 함수
    get() {
      return `${this.firstName} ${this.lastName}`;
    },
    // setter 함수
    set(name) {
      [this.firstName, this.lastName] = name.split(" ");
    },
    enumerable: true,
    configurable: true,
  },
});
```

### 객체 변경 방지

> 객체는 변경 가능한 값이기 때문에 재할당 없이 직접 변경할 수 있다.
> 이를 방지하고자 자바스크립트에서 여러 메서드를 제공한다.

| 구분           | 메서드                    | 프로퍼티 추가 | 프로퍼티 삭제 | 프로퍼티 값 읽기 | 프로퍼티 값 쓰기 | 프로퍼티 어트리뷰트 재정의 |
| -------------- | ------------------------- | ------------- | ------------- | ---------------- | ---------------- | -------------------------- |
| 객체 확장 금지 | Object.preventExtenstions | X             | O             | O                | O                | O                          |
| 객체 밀봉      | Object.seal               | X             | X             | O                | O                | X                          |
| 객체 동결      | Object.freeze             | X             | X             | O                | X                | X                          |

- 불변 객체
  변경 방지 메서드들은 shallow only이다.
  <br>즉, 객체의 직속 프로퍼티만 변경 방지가 적용되고, 프로퍼티의 객체는 영향을 주지 못한다.
  <br>따라서, freeze 메서드와 재귀함수를 이용해서 deep freeze를 구현할 수 있다.

```jsx
function deepFreeze(target) {
  // 객체가 아니거나 동결된 객체는 무시하고 객체이고 동결되지 않은 객체만 동결한다.
  if (target && typeof target === "object" && !Object.isFrozen(target)) {
    Object.freeze(target);
    /*
      모든 프로퍼티를 순회하며 재귀적으로 동결한다.
      Object.keys 메서드는 객체 자신의 열거 가능한 프로퍼티 키를 배열로 반환한다.
      ("19.15.2. Object.keys/values/entries 메서드" 참고)
      forEach 메서드는 배열을 순회하며 배열의 각 요소에 대하여 콜백 함수를 실행한다.
      ("27.9.2. Array.prototype.forEach" 참고)
    */
    Object.keys(target).forEach((key) => deepFreeze(target[key]));
  }
  return target;
}

const person = {
  name: "Lee",
  address: { city: "Seoul" },
};

// 깊은 객체 동결
deepFreeze(person);

console.log(Object.isFrozen(person)); // true
// 중첩 객체까지 동결한다.
console.log(Object.isFrozen(person.address)); // true

person.address.city = "Busan";
console.log(person); // {name: "Lee", address: {city: "Seoul"}}
```
