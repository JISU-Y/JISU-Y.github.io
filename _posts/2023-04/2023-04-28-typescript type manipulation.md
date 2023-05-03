---
layout: single
title: "[Typescript] Type Manipulation - js 객체를 ts 타입으로, ts string type을 ts 객체 타입 등으로 컨버팅하기"
categories: Typescript
tag: [Typescript, Javascript, type, typing]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Typescript의 Type Manipulation으로 좀 더 Typescript스럽게

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 항상 사용하다가 type을 어떻게 활용하고, 최대한 any를 사용하지 않으면서 효율적으로 할 수 있을지 고민한다.
util type을 많이 접하고 활용할 줄 알아야 진정히 Typescript를 사용하는 이점을 가져갈 수 있을 것 같아 정리해본다.
</aside>

## Type Manipulation

Javascript Objcet/Array 들을 어떻게 TS의 타입을 변경할 수 있을지, 그 반대는 어떻게 할지  
평소 개발하면서 자주 찾아보는 것들은 정리해보았다.

### JS Object Keys to TS String Literal Types

<aside>
👉 <strong>String Literal Type</strong>

string으로 이루어진 literal type (enum-like behavior with strings)

- **Literal Type**: specific strings and numbers in type  
  리터럴 타입: 특정 `string` 혹은 `number`를 참조할 수 있도록 하는 타입
  `tsx
  let changableString = "default" // type changableString => string
  const unchangableString = "default" // type changableString => "default"
  `
- By combining literals into unions, you can express a much more useful concept  
리터럴들을 union으로 결합하여 더 유용하게 사용할 수 있다.
</aside>

보통 Javascript object에 key와 value 값으로 지정해두고, 코드 곳곳에서 사용한다.

그러다가 object의 key값들을 타입으로 사용하고 싶을 때가 많다.

<br/>

**예시)**

<aside>
👉 <strong>구현 내용</strong>

영어 메뉴를 리스트로 보여주고자 한다.

그 메뉴를 hover 했을 때 한글로 보여주어야 한다고 해보자.
(구현은 안 하겠다. 그냥 한글이 쓸 곳이 어딘가 있다는 것만..)

</aside>

먼저 영어와 한글이 포함된 object가 있으면 좋겠다.

따라서 object를 다음과 같이 생성해서 keys를 가지고 array를 만들어 map을 돌린다.

```tsx
const MENU_WORDS = {
  dessert: "디저트",
  beverage: "음료",
  signature: "대표 메뉴",
  kids: "키즈 메뉴",
};

export default function MenuComponent() {
  return (
    <div>
      <h3>메뉴</h3>
      <ul>
        {Object.keys(MENU_WORDS).map((menuTitle) => (
          <li key={menuTitle}>{menuTitle}</li>
        ))}
      </ul>
    </div>
  );
}
```

간단하다.

근데 만약 대표 메뉴에만 💜를 붙여야 한다면?

`menuTitle`이 `signature`인지 알아야 한다.

```tsx
<div>
  <h3>메뉴</h3>
  <ul>
    {Object.keys(MENU_WORDS).map((menuTitle) => {
      // typeof menuTitle === string
      const isSignature = menuTitle === "signature";

      return (
        <li key={menuTitle}>{isSignature ? `💜${menuTitle}💜` : menuTitle}</li>
      );
    })}
  </ul>
</div>
```

근데 매우 찜찜한 모양새이다.

`menuTitle`의 type은 string이기 때문에 `signature`라는 글자는 톳씨 하나 틀리지 않고 정확히 일치해야 한다.

그런데도 자동 완성이 아닌 내가 적어야 한다. (날 믿지 못해.. 😅)

`const isSignature = menuTitle === MENU_WORDS.signature` 이렇게도 할 수 없다.

`MENU_WORDS`에서 `signature`를 참고하면 한글이기 때문에...

그래서 object를 기반으로 영어로 된 key들을 type으로 만들면 좋을 것 같다.

그럴 땐 `keyof` 키워드를 사용하면 된다.

```tsx
type MenuType = typeof MENU_WORDS; // { dessert: string; .... }
type MenuKeyType = keyof MenuType; // "dessert" | "beverage" | "signature" | "kids"

{
  Object.keys(MENU_WORDS).map((menuTitle) => {
    const isSignature = (menuTitle as MenuKeyType) === "signature";

    return (
      <li key={menuTitle}>{isSignature ? `💜${menuTitle}💜` : menuTitle}</li>
    );
  });
}
```

이렇게 하면 `signature`가 자동 완성이 되고 오탈자가 나도 compile단에서 오류를 잡을 수 있기 때문에 예상치 못한 오류를 방지할 수 있게 된다!

그냥 string은 any나 다름없다고 느낀다..

사실 위의 예는 그렇게 좋지는 않다. 내 머리 속에서 즉흥적으로 나오는 거라.. 😢

### TS Object Type to TS **String Literal** Types

근데 사실 `MenuType` 같은 경우 백엔드에서 넘어오는 데이터일 경우도 꽤 있다.

그것을 이용해서 String Literal Types을 만든다고 하면

```tsx
// 백엔드에서 넘어오는 데이터를 Typing
interface MenuType {
  dessert: "cookie" | "bread" | "waffles" | "cake";
  beverage: "soda" | "coffee" | "non-coffee" | "juice" | "smoothie";
  signature: "signature" | "best" | "today";
  kids: "kids" | "premium-kids";
}

type MenuUnionType = keyof MenuType; // "dessert" | "beverage" | "signature" | "kids"
type MenuArrayType = ReadonlyArray<MenuUnionType>; // readonly (keyof MenuType)[]

const menuArray: MenuArrayType = ["dessert", "beverage", "signature", "kids"];
```

이런 식으로 굳이 클라이언트 단에서 javascript object를 만들지 않고도 원하는 type들만 string literal type으로 만들 수 있겠다.

이후 array로 타입을 변경해서 배열을 이용해서 map을 돌릴 수도 있다!

### TS **String Literal** Types to TS Object Type

이제는 반대로, Union Type들을 이용해서 Object, 혹은 interface type을 만들 수는 없을까..?

나는 나를 믿지 못하기에 한 번 지정된 타입을 이용해서 사골 내듯 우려먹고 변형시키고 싶다.

먼저 위의 예시에서 dessert 또한 메뉴의 서브 메뉴 리스트를 나타내고 싶다면!?

`MENU_WORDS` 처럼 key는 영어, value는 한글로 된 object를 만들고 싶어진다.

물론 그냥 한 땀 한 땀 `cookie`는 “쿠키”, `bread`는 “빵” … 할 수 있다.

하지만 더 typescript스럽게! 더 안전하게! 더 편리하게! 할 수 있는 방법이 있을 것이다.

```tsx
interface MenuType {
  dessert: "cookie" | "bread" | "waffles" | "cake";
  beverage: "soda" | "coffee" | "non-coffee" | "juice" | "smoothie";
  signature: "signature" | "best" | "today";
  kids: "kids" | "premium-kids";
}

// 먼저 Indexed Access Types을 이용해서 dessert의 타입만 꺼내온다.
type DessertType = MenuType["dessert"]; // "cookie" | "bread" | "waffles" | "cake"
type DessertObjectType = {
  [K in DessertType]: string;
};

// Mapped Type을 이용해서 string literal type들의 항목들을 key로 꺼내온다.
const DESSERT_WORDS: DessertObjectType = {
  cookie: "쿠키",
  bread: "빵",
  waffles: "와플",
  cake: "케이크",
};
```

`in` 키워드를 이용해서 object type을 지정할 수 있다.

이렇게 하면 object를 작성할 때 자동 완성이 되어 훨씬 편리해진다!!

결국 `{[K in DessertType]: string}` 이것이 union type을 하나씩 key로 사용할 수 있도록 해주는 것이다.

string 말고 다른 타입을 지정하고 싶다면?

generic을 이용해서 util type을 만들 수 있을 것 같다.

```tsx
type UnionToObjectType<KeyType extends string, ValueType> = { [Property in KeyType]: ValueType }

type ObjectConvertedDessertType = UnionToObjectType<MenuType["dessert"], boolean>
// 결과
**{
    cookie: boolean;
    bread: boolean;
    waffles: boolean;
    cake: boolean;
}**
```

### JS Array to TS **String Literal** Types

또한 array 데이터에서 string literal type을 꺼내올 수 있다.

```tsx
const customerList = ["customer1", "customer2", "customer3", "customer4"] as const;
// string이 아닌 요소의 특정 문자를 원하기 때문에 as const 사용

type NeededUnionType = **typeof** customerList**[number]**; // **"customer1" | "customer2" | "customer3" | "customer4"**

// array의 keys(숫자)들을 type으로 꺼낼 수도 있다.
const numberKeys = Object.keys(customerList)
console.log(numberKeys); // ["0", "1", "2", "3"] // 물론 꺼내온 숫자들은 string 타입이다.
```

### JS Object values to TS **String Literal** Types

Javascript 객체의 key가 아닌 value로 string literal type을 만들고 싶을 때는?

```tsx
const menus = {
  dessert: "디저트",
  beverage: "음료",
  signature: "대표 메뉴",
  kids: "키즈 메뉴",
} as const; // as const를 하지 않으면 모두 string으로 나오게 된다.

// key는 매우 쉽다.
type MenusObjectType = typeof menus; // { dessert: "디저트", beverage: "음료", signature: "대표 메뉴", kids: "키즈 메뉴" }
type MenusKeyType = keyof typeof menus; // "signature" | "kids" | "dessert" | "beverage"

// typeof 키워드와 Indexed Access Types을 이용해 value를 참조하여 Type으로 만들 수 있다.
type MenusValuesType = (typeof menus)[MenusKeyType]; //  "디저트" | "음료" | "대표 메뉴" | "키즈 메뉴"
type MenusValueType = (typeof menus)[keyof typeof menus]; // "디저트" | "음료" | "대표 메뉴" | "키즈 메뉴"
```

- 제네릭을 사용하여 util type을 만들어보면 이렇게 할 수 있겠다.

  ```tsx
  type Values<ObjectType> = ObjectType[keyof ObjectType];

  type MenusValuesType = Values<MenusObjectType>; //  "디저트" | "음료" | "대표 메뉴" | "키즈 메뉴"
  ```

- array의 요소가 object일 때 object의 특정 속성의 value 또한 union type으로 꺼내올 수 있다.

  ```tsx
  const keyToValArray = [
    { value: "myValue1", label: "myLabel1" },
    { value: "myValue2", label: "myLabel2" },
  ] as const;

  // keyToValArray[number] => { value: 'myValue1', label: 'myLabel1' }
  type ObjectElementKeys = (typeof keyToValArray)[number]["value"]; // 'myValue1' | 'myValue2'
  ```

### 참고 링크

[https://www.typescriptlang.org/docs/handbook/2/mapped-types.html](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html)

[https://bobbyhadz.com/blog/typescript-create-union-type-from-array#create-a-union-type-from-an-array-in-typescript](https://bobbyhadz.com/blog/typescript-create-union-type-from-array#create-a-union-type-from-an-array-in-typescript)

[https://www.typescriptlang.org/docs/handbook/2/conditional-types.html](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)

[https://stackoverflow.com/questions/45251664/derive-union-type-from-tuple-array-values](https://stackoverflow.com/questions/45251664/derive-union-type-from-tuple-array-values)

[https://stackoverflow.com/questions/55127004/how-to-transform-union-type-to-tuple-type](https://stackoverflow.com/questions/55127004/how-to-transform-union-type-to-tuple-type)

<hr>
