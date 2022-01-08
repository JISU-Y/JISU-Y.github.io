---
layout: single
title: "[TIL] Javascript - Symbol"
categories: web
tag: [TIL, Javascript, Symbol]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## Javascript

### Symbol

#### Symbol 이란

> ES6에서 소개된 개념으로 변경 불가능한 원시 타입이고, 다른 값과 중복되지 않는 고유한 값이다.

유일한 식별자를 만들 때 사용하며 new를 붙이지 않는다. (객체가 아니고 원시타입이나까!)
유일성을 보장한다는 것은 즉, 전체 코드 중 무조건 하나라는 것.

```jsx
const a = Symbol();
const b = Symbol();

console(a == b); // false
console(a === b); // false
```

이렇게 똑같이 Symbol을 할당해 주었는데, a와 b는 전혀 다르다고 나온다.

또한, 안에 설명을 적어줄 수 있다.

```jsx
const id = Symbol("id"); // id는 기능에 영향은 없고 그냥 설명임
const id2 = Symbol("id");

console.log(id === id2); // false
```

그러나, Symbol 안에 적어주는 string 값은 id이름을 가진 심볼이 유일하다가 아니라 Symbol이 있는데 그냥 설명 적어놓는 것 (빈 걸로 하면 나중에 뭐가 뭔지 헷갈리니까)

<br>

#### Symbol 예제

**속성으로 사용**
property key는 문자형으로 보통 사용하지만 number나 boolean형도 가능하다.

```jsx
const obj = {
  1: "1입니다",
  false: "거짓",
};

// 하지만 keys 메서드로 key들을 가져오면 문자형으로 반환되고,
Object.keys(obj); // ['1', 'false']
// 접근할 때도 문자형으로 사용한다.
obj["1"];
obj["false"];
```

이처럼 Symbol을 이용해서도 key를 정의할 수 있다.

Symbol 타입을 객체 키로 사용해보자.

```jsx
const user = {
  name: "Mike",
  age: 30,
  [id]: "myid", // 사용할 때는 []안에 넣어서 사용
};

console.log(user); // {name: 'Mike', age: 30, Symbol(id): 'myid'}
console.log(user[id]); // 'myid'

Object.keys(user); // ['name','age']
```

keys 메서드를 이용해서 object의 모든 key를 확인할 때 'id' 키는 건너띈다.
<br>이처럼 객체 메서드들 (values, entries) 혹은 for in문 에서는 키가 Symbol형 인 것은 건너 띈다.

**근데 이걸 어따 써?**
<br>더 밑에 예제로 보면 더 잘 와닿겠지만, 설명을 하자면 원본 객체에 어떤 속성을 추가해서 쓰고 싶을 때, 하지만 그 객체를 임의로 건드리지 않는 것이 좋을 때, 객체를 건드리지 않고서 추가할 수 있다.

```jsx
const user = {
  name: "Mike",
  age: 30,
};

user.name = "myname"; // 원래 있던 객체(누가 만들어서 값을 넣어놓은) user에 name 키에 값이 있는데 그걸 무시하고 임의로 name을 할당해버리면 안된다.
user.name_I_added = "NO"; // 그렇다고 겹치지 않는 key의 이름을 쓰는 것도 좋지 않다.

const id = Symbol("id");
user[id] = "myid"; // Symbol형 id를 키로 설정해서 Value를 myid로 집어넣음
```

이렇게 키를 임의로 추가하게 되면 저 user가 사용되고 있는 어딘가에서 예상치 못하게 내가 추가했던 속성(name_I_added)이 튀어나올지 모르기 때문이다.

따라서, Symbol 형을 사용하여 속성으로 집어넣으면 다른 곳에서 keys 메서드를 사용하든, for in문을 사용하고 있든 id라는 속성이 갑툭튀할 일은 없다는 것이다.

**만약 keys 메서드나 for in 문에서도 Symbol key를 보고싶다면?**
그것도 다 메서드가 있다.

```jsx
const user = {
  name: "Mike",
  age: 30,
  [id]: "myid", // Symbol형 key
};

Object.getOwnPropertySymbols(user); // [Symbol(id)]

Reflect.ownKeys(user); // ['name', 'age', Symbol(id) ]
```

Symbol로 정의된 key만 나타내주는 메서드 => getOwnPropertySymbols를 이용하여 Symbol key를 가져올 수 있다.

또한, Reflect와 ownKeys 메서드를 사용하면 해당 객체의 모든 속성(Symbol 포함)을 나타낼 수 있다.

#### 전역 심볼

> 전역 심볼은 하나의 심볼만 보장받을 수 있도록 한다. 없으면 만들고, 있으면 그걸 쓴다.

Symbol 함수는 똑같이 이름을 쓰더라도 계속해서 다른 Symbol 값을 생성하지만(생성한 것이 서로 다 다르지만),
<br>Symbol.for은 키를 통해 다른 곳에서도 심볼을 공유/사용할 수 있다.
코드 어디에서든 사용 가능하다.

Symbol.for() 이 안에 설명을 담아서 다른 곳에서도 사용할 수 있도록 해준다.

```jsx
const id1 = Symbol.for("id");
const id2 = Symbol.for("id");

console.log(id1 === id2); // true

console.log(Symbol.keyFor(id1)); // 'id'
```

keyFor을 이용해서 Symbol을 정의할 때 적어두었던 설명("id")을 가져올 수 있다.

keyFor은 Symbol.for()로 즉, 전역 심볼로 정의한 변수에서만 사용 가능하다.

일반 Symbol에서 그 설명을 가져오려면 id.description 을 하면 된다.

```jsx
const id = Symbol("id 입니다");
id.description; // 'id 입니다'
```

<br>

**이해를 돕기 위한 예제**

1.  Symbol (X)
    <br>객체 속성을 직접 수정할 때이다.

    ```jsx
    // 다른 개발자가 만들어 놓은 객체
    const user = {
      name: "Mike",
      age: 30,
    };

    // 내가 작업 // 직접 객체 속성을 수정
    user.blackList = "조심! 얘 특급진상임.";

    // 이렇게 되면
    // 사용자가 접속하면 보는 메세지 (다른 개발자가 만들어 놓은 for문을 이용한 메세지)
    for (let key in user) {
      console.log(`His ${key} is ${user[key]}.`);
    }
    // His name is Mike.
    // His age is 30.
    // His blackList is 조심! 얘 특급진상임.
    ```

    **위 처럼 후덜덜한 상황이 벌어질 수 있다는 것이다.**

    따라서 사용자에게 보여주고 싶지는 않지만 내부적으로 알고 싶은 속성을 추가하려고 할 때 Symbol을 이용할 수 있을 것이다.

2.  Symbol (O)

    ```jsx
    // 다른 개발자가 만들어 놓은 객체
    const user = {
      name: "Mike",
      age: 30,
    };

    // blackList라는 이름의 심볼 생성
    const blackList = Symbol("blackList");
    user[blackList] = "조심! 얘 특급진상임.";

    // 사용자가 접속하면 보는 메세지 (다른 개발자가 만들어 놓은 for문을 이용한 메세지)
    for (let key in user) {
      console.log(`His ${key} is ${user[key]}.`);
    } // 심볼로 만들었기 때문에 for in 문에서 원치않는 속성이 출력되지 않는다.
    // His name is Mike.
    // His age is 30.
    ```

    이렇게 하면 for in문에서 Symbol 형의 key는 건너띄기 때문에 다른 사람이 만든 객체와 사용하고 있는 코드에 지장이 없도록 하면서 추가하고 싶은 속성을 추가할 수 있게 된다.

[참고 및 출처]
https://another-light.tistory.com/105
https://velog.io/@nomadhash/Java-Script-%EA%B9%8A%EC%9D%80-%EB%B3%B5%EC%82%AC%EC%99%80-%EC%96%95%EC%9D%80-%EB%B3%B5%EC%82%AC
https://hanamon.kr/javascript-%EB%B3%80%EC%88%98%EC%9D%98-%ED%83%80%EC%9E%85-%EC%9B%90%EC%8B%9C%ED%98%95%EA%B3%BC-%EC%B0%B8%EC%A1%B0%ED%98%95/
https://sanghaklee.tistory.com/23
https://www.youtube.com/watch?v=E9uCNn6BaGQ
