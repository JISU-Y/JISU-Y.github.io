---
layout: single
title: "[Javascript] Javascript - 메모리 관리"
categories: Javascript
tag: [Javascript, memory, garbage collection]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Javascript - 메모리 관리

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 가비지 콜렉션이 있다더라. 정도의 수준에서 좀 더 알아보도록 한다.
</aside>

## Javascript에서 메모리 관리

- 저수준 언어의 메모리 관리: 메모리 할당/해제 메서드 사용.
- 자바스크립트의 메모리 관리: **가비지 컬렉션**을 통해 자동으로 메모리 할당 및 해제.

<aside style='background-color : transparent; color: black; opacity: 0.8; border: 1px solid black; padding: 10px 20px'>
☝️ “자동 메모리 관리라고 해서 개발자가 메모리 관리에 대해 고민할 필요가 없다는 것은 아니다.”

\- MDN

</aside>

### 메모리 생존 주기

1. **필요할 때 할당.**
   - `c`: `malloc()`
   - `javascript`: 언어적으로 명시 X
2. **할당된 메모리 사용.**
   - 읽기 및 쓰기:
     - 할당된 메모리에 값을 넣을 수도
     - 거기에 저장되어 있는 값을 읽을 수도
3. **필요하지 않으면 해제.**
   - `c`: `free()`
   - `javascript`: 언어적으로 명시 X

---

### 메모리 할당

<aside style='background-color : transparent; color: black; opacity: 0.8; border: 1px solid black; padding: 10px 20px'>
❓ <strong>WHEN?</strong>

- 변수 및 함수 선언할 때 메모리 할당.
- 함수 호출할 때 메모리 할당.

</aside>

#### 변수 및 함수 선언 시

```jsx
// primitive type 메모리 할당
const test = “test-text”

// object type 메모리 할당
const obj = {
	one: 1,
	two: 2
}
const arr = ["one", "two", "three"]

// 함수 메모리 할당
function squareFunc(num) {
	return num*num
} // 함수를 저장하기 위해 메모리 할당
```

문자열, 숫자 등 primitive type 부터 object type 까지 선언 시 메모리 할당.

함수 또한 객체이기 때문에 함수 선언 시 메모리 할당.

#### 함수 호출 시

```jsx
const today = new Date(); // Date 생성자 함수를 호출하면 메모리 할당.

const myDiv = document.createElement("div"); // div 엘리먼트를 위한 메모리 할당.
```

Date 클래스(생성자 함수)를 사용하면 date 객체가 생겨 메모리 할당됨.  
엘리먼트도 마찬가지.

<aside style='background-color : transparent; color: black; opacity: 0.8; border: 1px solid black; padding: 10px 20px'>
❓ <strong>WHERE?</strong>

JS 엔진  
→ 데이터를 저장할 수 있는 <span style='text-decoration: underline; font-weight: 600;'>힙(Heap)</span>과 <span style='text-decoration: underline; font-weight: 600;'>스택(Stack)</span> 영역 존재.

</aside>

**스택**: 정적 메모리 할당

- 컴파일 타임에 크기를 알고 있는 데이터
- primitive type, 참조(객체와 함수를 가리키는)
- 고정된 메모리 크기 할당.

**힙**: 동적 메모리 할당

- 런타임에 크기를 알게 되는 데이터
- 필요에 따라 더 많은 공간을 할당.
- 객체(함수 포함)

할당

```jsx
const user = {
  id: 1,
  name: "blah",
}; // user 객체 자체는 동적 메모리 -> 힙에 할당
```

재할당

```jsx
let variable = "init"; // 스택에 할당

variable = "initial"; // 스택에 메모리 새로 할당
```

JS에서는 기본적으로 primitive type들은 immutable

→ 할당된 메모리에 들어있는 값을 변경하는 것이 아님.  
새로 메모리를 할당 받아 가리키게 되는 메모리 위치를 변경해주는 것.

---

### 메모리 사용

변수나 객체 속성의 값을 읽고 쓰거나 함수 호출 시 함수에 인수를 전달할 때 메모리에 저장되어 있는 것을 사용하여 수행.

---

### 메모리 해제 \*\* (중요)

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
☝️ 메모리 해제가 관건

할당된 **메모리가 더 이상 필요없는지 여부를 판단하기 어렵기** 때문.

</aside>

→ Javascript 에서는 자동 메모리 관리 방법 **가비지 컬렉션(GC)** 사용  
메모리 할당 추적, 메모리 블록 필요 없는지 판단하여 회수

---

## 가비지 콜렉션

### Reference-counting 가비지 콜렉션

> 아무 것도 참조하지 않는 오브젝트를 가비지로 처리.

```jsx
let company = "startup";

let team = {
  cs: "whoever",
  marketing: "none",
  product: ["dev", "design", "po", "qa"],
};

function sprint() {
  return "tired";
}

let ourTeam = team;

let productTeam = team.product;
```

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-08/24-reference-counting.png)

- `company`는 원시 타입을 가지고 있기에 스택에 쌓임.
- 스택에 객체를 가리키는 `team`이라는 참조가 쌓임.
- 객체 자체는 힙에 있음.
- 스택에 `ourTeam` 참조가 쌓임
- `ourTeam`도 `team`이 가리키는 똑같은 메모리 위치를 가리킴.
- `productTeam`이라는 참조가 스택에 쌓임.
- 힙에 있는 메모리(`team.product`의 값이 있는)가 있고, 그걸 `productTeam`이 가리킴.

<aside style='background-color : transparent; color: black; opacity: 0.8; border: 1px solid black; padding: 10px 20px'>
🤔 <strong>궁금증!</strong>

객체가 한번에 저장되는 거야 뭐야? JS에서 객체는 동적 메모리 아닌가? 연속된 메모리 영역이 아닐텐데?

- <strong>`team`</strong> 참조가 객체를 가리킬 때, 실제로는 해당 객체의 메모리 주소를 참조한다.
  - 이를 통해 객체의 모든 속성들에 접근하여 할 수 있다.
  - 객체의 속성들은 각각 따로 저장되지만, <strong>`team`</strong> 참조를 통해 각각의 속성(값)에 접근할 수 있게 된다.
- 물론 배열의 요소 또한 각각 다른 영역에 저장된다.
</aside>

```jsx
team = null;
ourTeam = null;
```

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-08/24-reference-counting-null.png)

- `team`과 `ourTeam`이 `null`이 되어 가리키고 있는 게 끊어지며 이 쓸모없는(참조되지 않는) 메모리 영역은 가비지 컬렉션의 대상이 됨.
- 그러나 `productTeam`이 가리키는 메모리 영역은 그대로 남아있음.
  - `team` 객체 메모리가 사라졌다고 `productTeam`이 사라진 건 아님.

그러나, 이 알고리즘은 <span style='text-decoration: underline; font-weight: 600;'>순환 참조를 고려하지 못함.</span> 😭

```jsx
let you = {
  name: "your name",
};
let me = {
  name: "my name",
};

you.me = me;
me.you = you;
```

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-08/24-reference-counting-circular-referencing.png)

```jsx
you = null;
me = null;
```

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-08/24-reference-counting-keep-alive.png)

`you`와 `me`를 서로 참조하게 하고(순환 참조) `you` 참조와 `me` 참조를 끊어버려도 힙에서는 메모리 영역이 계속 남아있게 됨.

### Mark-and-sweep 알고리즘

> 닿을 수 없는 오브젝트를 가비지로 처리

`roots`(전역 오브젝트/브라우저에서는 `window`, NodeJS 환경에서는 `global`) 객체에서 시작해서 참조하여 도달할 수 없는 객체를 찾음.

도달할 수 없으면 가비지로 mark하고, 이후에 sweep하게 됨.

```jsx
let team = {
  cs: "whoever",
  marketing: "none",
  product: ["dev", "design", "po", "qa"],
};

let anotherRefTeam = team;
let productTeam = team.product;

team = null;
anotherRefTeam = null;

console.log(productTeam);

let you = {
  name: "your name",
};
let me = {
  name: "my name",
};

you.me = me;
me.you = you;

you = null;
me = null;

const unusedValue = "will not be used";
```

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-08/24-mark-and-sweep.png)

- 순환 참조 되고 있던 것들은 스택의 참조들이 `null`로 끊어졌기 때문에 `window`에서 접근할 수 있는 방법이 없음.
- `unusedValue` 같은 경우는 아무데도 사용되지 않으므로 가비지 컬렉션의 대상이 된다.
- `productTeam`는 `console`의 `log` 메서드의 인자로 참조를 사용하고 있기 때문에 reachable하여 가비지 컬렉션의 대상이 되지 않는다.
  - `productTeam`도 아무데도 사용되지 않으면 가비지 컬렉션의 대상이 된다.

### 메모리 낭비

#### 가비지 콜렉터의 Trade-offs

- 아무리 알아서 해준다고 해도 청소되는 것은 필요 없을 때 되는 게 아니라 주기적으로 되는 것이기 때문에 생각하는 것 보다 메모리를 많이 잡아먹을 수 있다.
- 콜렉션이 주기적으로 실행되는 것을 개발자가 정확히 언제 일어나는지 알 수가 없다.
- 컬렉팅하게 되는 가비지의 양이 많거나 빈도수가 많아져도 성능을 잡아먹게 됨.
  → 그래도 다행인건 유저나 개발자가 신경 쓸 정도는 아니라고 함.

<aside style='background-color : transparent; color: black; opacity: 0.8; border: 1px solid black; padding: 10px 20px'>
👉 그래서 개발자로서 메모리를 어떻게 관리하면 될까?

낭비 되는 원인 몇 가지를 살펴보자.

</aside>

**전역 변수**

var 혹은 아예 키워드를 쓰지 않으면 전역 변수에 저장 됨.  
(window 객체의 속성으로 할당 됨.)

→ function 키워드도 마찬가지라고 함 🙀

사용하지 말라기 보다 null로 참조를 해제해주면 좋음.

**잊어버린 타이머**

```jsx
// 잊어버린 타이머
const object = {};
const intervalId = setInterval(function () {
  // 여기에서 사용된 모든 것들은 interval이 클리어될 때까지 수집되지 않음.
  doSomething(object);
}, 2000);
```

2초마다 실행되므로 interval이 끝나지 않는 이상 저 스코프 안에서 참조된 객체들은 수집되지 않음.

```jsx
clearInterval(intervalId);
```

clearInterval로 타이머 취소.

→ SPA에서 특히 조심. 페이지가 변경되어도 백그라운드에서 실행되므로

**구독 취소를 잊어버린 이벤트 리스너**

이벤트 리스너 콜백은 예전에는 수집 못했는데 요새 브라우저는 다 한다고 함.

그래도 remove 해주자!

**DOM 참조**

```jsx
const elements = [];
const element = document.getElementById("button");
elements.push(element);

function removeAllElements() {
  elements.forEach((item, index) => {
    document.body.removeChild(document.getElementById(item.id)) **
      elements.splice(index, 1); // elements 배열 mutate하여 요소 제거**
  });
}
```

JS에서 DOM을 참조하여 배열에 저장해두었고, 엘리먼트는 지웠지만

elements 객체는 여전히 살아있으므로 메모리를 잡아먹는다.

따라서 elements에서 참조하고 있는 요소들 모두 제거.

### 참고 링크

https://developer.mozilla.org/ko/docs/Web/JavaScript/Memory_management

https://felixgerschau.com/javascript-memory-management/

(보면 좋을 것 같은 글들)

https://yceffort.kr/2020/07/memory-leaks-in-javascript

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/WeakMap

<hr>
