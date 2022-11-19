---
layout: single
title: "[Typescript] Typescript의 Conditional Type, Infer Type, Type guard"
categories: Typescript
tag: [Typescript, util type]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Typescript Util

Typescript 언어에서 Conditional Type은 무엇인지, 또 Infer 타입과 Type guard까지 알아본다.

## Conditional Type

### Conditional Type란?

> 삼항연산자를 이용해서 조건적으로 지정하는 타입

```tsx
Type ConditionType<T, U> = T extends U ? X : Y
```

**T**가 **U**에 포함되면 **X라는 타입**을, 아니라면 **Y라는 타입**을 반환해주는 타입을 지정할 수 있다.

☝ **extends**는 **부분 집합**의 개념으로 이해하면 쉽다!

### 조건 형식

삼항 연산자의 조건문에 올 수 있는 문법은 TypeScript 4.3 기준으로 **T extends U 형식**밖에 없다.
아직까지 타입에서 조건을 나타낼 수 있는 문법이 extends 키워드 밖에 없다.  
⇒ [참고 아티클](https://driip.me/b812974b-3974-46e3-829e-1476b9b30c94)

(🤔 이 부분이 궁금해서 찾아보는데 잘 안나오는 것 같다.)

### 예시

변수 생성 시 인자로 넘어오는 값에 따라서 다르게 타입을 주려면

```tsx
type IdLabel = { id: number };
type NameLabel = { name: string };
```

🤨 **아마도 내가 했다면**

return을 분기처리해도 어차피 NameLabel, IdLabel 둘 중 하나이니 이렇게 처리하지 않았을까?

```tsx
function createLabelMe<T extends number | string>(
  idOrName: T
): NameLabel | IdLabel {
  if (typeof idOrName === "number") {
    return { id: idOrName as number };
  }

  return { name: idOrName as string };
}

let aa = createLabelMe("typescript"); // aa: NameLabel | IdLabel

let bb = createLabelMe(2.8); // bb: NameLabel | IdLabel

let cc = createLabelMe(Math.random() ? "hello" : 42); // cc: NameLabel | IdLabel
```

😊 **Docs에는**

타입 파라미터에 따라서 return type이 정해지게 타이핑했다!  
-> Conditional Type을 이용!

```tsx
type NameOrId<T extends number | string> = T extends number
  ? IdLabel
  : NameLabel;

function createLabel<T extends number | string>(idOrName: T): NameOrId<T> {
  throw "unimplemented";
}

let a = createLabel("typescript"); // a: NameLabel

let b = createLabel(2.8); // b: IdLabel

let c = createLabel(Math.random() ? "hello" : 42); // c: NameLabel | IdLabel
```

인자 `idOrName`는 number이거나 string일 수 있다.

그러나 반환 타입은 모든 타입을 허용하기 보다 string인자가 들어왔을 때는 string으로 반환하고,
number가 들어왔을 때는 number 타입으로 반환하도록 하려면 Conditional Typing 하면 된다.

⇒ **반환 타입**도 Conditional Typing하여 **조건적으로 지정된 타입**을 사용할 수 있게 된다.

좀 더 Typescript스럽게!

<br/>

#### 🙊 왜 createLabel 함수는 return을 제대로 해주지 않을까?

```tsx
function createLabel<T extends number | string>(idOrName: T): NameOrId<T> {
  throw "unimplemented";
}
```

\*[스택 오버플로우](https://stackoverflow.com/questions/66552211/minimal-implementation-of-function-with-conditional-type-return-type-in-typescri)를 참조했습니다.

이는 타입스크립트의 한계라고 할 수 있다!

제네릭 타입, 특히 Conditional Type에서 불확실한 타입은 Typescript가 컴파일 단에서 확정지을 수 없다.

타입 파라미터가 평가될 때 까지는 아무도 모르므로 return 타입을 어느 하나로 특정할 수 없다.

타입 단언으로 하려면 할 수는 있다.

```tsx
function createLabel<T extends number | string>(idOrName: T): NameOrId<T> {
  if (typeof idOrName === "number") {
    return { id: idOrName as number } as NameOrId<T>;
  }
  return { name: idOrName as string } as NameOrId<T>;
}
```

Type Assertion이 좋거나 나쁘다기 보다 그냥 개인이 보기에 좋지 않다면 다른 방법도 있다.

다른 방법은 타입스크립트의 `Function overloads` 를 사용하는 것!

[Typescript 핸드북 - overloads](https://www.typescriptlang.org/docs/handbook/2/functions.html#function-overloads)

```tsx
// overload signatures
function len(s: string): number;
function len(arr: any[]): number;

// implementation signature
// body of the function
function len(x: any) {
  return x.length;
}
```

따라서 이를 사용해보면, createLabel Overloads signature에서 T를 사용해서 Conditional Typing하면 된다고 한다.

컴파일러는 Overload function에서 return 타입이 IdLabel | NameLabel인 것을 알기 때문에 타입 단언이 없어도 컴파일 단 타입 에러가 나지 않는다고 합니다.

```tsx
function createLabel<T extends number | string>(idOrName: T): NameOrId<T>;
function createLabel(idOrName: number | string): NameOrId<number | string> {
  if (typeof idOrName === "number") return { id: idOrName };
  return { name: idOrName };
}
```

### Conditional Type의 활용

**Pick처럼 특정 타입의 특정 속성 타입만 가져오고 싶을 때!**

```tsx
type MessageOf<T> = T["message"];
```

❌ 에러: `Type '"message"' cannot be used to index type 'T'.`

당연하게도, 에러가 발생한다.  
⇒ T type에 `message`가 있는지 없는지 모르기 때문.

🤔 나름 고쳐보기

```tsx
type MessageOf<T extends { message: unknown }> = T["message"];

interface Email {
  message: string;
}

type EmailMessageContents = MessageOf<Email>;
```

그냥 T에 constraint(제약조건)을 두면 되는 것 아닌가!?  
아싸 잘 된다. 끝! 😆

🧐 그런데 만약에 `message`가 없어도 타입을 특정 타입으로 뱉고 싶게 한다면?

→ 이 때 Conditional Typing으로 타입을 지정해보기!

```tsx
// T는 아무거나 와도 됨. 조건부가 처리해줄 것.
type MessageOf<T> = T extends { message: unknown } ? T["message"] : never;

interface Email {
  message: string;
}

interface Dog {
  bark(): void;
}

type EmailMessageContents = MessageOf<Email>; // EmailMessageContents: string

type DogMessageContents = MessageOf<Dog>; // DogMessageContents: never
```

T 타입은 아무거나 일단 받고(`message`가 없더라도),

Type 안 조건부에서 **extends**로 true/false 확인 후 **false 인 경우 never를 반환**해주면 된다.

<br/>

무궁무진히 고도화할 수 있을 것 같은 Conditional Type이다.

---

## Infer 키워드

### Infer란?

> There is an **`infer`** keyword that can be used within a condition in a conditional type to put the **inferred type into a variable**. That inferred variable can then be **used within the conditional branches.**

즉, Conditional Type에서 **타입을 변수화해서 사용**할 수 있는 키워드.

변수화한 타입(inferred variable)을 **조건부 브랜치에 사용**할 수 있다.

```tsx
type ConditionalTypeWithInfer<T> = T extends { property: infer R } ? R : T;
```

⇒ infer 키워드와 지정한 네이밍 타입(R)을 사용하며, infer를 이용해서 T의 타입에 있는 타입을 가져와서 True/False 브랜치에 사용한다.

❓그냥 설명만 보면 무슨 말인지 감이 안잡힌다.

그러나 설명보다 사용하는 게 더 쉬운 infer!

### 예시

```tsx
// ArrayType을 받아 그 Array의 요소가 무슨 타입인지 확인하여 요소의 타입을 반환해주는 조건부 타입
type ArrayElementType<T> = T extends (infer R)[] ? R : T;

type NumberItemType = ArrayElementType<number[]>;
// type of NumberItemType is `number`

type NotArrayType = ArrayElementType<{ name: string }>;
// type of NotArrayType is `{name: string}`
```

`ArrayElementType` 은 특정 타입의 배열을 받아서 그 배열의 요소 타입을 타입으로 지정해줌, 배열이 아닌 타입이라면 그냥 받은 타입 그대로 지정하기

😌 **차근차근 보기**

`NumberItemType` 은 `ArrayElementType`에 `number[]` 타입을 넣었다. 그럼 어떤 타입이 반환될까?

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
👉 일단 `T extends (infer R)[]` 이 조건을 만족하는지 먼저 봐보자

**T는 배열 형태**이어야 만족하는 조건이라는 뜻이다!

→ `number[]`은 배열이기 때문에 **조건은 만족**한다.

</aside>

여기서 **infer**는 그래서 무슨 역할을?  
→ **infer 이 키워드를 사용해서 number 타입을 쏙! 빼올 수 있는 것**이다!

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px; margin-bottom: 10px'>
👉 `T extends (infer R)[]` 이 조건을 만족하고, 거기서 빼온 R

즉, `NumberItemType`에서 R은 `number`가 되어 이를 True 브랜치에 사용함으로써 타입을 지정할 수 있다.

</aside>

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
👉 `NotArrayType`은 `{ name: string }` 타입을 넘겨주었다.

다시 조건을 보면 배열이어야 하는데, 이 타입은 배열이 아니다.  
그렇다면 그냥 False 브랜치에 있는 T를 반환해주므로 그대로 `{ name: string }` 타입이 되겠다.

</aside>

처음에 이것을 이해하는지 왜 어려웠는지는 모르지만  
한 번 이해하고 나면 너무 간단한 유틸리티 키워드라 조금 어이없는 부분이 있다. 😵‍💫

### 활용: 함수의 반환 타입

이 infer 키워드는 **함수의 반환 타입**을 타입마다 다르게 하고 싶을 때 많이 사용하게 된다!

```tsx
type FunctionReturnType<T> = T extends (...args: any) => infer R ? R : T;
```

⇒ T로 받은 타입이 **함수일 때**는 infer 키워드로 R 타입(**반환 타입**)을 가져오고 **아니라면 그냥 T**로 지정한다.

```tsx
function isOddNumber(num: number): boolean {
  return num % 2 === 1;
}

function getUserNameById(userId: number): string {
  // get userName from somewhere
  return "jisu-yoo";
}

function whatAboutVoidFunc(): void {
  // ...
}
```

😶‍🌫️ 다음의 타입은? (쉬움 주의)

```tsx
type CheckOddNumberType = FunctionReturnType<typeof isOddNumber>;
type UserNameType = FunctionReturnType<typeof getUserNameById>;
type VoidType = FunctionReturnType<typeof whatAboutVoidFunc>;
```

- 타입은?
  ```tsx
  CheckOddNumberType: boolean
  UserNameType: string
  VoidType: void
  ```

**🙄 좀 더 디벨롭**

```tsx
const ObjectType = { numberProperty: number };

type WhatType = FunctionReturnType<ObjectType>; // ?
```

ObjectType도 FunctionReturnType 타입 파라미터를 통해 넘겨줄 수 있다

⇒ 제네릭 자체는 constraint(제약조건)이 없기 때문에, `T extends (...args: any) => infer R` 이 조건을 확인하게 된다.

그런데 이 조건에서 **T 타입은 함수여야 하는 조건**이 있다.

⇒ 그러므로 False 브랜치가 타입으로 지정되게 되므로 **그대로 T 타입인 ObjectType**이 지정된다.

근데 어차피 함수만 return type을 확인하고 싶은데 굳이 함수 타입이 아닌 타입까지 파라미터로 받아야할까 싶은 것이다.

```tsx
type FunctionReturnType<T extends (...args: any) => any> = T extends (
  ...args: any
) => infer R
  ? R
  : T;
```

애초에 함수 타입만 가능하도록 지정해두면 함수 타입이 아닌 타입을 넘겨주었을 때 Type error가 발생되므로 사용자가 더 편할 수 있지 않을까? 🥺

### 활용: Template Literal + Infer 조합

토스에서도 Template Literal을 많이 이용한다고 한다.

```tsx
// 출처: 토스
type InOrOut<T> = T extends `fade${infer R}` ? R : never;

type I = InOrOut<"fadeIn">; // type I = "In"
type O = InOrOut<"fadeOut">; // type O = "Out"
```

### 활용: 재귀

```tsx
// 출처: 토스
type TrimRight<T extends string> = T extends `${infer R} `
  ? TrimRight<R> // 오른쪽 띄어쓰기가 없을 때까지 Recurse
  : T;

// type T = "Trim"
type T = TrimRight<"Trim      ">;
```

⇒ Type에서는 if / for / while을 사용할 수 없어 재귀적으로 구현한다.

이 재귀를 이용하여 nesting된 타입, Literal Type에서 반복적인 string 없애기 등 활용도가 매우 높다!

그러나 어렵다. 😵

---

## Typescript Type Guard

### Type Guard란?

> Type Guards allow you to **narrow down the type** of an object within a conditional block.

조건문을 통해 **특정 타입만 사용하도록 타입을 좁혀주는 것**을 타입 가드라고한다.

### 구현

구현 자체는 어렵지 않다.

보통 **조건문**으로 타입을 확인하는 형태로 **분기 처리**해준다.

👀 **타입을 확인하는 네가지 방법**

1. **typeof** 로 변수의 타입과 특정 타입을 비교하기

   ```tsx
   function formatTime(x: number | string): CustomDateType {
     if (typeof x === "string") {
       // 이 블록에서 x는 무조건 string 타입
       const date = x; // ... CustomDateType으로 바꿔줄 어떤 연산
       return date;
     }

     const date = x; // ... CustomDateType으로 바꿔줄 어떤 연산

     const firstLetterOfX = x[0];
     // => **Error: There is no guarantee that `x` is a `string`**

     return date;
   }
   ```

2. **instanceof** 로 변수가 특정 타입으로부터 생성된 인스턴스와 특정 타입을 비교하기

   ```tsx
   class Common {
     common = "123";
   }
   class Foo extends Common {
     foo = 123;
   }
   class Bar extends Common {
     bar = 123;
   }

   function doSomething(arg: Foo | Bar) {
     let obj = {
       common: arg.common,
       whatever: null,
     };

     if (arg instanceof Foo) {
       obj.whatever = arg.foo; // arg.bar는 참조 불가
     }
     if (arg instanceof Bar) {
       obj.whatever = arg.bar; // arg.foo는 참조 불가
     }

     return obj;
   }

   doSomething(new Foo());
   doSomething(new Bar());
   ```

3. **in** 키워드 사용

   ```tsx
   interface A {
     x: number;
   }
   interface B {
     y: string;
   }

   function doStuff(q: A | B) {
     if ("x" in q) {
       // q: A
     } else {
       // q: B
     }
   }
   ```

4. **literal type**인 경우 바로 비교

   ```tsx
   type TriState = "yes" | "no" | "unknown";

   function logOutState(state: TriState) {
     if (state === "yes") {
       console.log("User selected yes");
     } else if (state === "no") {
       console.log("User selected no");
     } else {
       console.log("User has not made a selection yet");
     }
   }
   ```

### user defined type guard

🥳 **is 키워드**를 이용해서 커스텀 타입 가드 함수를 만들 수도 있다.

```tsx
type Fish = {
  swim: () => void;
};

type Bird = {
  fly: () => void;
};

/**
 * User Defined Type Guard!
 */
function GuardFishType(pet: any | Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}

/**
 * 그냥 boolean값 판단 함수
 */
function isFish(pet: any | Fish | Bird): boolean {
  return (pet as Fish).swim !== undefined;
}
```

**Boolean 판단 함수**

```tsx
function isFish(pet: any | Fish | Bird): boolean {
  return (pet as Fish).swim !== undefined;
}

function example(randomAnimal: any) {
  if (isFish(randomAnimal)) {
    console.log(randomAnimal + "lives in water and can" + randomAmimal.swim);
    console.log(randomAnimal.fly); // 컴파일 단에서도 전혀 문제되지 않는다.
  }
}
```

`randomAnimal`이 `Fish`일 때 조건 블럭이지만, `randomAnimal` 타입 자체는 여전히 `any`이다.

⇒ 일반적으로 `Fish`인지 아닌지를 `boolean`으로 판단하는 함수는 Type narrowing이 되지 않는다.

⇒ 따라서 Fish에 없는 fly 속성을 참조하려다가 컴파일 단에서는 문제가 안되겠지만, 매우 찜찜한 모양새이다. 😔

**User Defined Type Guard**

⇒ is 키워드를 사용하면 `GuardFishType` 함수가 `pet` 인자를 받아서 검수가 끝난 후 **return으로 true**를 해줄 때,  
typescript가 전달받았던 변수의 **타입을 `Fish`로 좁혀준다**.

```tsx
function GuardFishType(pet: any | Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}

function example(randomAnimal: any) {
  if (GuardFishType(randomAnimal)) {
    console.log(randomAnimal + "lives in water and can" + randomAmimal.swim);
    // Type이 좁혀진 블록이므로 접근 불가능
    // 이를 Typescript가 컴파일단에서 잡아줌
    console.log(randomAnimal.fly); // **Error: Property 'fly' does not exist on type 'Fish'**
  }

  // 사실 이 블록에서는 Typescript가 보호하지 않는 부분이기 때문에 fly에 접근해도 가능하다.
  // 컴파일에는 문제가 없으나 런타임에서 문제가 될 코드.
  console.log(randomAnimal.fly);
}
```

이렇게 `Type Guard`를 사용하면 타입 가드가 적용되는 블록에서는 컴파일에서 오류를 잡을 수 있다.

🙃 그러나, early return이 되지 않는 이상, Typescript가 보호하는 블럭은 if문 블록이기 때문에
if문 블록 밖에서 참조는 가능하므로 주의해야한다.

**User Defined Type Guard 실적용!** 😝

**`Recoil`**의 `selector` set 프로퍼티는 다른 `atom`을 조작할 수 있는 set 함수와 reset 함수를 제공하고 있다.

set 프로퍼티의 함수에서 인자로 넘어온 값이 없을 때 `DefaultValue`로 타입이 지정되는데,

이 때 인자를 타입 구분하여 set과 reset을 해주어야한다.

```tsx
// DefaultValue Type guard
export function guardRecoilDefaultValue(arg: unknown): arg is DefaultValue {
  return arg instanceof DefaultValue;
}
```

```tsx
export const atomSelector = selector<Item>({
  key: "recoil-key",
  get: ({ get }) => {
    // get이라는 함수
  },
  // **newItem: DefaultValue | Item**
  set: ({ set, reset }, newItem) => {
    // atomSelector를 reset으로 call 했을 경우 이 블럭을 진행
    if (guardRecoilDefaultValue(newItem)) {
      // **newItem: DefaultValue**
      reset(atomState);
      return;
    }

    // atomSelector를 set으로 사용했을 경우 여기를 진행
    // **newItem: Item**
    set(atomState, newItem);
  },
});
```

사실, 이 키워드와 기능들은 매우 단순한 예시들인데,  
고도화 하려면 생각보다 머리를 많이 싸매야 해서 아직은 좀 역부족인 것 같았다..

Typescript를 진짜 다른 언어로서 공부를 많이 해야겠다고 생각했다. 😇

---

### 참고 아티클

[https://www.typescriptlang.org/docs/handbook/2/conditional-types.html](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)

[https://toss.tech/article/template-literal-types](https://toss.tech/article/template-literal-types)

[https://learntypescript.dev/09/l2-conditional-infer](https://learntypescript.dev/09/l2-conditional-infer)

[https://basarat.gitbook.io/typescript/type-system/typeguard#type-guard](https://basarat.gitbook.io/typescript/type-system/typeguard#type-guard)

<hr>
