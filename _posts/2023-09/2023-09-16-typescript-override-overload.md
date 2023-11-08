---
layout: single
title: "[Typescript] override와 overload"
categories: Typescript
tag: [Typescript, Javascript, type, function, override, overload]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Typescript - 오버로딩과 오버라이딩

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 문득 예전에 공부했던 것 중 오버라이딩 개념에 대해서 본 적 있는데 이후로 알아보지 못해 이번에 알아보게 되었다.

</aside>

## 오버로딩 / 오버라이딩

> Javascript에서 오버로딩과 오버라이딩의 정의를 알아보도록 한다.

### 오버라이딩 (Override)

> 하위 클래스가 상위 클래스의 메서드를 재정의하는 것

**특징**

- 보통 클래스 상속에서 사용.

  → 물론 함수에서도 사용할 수는 있지만 강력한 super 키워드를 사용하지 못한다거나 귀찮게 closure를 사용해서 구현해야 한다.

- 하위 클래스에서 부모 클래스의 메서드와 동일한 이름의 메서드를 정의하여 다형성과 코드 재사용성을 높임.

- 덮어쓰기라고 생각할 수도 있다. (물론 상속받은 것을 없애버리지는 않지만)

### 오버로딩 (Overload)

> 같은 이름을 가진 함수/메서드가 다양한 형태의 파라미터를 받아 각기 다르게 동작하도록 설계하는 것
>
> 그러나, JavaScript에서는 <u>전통적인 방식의 메서드 오버로딩이 지원되지 않는다.</u>

**특징**

- 오버로딩은 함수의 시그니처(function definition)가 같은 이름을 가지지만 **인자의 개수나 타입을 다르게 하여** 사용성을 높임
- Javascript 에서는 명시적으로는 지원되지 않고 아래와 같이 구현할 수 있다.
  - Javascript에서는 인자들의 타입이 원래도 자유로움.
  - 개수는 arguments 나 rest 파라미터를 이용해서 비슷하게 구현 가능함.

아래와 같이 비교하기도 함. (너무 강력한 그림이라.. 다른 대안의 이미지를 찾지 못함)

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-09/16-overload-override.png)

## Javascript에서 활용

### 오버라이딩 (Override)

```jsx
class Developer {
  makeSound() {
    console.log("어?");
  }
}

class FrontDev extends Developer {
  makeSound() {
    super.makeSound(); // Developer에 정의된 makeSound 메서드를 오버라이딩.
    console.log("화면 이거 왜 이래.");
  }
}

const frustratedDeveloper = new Developer();
frustratedDeveloper.makeSound();
// 어?

const frontendDeveloper = new FrontDev();
frontendDeveloper.makeSound();
// 어?
// 화면 이거 왜 이래.
```

`Developer` 클래스를 상속받은 `FrontDev` 클래스를 이용해서 생성한 인스턴스 `frontendDeveloper`에서 `makeSound`라는 메서드를 사용했다.

이 `frontendDeveloper`의 `makeSound` 메서드를 사용하면 `Developer` 클래스의 `makeSound` 또한 같이 출력되는 것을 확인할 수 있다.

`Developer` 특성을 그대로 이어 받고, 추가적으로 `override`하여 `FrontDev` 클래스 만의 특성을 더해준 것으로 활용 가능한 모습이다.

### overload

javascript 에서 제공하지 않는 overloading을 arguments, rest 파라미터로 비슷하게 구현할 수 있다.

- arguments

  ```jsx
  function add() {
    if (arguments.length === 2) {
      if (
        typeof arguments[0] === "string" &&
        typeof arguments[1] === "string"
      ) {
        return arguments[0] + " " + arguments[1];
      }
      if (
        typeof arguments[0] === "number" &&
        typeof arguments[1] === "number"
      ) {
        return arguments[0] + arguments[1];
      }
    }
    return null;
  }

  console.log(add("Hello", "world")); // "Hello world"
  console.log(add(1, 2, 3, 4)); // null
  ```

- …rest 파라미터

  ```jsx
  function add(...args) {
    if (args.length === 2) {
      if (typeof args[0] === "string" && typeof args[1] === "string") {
        return args[0] + " " + args[1];
      }
      if (typeof args[0] === "number" && typeof args[1] === "number") {
        return args[0] + args[1];
      }
    }
    return null;
  }

  console.log(add("Hello", "world")); // "Hello world"
  console.log(add(1, 2)); // 3
  console.log(add("Hello", 2)); // null
  ```

## Typescript에서 활용

왜 여태까지 오버로딩과 오버라이딩을 알아보았나?

Typescript에서 이런 overloading을 사용해보고 싶기 때문.

⇒ 유연한 파라미터를 가진 함수를 사용하고 싶으니까! (그러면서도 안전한..!)

### 활용 예시

예를 들어, 파라미터를 string, number 둘 다 받고 싶은데 완전 믹스하고 싶지는 않고,

string을 받으면 두번째 파라미터도 string, 첫번째 파라미터가 number면 두번째도 number로 받게 하고 싶다.

**any를 쓰면 당연히 안되겠지?**  
→ 시간이 없을 때만 쓰는 걸로.

```tsx
function add(a: any, b: any): any {
  return a + b;
}

console.log(add("Hello ", "Steve")); // returns "Hello Steve"
console.log(add(10, 20)); // returns 30
console.log(add("Hello ", 20)); // 아마 에러
console.log(add(true, false)); // 1 (* Javascript 타입 강제 형변환으로 인해 number로 변해 1 + 0이 됨)
```

**유니온 타입?**  
→ 의외로 되지 않는다. 😧

```tsx
function add(a: string | number, b: string | number): string | number {
  return a + b; // Error: Operator '+' cannot be applied to types 'string | number' and 'string | number'.(2365)
}
```

**Generic 될 것 같은데?**  
→ 배신이다. 안된다. 😱

```tsx
function add<T extends string | number>(a: T, b: T): T {
  return a + b; // Error: Operator '+' cannot be applied to types 'T' and 'T'
}
```

TypeScript에서 **`T extends string | number`** 와 같은 제네릭을 사용 시 **`+`** 연산자는 두 타입이 **`string`** 또는 **`number`** 중 하나로 일치해야 함을 보장하지 않는다.  
(피연산자의 타입이 일치해야 한다.)

**`T`** 가 **`string`** 이 될 수도 있고 **`number`** 가 될 수도 있기 때문에,  
이 두 타입에 대한 **`+`** 연산은 서로 다르기 때문에 컴파일러가 이를 허용하지 않음.

위의 유니온 타입도 마찬가지.

→ 그래서 굳이 + 연산자를 사용하지 않고 string(`${a} + ${b}`)으로만 return 해준다면 상관없다.

**그래서 오버로딩!**

```tsx
// first signature = function definition
function add(a:string, b:string):string;
// second signature
function add(a:number, b:number): number;
// 오버 로딩 함수 구현체(implementation)는 any 로 지정해야 한다.
// any로 지정해도 아무거나 넣을 수 있는 것이 아니고 definition에 지정된 type만 가능하다.
**function add(a:any, b:any): any {
    return a + b;
}**

console.log(add("Hello ", "Steve")); // returns "Hello Steve"
console.log(add(10, 20)); // returns 30
console.log(add(true, false)); // error: No overload matches this call.
```

인자로 boolean 값을 넣으면 Error 발생

```
No overload matches this call.
	Overload 1 of 2, '(a: string, b: string): string', gave the following error.
	Argument of type 'boolean' is not assignable to parameter of type 'string'.
	Overload 2 of 2, '(a: number, b: number): number', gave the following error.
	Argument of type 'boolean' is not assignable to parameter of type 'number'.
```

**어어 나왔다 "그 에러"**

`No overload matches this call.`

> 호출하려는 함수에 맞는 시그니처가 없을 때 발생하는 에러.

함수가 가지고 있는 function declarations의 시그니처들이 오버로딩 되어 있을 수 있다.

그러나, 여기서는 add라는 함수에 boolean 값을 받는 다는 시그니처 즉, function declaration이 없기 떄문에

이러한 에러가 발생하게 되는 것이다!

### 참고 링크

https://www.tutorialsteacher.com/typescript/function-overloading

https://carldesouza.com/function-overloading-in-javascript/

https://www.zerocho.com/category/JavaScript/post/59c17a58f40d2800197c65d6

<hr>
