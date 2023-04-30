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

# i18n (Internationalization)

## i18n

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 도대체 i18n이 무엇일까.. 지나가다 본 적만 있고, 제대로 알아 본 적은 없어서 <br/>
이번 기회에 어떤 것인지 알아보기로 했다. (사실 찍먹임.)

- 주의: 참고만 하시고 더 좋은 official한 설명을 찾아보시는 것을 추천합니다.
</aside>

### 줄임말

`Internationalization`의 축약형

i와 n 사이에 18글자가 있기 때문.

a11y(accessiblity)와 l10n(localization), t9n(translation) 등등 모두 같은 원리

### 뜻

SW 국제화

→ 국제적으로 통용되는 SW를 설계하고 개발하는 것.

국제적으로 서비스하려고 하는 앱은 필수라고 할 수 있다.

## i18n vs l10n 비교

그렇다면 i18n과 l10n의 차이점은 무엇일까?

### i18n

서비스를 **국제화** 시키는 것.

**고려되어야 하는 사항**

- 언어 번역: 모든 사용자가 알아들을 수 있는 언어를 다양하게 지원하여 국제화.
- 유니코드: 문자들을 컴퓨터에서 일관되게 표현하며 다룰 수 있도록 설계된 산업 표준
  ⇒ 유니코드를 이용해서 모든 언어를 나타낼 수 있기 때문!
- 리소스 외부화 및 관리: 이미지, 소스코드 등으로부터 리소스 분리
  ⇒ 프로그램 수정 없이 다국어 지원 가능하도록!
- 로케일 대응: 날짜, 시간 형식, 달력, 통화, 문자열 정렬 순서 등 다수 로케일에 유연하게 대응
  ⇒ 마찬가지로 나라마다 다른 형식들을 하드코딩이 아닌 자동으로 적용되도록 고려!
- Localizability(Internationalization and localization): 지역화 가능성 (서비스의 재설계나 개발없이도 다양한 언어에 적용될 수 있는 정도)
  ⇒ 번역될 경우 UI에 미치는 영향 분석
  ex) 텍스트 확장/축소, 아랍어/중국어 등과 같은 비라틴어 지원, 텍스트 방향 등

### l10n

서비스를 **현지화** 시키는 것.

⇒ 서비스의 타겟 시장의 문화 및 요구 사항에 맞게 서비스를 바꾸는 작업

(서비스가 완성이 된 이후 작업할 수도, 서비스를 만들어 나가는 과정에서 agile하게 작업할 수도 있다.)

이 현지화(l10n)을 할 수 있도록 기반이 되어 있어야 하는 작업이 국제화(i18n)

**고려되어야 하는 사항**

- 언어 번역: 현지화 언어 번역
- 문화적, 사회적 처리: 언어 뿐만 아니라 그 나라에 맞게 대응

  - ex) 총 쏘는 게임

    ![l10n_example]({{ site.url }}{{ site.baseurl }}/assets/images/2023-04/l10n_example.png)

    이미지 출처: [[https://miaow-miaow.tistory.com/32](https://miaow-miaow.tistory.com/32)]

## i18n 실제 사용

`i18next`는 거의 모든 라이브러리/프레임워크를 지원하고 있음.

react에는 `react-i18next` next에는 `next-i18next` 를 사용함.

**i18next config setting**

1. 패키지 install

   next에서는 세 패키지 모두 설치해야 한다.

   ```bash
   npm i i18next react-i18next next-i18next
   ```

2. next-i18next config 설정

   ```jsx
   // next-i18next.config.js

   module.exports = {
     i18n: {
       locales: ["en", "ko"],
       defaultLocale: "ko",
       localeDetection: true,
     },
     localePath:
       typeof window === "undefined"
         ? require("path").resolve("./public/locales")
         : "/locales",
   };
   ```

   - localePath를 설정해주는 이유
     vercel 등 클라우드 플랫폼에 배포하는 경우, SSR 코드의 경우 window 객체가 없을 수 있다.
     따라서 위와 같이 정의해준다고 한다.

3. next config 수정

   ```jsx
   // next.config.js
   const { i18n } = require("./next-i18next.config.js");

   /** @type {import('next').NextConfig} */
   const nextConfig = {
     i18n,
     reactStrictMode: true,
   };

   module.exports = nextConfig;
   ```

4. 언어 팩 json 파일 생성

   기본적으로 다음과 같은 폴더 구조를 가지고 있어야 한다.

   common.json 파일이 없는 경우 error가 발생한다.

   ⇒ 빈 파일만 있어도 상관없다. 무조건 생성 필수!

   ![l10n_example]({{ site.url }}{{ site.baseurl }}/assets/images/2023-04/common_error.JPG)

   ```
   .
   └── public
       └── locales
           ├── en
           |   └── common.json
           └── ko
               └── common.json
   ```

   common안에 모든 key들을 넣어서 사용할 수 있겠지만,

   단어 마다 key를 같은 레벨에 나열하면 너무 알아보기 힘들 것이고,

   그렇다고 nesting을 하자니 chaining이 길어지면 사용할 때 헷갈릴 것이다.

   (아무래도 사용할 때는 string이기 때문에 chaining 하더라도 자동완성이 안되니 휴먼에러 발생 확률 높음)

   ex)

   ```jsx
   // public/locales/en/common.json
   {
     "h1": "title",
     "description": "This app is for all over the world",
     "result": {
   		"success": "success!",
   		"failure": "failure!",
   		"warning": "warning!",
   	},
   	... // 밑으로 만 개... 😫
   }

   // 컴포넌트 단에서 사용 시
   console.log(t("result.success")) // 휴먼 에러 발생 가능성! 😫
   ```

   그래서 많은 사람들이 따로 파일을 만들어 사용한다.

   파일을 나누는 기준은 취향따라 하면 될 것 같다.

   일단은 도메인/페이지 별로 나누기로 한다.

   ```jsx
   // public/locales/en/home.json
   {
     "country": "US",
     "welcome": "Hello, Welcome to America"
   }
   ```

   ```jsx
   // public/locales/ko/home.json
   {
     "country": "한국",
     "welcome": "안녕하세요. 한국에 오신 걸 환영합니다."
   }
   ```

**번역된 언어들의 사용**

- 먼저 app에 HOC를 감싸야 한다.

  ```jsx
  import { appWithTranslation } from "next-i18next";

  const MyApp = ({ Component, pageProps }) => <Component {...pageProps} />;

  export default appWithTranslation(MyApp);
  ```

- page에 serverSideTranslations 추가
  page-level에서 사용하는 비동기 함수이다. (getStaticProps/getServerSideProps 사용)

  ```jsx
  // src/pages/index.tsx
  import { serverSideTranslations } from "next-i18next/serverSideTranslations";

  export const getStaticProps = async ({ locale }: GetStaticPropsContext) => {
    return {
      props: {
        ...(await serverSideTranslations(locale || "ko", ["common", "home"])),
      },
    };
  };
  ```

  GetStaticPropsContext에서 locale을 참조할 수 있다.
  \*nodeJS 코드를 포함하고 있기 때문에 `next-i18next/serverSideTranslations` 모듈에서 가져와야 한다.

- useTranslation(hook): hook으로 사용

  ```jsx
  import { GetStaticPropsContext, InferGetStaticPropsType } from "next";
  import Head from "next/head";
  import { useTranslation } from "next-i18next";
  import { serverSideTranslations } from "next-i18next/serverSideTranslations";

  export default function Home(
    _props: InferGetStaticPropsType<typeof getStaticProps>
  ) {
    const { t } = useTranslation("home");

    return (
      <>
        <Head>
          <title>Create Next App</title>
          <meta name="description" content="Generated by create next app" />
          <meta name="viewport" content="width=device-width, initial-scale=1" />
          <link rel="icon" href="/favicon.ico" />
        </Head>
        <main>
          **<div>{t("country")}</div>
          <div>{t("welcome")}</div>**
        </main>
      </>
    );
  }
  ```

  locale이 “ko” 일 때

  ![l10n_example]({{ site.url }}{{ site.baseurl }}/assets/images/2023-04/i18n-ko.JPG)

  locale이 “en” 일 때

  ![l10n_example]({{ site.url }}{{ site.baseurl }}/assets/images/2023-04/i18n-en.JPG)

하드 코딩하지 않아도 locale이 달라지는 경우 자동으로 다르게 나타나게 된다!! 🎉

**추가 기능**

- changeLanguage로 언어 변경 빠르게 가능

  ```jsx
  import { GetStaticPropsContext, InferGetStaticPropsType } from "next";
  import Head from "next/head";
  import { useRouter } from "next/router";
  import { useTranslation } from "next-i18next";
  import { serverSideTranslations } from "next-i18next/serverSideTranslations";

  export default function Home(
    _props: InferGetStaticPropsType<typeof getStaticProps>
  ) {
    const router = useRouter();
    const { t } = useTranslation("home");

    const handleLangToggle = (newLocale: string) => {
      const { pathname, asPath, query } = router;
      router.push({ pathname, query }, asPath, { locale: newLocale });
    };

    const changeTo = router.locale === "ko" ? "en" : "ko";

    return (
      <>
        <Head>
          <title>Create Next App</title>
          <meta name="description" content="Generated by create next app" />
          <meta name="viewport" content="width=device-width, initial-scale=1" />
          <link rel="icon" href="/favicon.ico" />
        </Head>
        <main>
          <button onClick={() => handleLangToggle(changeTo)}>
            change lang
          </button>
          <div>{t("country", { changeTo })}</div>
          <div>{t("welcome", { changeTo })}</div>
        </main>
      </>
    );
  }

  export const getStaticProps = async ({ locale }: GetStaticPropsContext) => {
    return {
      props: {
        ...(await serverSideTranslations(locale || "ko", ["common", "home"])),
      },
    };
  };
  ```

  결과

  ![l10n_example]({{ site.url }}{{ site.baseurl }}/assets/images/2023-04/change language.gif)

## 그래서 번역은 누가 업데이트?

시험으로 해본 앱은 번역할 것이 많지 않으니 개발자가 직접 public에 담아서 가져다 쓰면 된다.

그런데 실제 서비스를 하고 있는 웹 앱이라면?

여러 문제가 발생한다.

- 번역할 단어가 너무 많음: NHN의 Dooray는 번역에 사용하는 key값들만 만 개가 넘는다고 한다.
- 코드에서 관리하면 관리가 어려워짐. 보기 힘들고, 용량도 커짐.
- 번역가가 작업을 하는 것이 아닌 개발자가 번역가나 다른 사람에게 파일을 받아서 작업해야 함.
- 코드에 포함되어 있기 때문에 업데이트도 자주 할 수 없음.

⇒ 그래서 개발자는 자동화를 해야 한다.

글로벌 서비스를 만드려면 아무래도 영어 이외에 중국어, 일본어, 등등 지원하면 더욱 더 서비스가 빛을 발할 것이다.

### csv 관리 방법

번역 파일을 csv 파일로 만들어서 따로 관리.

csv 파일은 git 관리도 가능하고 보기 편함.

→ json으로 바로 확인이 불가능.

### nhn에서 투고한 글에서의 방법

- 구글 스프레드 시트

**기본적인 플로우**

1. 구글 스프레드 시트의 엑셀에 단어들을 번역가들이 작업.
2. 엑셀의 값들을 모두 불러옴.
3. object / json으로 컨버팅하여 사용.

**사용하는 패키지**

```jsx
npm install i18next
npm install -D i18next-scanner
npm install -D google-spreadsheet
```

i18next 단어를 전달하여 번역한 (매핑된) 단어를 반환해주는 자바스크립트 국제화 라이브러리 (복수형, 인터폴레이션, 문맥 기능도 있음)

i18next-scanner 18next.t() 나 i18n.t() 같은 함수를 스캔하여 key 추출, 언어별 json 파일 생성 ⇒ script로 실행 필요 (codegen 처럼)

google spread sheet는 구글 스프레드 시트 api를 js에서 사용할 수 있도록 래핑한 라이브러리. 시트 조작 가능.

⇒ upload와 download util을 만들어서 json으로 생성된 번역 단어들을 구글 스프레드 시트에 맞게 열을 추가한다.

### 시도는 해보지 않았지만 해보고 싶은 방법

- firebase remote config 혹은 db

기본적인 플로우

1. 번역가는 firebase에서 데이터 바꾸고
2. firebase에서 데이터 가져와서 사용

- 혹은 key value json으로 저장한다고 하면 따로 버전이나 정기 배포 없이 바뀐 정보를 보여줄 수 있으니 좋지 않을까
- firebase dependency를 추가해두면 나중에 A/B 테스트 도입도 고려할 수 있다!

⇒ firebase remote config가 데이터를 얼마나 저장할 수 있을지, 번역가가 작업하기는 편한지 등 확인 필요

### 참고 아티클

[https://all-dev-kang.tistory.com/entry/리액트-글로벌한-웹을-향하여-react-i18n-다국어지원#들어가며](https://all-dev-kang.tistory.com/entry/%EB%A6%AC%EC%95%A1%ED%8A%B8-%EA%B8%80%EB%A1%9C%EB%B2%8C%ED%95%9C-%EC%9B%B9%EC%9D%84-%ED%96%A5%ED%95%98%EC%97%AC-react-i18n-%EB%8B%A4%EA%B5%AD%EC%96%B4%EC%A7%80%EC%9B%90#%EB%93%A4%EC%96%B4%EA%B0%80%EB%A9%B0)

[https://meetup.nhncloud.com/posts/295](https://meetup.nhncloud.com/posts/295)

[https://github.com/i18next/next-i18next](https://github.com/i18next/next-i18next)

[https://www.i18next.com/overview/supported-frameworks](https://www.i18next.com/overview/supported-frameworks)

[https://miaow-miaow.tistory.com/32](https://miaow-miaow.tistory.com/32)

[https://tolgee.io/blog/localization-basics-S01E01](https://tolgee.io/blog/localization-basics-S01E01)

[https://crowdin.com/blog/2022/07/14/internationalization-vs-localization](https://crowdin.com/blog/2022/07/14/internationalization-vs-localization)

<hr>
