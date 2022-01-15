---
layout: single
title: "[TIL] Javascript - 제너레이터 / async/await"
categories: web
tag: [TIL, Javascript, 제너레이터, async, await]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## Javascript 개념

### 제너레이터

async와 await를 알아보기 전에 제너레이터라는 개념을 짚고 넘어가야 할 것 같다.

#### 제너레이터(generator)란?

1. 제너레이터의 정의

   > 제너레이터 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수

2. 제너레이터의 특징

   \- 제너레이터 함수는 함수 실행의 제어권을 함수 호출자(caller)에게 양도함으로써 제어(일시 중지/재개)된다.

   \- 제너레이터 함수는 함수 호출자와 상태(데이터) 또한 주고받을 수 있다. (양방향으로 함수의 상태를 주고받음)

   \- 제너레이터 함수를 호출하면 코드를 실행하는 것이 아니라 이터러블이면서 동시에 이터레이터인 제너레이터 객체를 반환한다.

   iterable(이터러블)과 iterator(이터레이터)의 설명은 제너레이터 객체에서 알아보도록 한다.

#### 제너레이터의 생성

```jsx
function* getDecFunc() {
  yield 1;
}
```

일반 함수처럼 정의하고, function 키워드 뒤에 \*을 붙인다.

    (*) 애스터리스크의 위치는 function과 함수 이름 사이 어디든 상관 없다.
    화살표 함수로 정의할 수 없다.
    제너레이터 함수는 new 연산자와 함께 생성자 함수로 호출될 수 없다.

#### 제너레이터 사용

- 제너레이터 객체

  > 제너레이터 함수를 호출하면 일반 함수처럼 함수 코드 블록을 실행하는 것이 아니다.
  > **제너레이터 객체를 생성해서 반환**하는 것이다.
  > 반환된 제너레이터 객체는 이터러블(iterable)이면서 이터레이터(iterator)이다.

- 이터러블(iterable): Symbol.iterator 메서드를 상속받아 사용할 수 있다.(Object.prototype 에 있음)

- 이터레이터(iterator): 프로토타입에 next 메서드를 가지고 있고, result 객체(value, done 프로퍼티를 갖는 객체)를 반환한다.

1. 제너레이터 선언 및 정의

   ```jsx
   function* fn() {
     console.log(1);
     yield 1;
     console.log(2);
     yield 2;
     console.log(3);
     console.log(4);
     yield 3;
     return "finish";
   }

   const generator = fn();

   // 제너레이터 함수는 제너레이터 객체를 반환한다.
   console.log(generator); // Object [Generator] {}
   ```

   ![](https://images.velog.io/images/jisu129/post/6db8f4ad-cf90-491e-99fb-182d7731ae89/generator%20%EC%BD%98%EC%86%942.JPG)

2. 제너레이터 객체의 next 메서드 사용

   제너레이터는 이터레이터(iterator)이니 next 메서드를 사용할 수 있다.

   \* next 메서드 : yield 표현식까지만 실행해서 yield 값을 value 프로퍼티에 넣어서 반환한다.

   (가장 처음 yield를 만날 때까지의 함수를 실행하고 리절트 객체를 반환)

   ```jsx
   console.log(generator.next());
   // 1 // 제너레이터 함수 내부에서 실행된 console.log(1)
   // { value: 1, done: false } // yield를 만나 반환되는 값을 가지고 객체로 반환됨
   // value는 yield 오른쪽에 있는 값, yield에 값이 없으면 undefined 반환
   // done은 제너레이터 함수가 끝났는지 안끝났는지를 판단

   // 또 next 메서드를 이용하여 제너레이터 함수를 호출하면
   console.log(generator.next());
   // 2 // 제너레이터 함수 내부에서 실행된 console.log(2)
   // { value: 2, done: false }

   // 또 next 메서드를 이용하여 제너레이터 함수를 호출하면
   console.log(generator.next());
   // 3
   // 4
   // { value: 3, done: false }

   // 또 next 메서드를 이용하여 제너레이터 함수를 호출하면
   console.log(generator.next());
   // { value: 'finish', done: true }
   // return 문에 의해서 값이 반환된다.
   // done은 true로 바뀐다.

   // 만약 끝난 상태에서 한 번 더 next 메서드로 호출하면?
   console.log(generator.next());
   // { value: undefined, done: true }
   // yield 값이 없으므로 value는 undefined
   // 함수는 이미 끝났으니까 done은 true로 출력이 된다.
   ```

3. 제너레이터 객체의 추가 메서드(return, throw)

   제너레이터 객체는 iterator 이기 때문에 next 메서드를 기본으로 가지지만, 추가적으로 return, throw 메서드를 갖는다.

   \* return 메서드 : return 표현식까지 실행(함수를 모두 실행), return 값을 value에 넣고, 함수도 끝났으므로 done도 true로 반환한다.

   이후 next 메서드를 사용하면 계속 {value: undefined, done: true} 객체를 반환한다.

   ```jsx
   function* genFunc() {
     console.log(1);
     yield 1;
     console.log(2);
     yield 2;
     console.log(3);
     console.log(4);
     yield 3;
     return "finish";
   }

   const generator = genFunc();

   // 이렇게 바로 return 메서드를 사용해서 제너레이터 함수를 호출하면
   console.log(generator.return());
   // { value: undefined, done: true }
   // yield 값은 없으므로 undefined
   // return이 되면서 함수는 끝났으므로 done은 true를 반환한다.

   // return 이후 next 메서드를 사용해서 generator를 호출하면
   console.log(generator.next());
   // { value: undefined, done: true }
   ```

   \* throw 메서드 : 인수로 전달받은 에러를 발생시킨다. (catch 문으로 바로 간다.)
   <br>마찬가지로 어쨌든 함수가 끝났으므로 done도 true로 반환한다.

   ```jsx
   function* genFunc() {
     try {
       console.log(1);
       yield 1;
       console.log(2);
       yield 2;
       console.log(3);
       console.log(4);
       yield 3;
       return "finish";
     } catch (error) {
       console.log(error);
     }
   }

   const generator = genFunc();

   console.log(generator.next());
   console.log(generator.next());

   console.log(generator.throw(new Error("Error occured")));
   // Error: Error occured
   // throw로 error를 넘겨 주면
   // { value: undefined, done: true }

   console.log(generator.next());
   // { value: undefined, done: true }
   // error를 던져서 catch 문으로 함수가 끝났기 때문에

   // return 이후 next 메서드를 사용해서 generator를 호출하면
   console.log(generator.next());
   // { value: undefined, done: true }
   ```

#### 제너레이터의 활용

제너레이터 함수에다가는 제너레이터 함수 안에서 관리하는 상태도 꺼내올 수 있고(yield, return의 값),
next 메서드를 이용해서 밖에서 제너레이터 함수로 값을 집어넣을 수도 있다.

이 특징을 이용해서 비동기 처리에 사용될 수 있다.
따라서 프로미스를 사용할 때, 프로미스의 후속처리 메서드(then, catch, finally)등이 없어도 처리 결과를 반환하도록 구현할 수 있다.

\* 제너레이터로 async 함수(제너레이터 실행기) 만들기

```jsx
const async = (generatorFunc) => {
  const generator = generatorFunc()

  const onResolved = (arg) => {
    const result = generator.next(arg)

    return result.done ? result.value : result.value.then((res) => onResolved(res)) // 재귀 호출
  }

  return onResolved // 클로저 사용 (이 렉시컬 환경에서 generator의 변수를 기억!)
}

**async(function* fetchTodo() {
  const url = "https://jsonplaceholder.typicode.com/todos/1"

  const response = yield fetch(url)
  const todo = yield response.json()
  console.log(todo)
  // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
})**() // async 함수가 반환하는 것은 onResolved 함수이므로 실행해주어야 한다. ()
```

제너레이터를 사용해서 비동기 처리를 동기처럼 동작하도록 구현했지만 코드가 장황해지고 가독성이 나빠졌다.

ES8에서는 제너레이터보다 간단하고 가독성 좋게 비동기 처리를 동기 처리처럼 동작하도록
구현할 수 있는 async/await가 도입되었다.

### 비동기 - (async / await)

> ES8에 도입된 async function과 await 키워드의 조합은 비동기 코드를 쓰고 Promise를 더 읽기 쉽게 만들어준다.

#### async / await 특징

\- async await 또한 프로미스를 기반으로 동작한다.

\- async 함수는 항상 프로미스를 반환한다.

\- await 키워드는 프로미스가 settled 상태(비동기 처리가 수행된 상태/fulfilled, rejected)가 되면 프로미스가 resolve 처리한 결과를 반환한다.

```jsx
const fetch = require("node-fetch");

async function fetchTodo() {
  const url = "https://jsonplaceholder.typicode.com/todos/1";

  const response = await fetch(url);
  const todo = await response.json();
  console.log(todo);
  // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
}

fetchTodo();
```

그러니까, 요청한 처리가 settled 상태가 될 때까지 대기하는 것이다.
<br>fetch(url) 요청 처리가 되어서 프로미스를 받으면 그것을 response에 할당하게 되는 것.

그렇다. 잠깐 중지하여 비동기 처리를 다 끝내버리는 것이다.

#### 주의점

이렇게 비동기 처리가 완료될 때까지 기다리는 것이기 때문에
모든 promise에 await 사용하는 것은 주의해야한다.

\* 비동기가 순서대로 동작할 필요가 없을 때

```jsx
async function foo() {
  const a = await new Promise((resolve) => setTimeout(() => resolve(1), 3000));
  const b = await new Promise((resolve) => setTimeout(() => resolve(2), 2000));
  const c = await new Promise((resolve) => setTimeout(() => resolve(3), 1000));

  console.log([a, b, c]); // [1, 2, 3]
}

foo(); // 약 6초 소요된다.
```

변수 a, b, c에 할당하는 값들은 서로 영향이 없기 때문에 순서를 신경쓰지 않아도 된다.
<br>따라서, await를 모두에게 붙여주면 시간이 오래걸리게 된다.

개선 코드

```jsx
async function foo() {
  const res = await Promise.all([
    new Promise((resolve) => setTimeout(() => resolve(1), 3000)),
    new Promise((resolve) => setTimeout(() => resolve(2), 2000)),
    new Promise((resolve) => setTimeout(() => resolve(3), 1000)),
  ]);

  console.log(res); // [1, 2, 3]
}

foo(); // 약 3초 소요된다.
```

배열안의 비동기 처리가 각각 순서 상관없이 비동기적으로 처리되어 모두 담기면
그 때 res 변수에 response가 할당이 된다.

그렇다고 모든 걸 이렇게 비동기로 돌릴 수는 없다.
앞의 값을 무조건 받아야 하는 처리이면 어쩔 수 없이 모든 promise 앞에 await를 해주어야 한다.

\* 비동기가 순서대로 동작해야할 때

```jsx
async function bar(n) {
  const a = await new Promise((resolve) => setTimeout(() => resolve(n), 3000));
  // 두 번째 비동기 처리를 수행하려면 첫 번째 비동기 처리 결과가 필요하다.
  const b = await new Promise((resolve) =>
    setTimeout(() => resolve(a + 1), 2000)
  );
  // 세 번째 비동기 처리를 수행하려면 두 번째 비동기 처리 결과가 필요하다.
  const c = await new Promise((resolve) =>
    setTimeout(() => resolve(b + 1), 1000)
  );

  console.log([a, b, c]); // [1, 2, 3]
}

bar(1); // 약 6초 소요된다.
```

#### 디버깅의 개선

\* 콜백함수의 디버깅 문제
콜백 함수에서 디버깅이 어려웠던 이유!

> 콜백 함수에 에러가 발생하면 에러 발생한 함수가 비동기 함수가 아니라 콜백이기 때문에..

```jsx
try {
  setTimeout(() => {
    throw new Error("Error!");
  }, 1000);
} catch (e) {
  // 에러를 캐치하지 못한다
  console.error("캐치한 에러", e);
}
```

() => {
throw new Error("Error!")
}
이 함수가 Error를 발생시킨 것 이지

setTimeout(() => {
throw new Error("Error!")
}, 1000)
이 비동기 함수가 Error를 발생시킨 것이 아니기 때문에 catch문에서 Error를 캐치하지 못한다.

근데 이제 async await을 쓰면 호출자가 명확해진다.

에러 발생 시점이 비동기 함수가 되는 것이기 때문에 try catch문을 사용해서 에러를 캐치할 수 있게 된다.

\* async await에서의 디버깅(try / catch)

```jsx
const foo = async () => {
  try {
    const wrongUrl = "https://wrong.url";

    const response = await fetch(wrongUrl);
    const data = await response.json();
    console.log(data);
  } catch (err) {
    console.error(err); // TypeError: Failed to fetch
  }
};

foo();
```

async 함수는 항상 Promise를 반환한다고 했다.

그렇기 때문에 promise 후속 처리 메서드를 사용해서도 error를 캐치할 수 있다.

\* async await에서의 디버깅(promise 후속 처리 메서드)

```jsx
const fetch = require("node-fetch");

const foo = async () => {
  const wrongUrl = "https://wrong.url";

  const response = await fetch(wrongUrl);
  const data = await response.json();
  return data;
};

foo()
  .then(console.log) //
  .catch(console.error); // TypeError: Failed to fetch
```
