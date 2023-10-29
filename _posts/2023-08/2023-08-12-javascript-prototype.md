---
layout: single
title: "[Javascript] Javascript - Prototype"
categories: Javascript
tag: [Javascript, Prototype]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Javascript - Prototype

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 prototype은 javascript에서 상속을 가능하게 해주는 어떤 것?

그 정도만 알아도 충분한지 의문이 들어 좀 더 딥 다이브 하기로 하고 찾아보았습니다.

</aside>

### 시작하기 전

개발자는 아무 이름이나 갖다 붙이지 않는다. 분명히 원하는 바가 있고, 뜻을 관통하는 단어를 선택하여 명명한다.

그래서 javascript의 prototype 개념을 좀 더 잘 이해하기 위해 왜 그렇게 명명되었는지 알려면

`prototype` 용어 자체가 무엇을 뜻하는지를 알아야 할 것 같다.

**프로토타입?**

> 프로토타입은 **원래의 형태** 또는 전형적인 예, 기초 또는 표준이다. 프로토타입은 '시스템의 미완성 버전 또는 **중요한 기능들이 포함되어 있는 시스템의 초기모델**’

## Javascript

프로토타입 기반 언어 (prototype-based language)

- **모든 객체들은 “프로토타입 객체”를 가진다.**
  - 프로토타입 객체: 메서드와 속성을 상속 받기 위한 템플릿

프로토 타입 체인 (prototype chain)

- 프로토타입 객체도 상위 프로토타입 객체로 부터 상속 받을 수도 있고, 그 상위도 위에서 상속받을 수 도 있음.
- 이를 통해 특정 객체에서 다른 객체에 정의된 메서드와 속성을 사용할 수 있게 된다. (상속)
- 사실 객체 자체에 **메서드와 속성**이 정의된 것이 아니라 **객체의 생성자의 prototype 속성에 정의**되어 있다.\*\*\*

→ 객체 prototype === 객체 개별 속성 (\_\_proto\_\_)  
(\_\_proto\_\_ 로 해당 객체의 프로토타입에 접근할 수 있음)

→ 객체 생성자의 prototype === 생성자(원형)의 속성

## prototype, \_\_proto\_\_ 이게 다 뭘까?

자, 이해가 잘 안가니까 예제로 보자.

Bird라는 클래스(생성자)를 만들어서 parrot이라는 인스턴스 객체를 만들었다.

```jsx
// 객체 생성 (생성자 함수 혹은 클래스)
export class Bird {
  wings = "날개";
  feathers = "깃털";
  beak = "부리";

  layEggs() {
    return `일을 낳는다.`;
  }

  standWithTwoFeet() {
    return `이족 보행을 한다.`;
  }
}

// 인스턴스 만들기
const parrot = new Bird();

console.log(parrot); // Bird {}
```

### \_\_proto\_\_

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-08/13-parrot-console.png)

parrot 자체만 찍어보면 얘 스스로 가지고 있는 속성이나 메서드가 없다.

하지만 여전히 `parrot.wings` 이렇게 winds 라는 속성을 참조할 수 있다.

상속을 받았으니 당연한 얘기 같지만, 어떻게 wings 속성에 접근을 한 것일까?

wings를 참조하게 되면 parrot의 속성에서 찾고,

만약 없으면 부모가 가지고 있는 속성을 찾게 된다.

이때 부모가 가지고 있는 속성 및 메서드 정보를 가지고 있는 것이 프로토타입 객체이다.  
(유전 정보라고 할 수 있다.)

그리고 이때 부모의 유전 정보에서 속성과 메서드를 참조할 수 있는 링크를 제공한다.

그것이 바로 <strong>\_\_proto\_\_</strong>이다.

이렇게 해서 Javascript에서 상속의 개념을 사용할 수 있다.

따라서 다음과 같이 비교해보면 true 가 나오게 된다.

```jsx
console.log(Bird.prototype === parrot.__proto__); // true
console.log(Bird.prototype === Object.getPrototypeOf(parrot)); // true
```

- 그래서 \_\_proto\_\_.\_\_proto\_\_.\_\_proto\_\_ … 체이닝 타고 올라가다보면,  
  Javascript Object 객체를 만나게 되고, 그 Object 객체의 proto는 null을 가리키고 있다.

```jsx
console.log(Bird.prototype.__proto__ === Object.prototype); // true
// (Bird의 prototype의 유전 정보를 가리키는 proto는 Object의 prototype을 가리킨다)
```

### prototype

잠깐, 왜 parrot.\_\_proto\_\_ 랑 Bird랑 비교하는 게 아니라 Bird.prototype을 비교하는 걸까?  
parrot은 Bird로 만들어졌는데??

Bird는 생성자이다.

따라서 생성자로 인스턴스를 만드는 것은 당연한 것이고,

생성자가 가지고 있는 특징들(속성, 메서드)을 유전 및 상속시켜주기 위해 기록/보관하고 있는 곳(정의된 곳)이 바로 **prototype 속성**(객체)이다.

그림으로 보면 다음과 같다.

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-08/13-proto-and-prototype.png)

### constructor

뭐야, constructor는 왜 또 나와?

**MDN의 설명**

> 모든 생성자 함수는 `[constructor](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/constructor)` 속성을 지닌 객체를 프로토타입 객체로 가지고 있습니다. 이 `constructor` 속성은 원본 생성자 함수 자신을 가리키고 있습니다.

그러니까, 생성자 함수는 프로토타입 객체(Bird.prototype으로 접근했을 때 나오는 그 객체)를 가지고 있다.  
그 객체 내부에는 constructor라는 속성이 있고, 얘는 또 원래의 생성자 함수를 가리킨다.

그래서 유전 정보는 프로토타입 객체가 가지고 있고,  
이 프로토타입 객체가 생성자 함수를 가리키고 있기 때문에  
생성자로 인스턴스를 생성하면 생성된 모든 인스턴스에서 가지고 있던 속성과 메서드를 모두 참조할 수 있는 것이다.

```jsx
console.log(parrot.constructor === Bird); // true
```

⇒ 그래서 해당 클래스에서 인스턴스를 만들었다고 해서 메서드나 속성들이 복사가 되는 것은 아니다.

그저 \_\_proto\_\_를 통해 chaining 을 타고 올라가서 접근하게 되는 것.

- Bird와 Object의 관계도

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-08/13-and-prototype-and-object.png)

### [[prototype]]

browser console에 항상 찍히는 이 친구는 무엇일까?

사실, 프로토타입 객체에 바로 접근하는 공식적인 방법은 없었다고 한다.

→ Javascript 언어 표준 스펙에서`[[prototype]]`으로 표현되는 프로토타입 객체에 대한 링크를 **내부 속성으로 정의**되어 있다.

그러나 브라우저에서는 이 `[[prototype]]` 속성 대신 `__proto__`를 통해 접근할 수 있도록 구현되어 있다.

그런데, proto는 deprecated 되었고,  
ECMAScript 2015부터 `Object.getPrototypeOf(obj)` 메서드를 통해 프로토타입 객체에 접근할 수 있게 되었다고 한다.

### 결론

왜 prototype이라고 했을까? ⇒ 만들어진 인스턴스는 원형/템플릿/틀이 있기 때문일 것 같다.

### 참고 링크

https://developer.mozilla.org/ko/docs/Learn/JavaScript/Objects/Object_prototypes

https://yceffort.kr/2020/06/javascript-prototype

[https://mong-blog.tistory.com/entry/프로토타입-feat-매운-맛🔥](https://mong-blog.tistory.com/entry/%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-feat-%EB%A7%A4%EC%9A%B4-%EB%A7%9B%F0%9F%94%A5)

<hr>
