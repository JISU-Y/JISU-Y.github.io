---
layout: single
title: "[React] Code splitting과 Lazy-loading"
categories: React
tag: [React, Next.js, code-splitting, lazy-loading, performance]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Code Splitting

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 회사 업무 중 이전 코드 중에 dynamic import (next js)를 사용한 부분이 꽤 많길래.
정말로 성능에 도움이 될까? 싶어서 알아보게 됨.

</aside>

## Code Splitting(코드 분할)

> **코드분할**은 번들한 여러 코드 혹은 컴포넌트를 분리하는 것.
> 필요에 따라 특정한 컴포넌트만 로딩하거나, 병렬로 로딩할 수 있다.

원래 웹 사이트/애플리케이션 개발은  
따로 개발했던 여러 개의 파일들이 번들러에 의해 <u>하나의 js 파일</u>로 모아져서 브라우저가 읽고 동작하게 됨.

**런타임에 여러 번들을 동적으로 만들고** 불러오는 것.

근데 하나로 다 모으니 너무 커짐.

- 애플리케이션 복잡화
- 유지 관리로 인해 늘어나는 코드
- 서드 파티 라이브러리 개수

따라서 코드 분할을 이용하여 여러 코드 혹은 컴포넌트를 분리하게 되었다.

- 특정 컴포넌트만 로딩 등등

⇒ 스크립트 작게 여러 파일로 분할

로딩에 필요한 파일 다운로드는 빠르게

추가 스크립트는 특정 상호 작용 시 지연 로딩 (lazy loading) (서버에 요청이 lazy하게 로딩되는 것)

- 코드량은 같음
- 파일 수 늘어남. (청크 파일)
- 초기 로딩 시 필요한 코드 적어짐.

### 구현

#### Javascript (ECMAScript)

import 함수

```jsx
import("./someFile").then((file) => console.log(file.add(12, 34)));
```

#### Webpack (React)

Webpack 같은 번들러에서 기능을 제공해줌.

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-09/23-wepack-code-splitting.jpg)

보통 Webpack에서 코드 스플리팅이 적용된 경우, 여러 개의 청크(Chunk) 파일이 생성됨. 이것들이 서버에 배포되면서 서버에 저장됨.

**처음 앱이 실행되면 메인 번들만 로드**하게 되고, 특정 라우트를 이동하거나 설정한 내용에 따라 추가적으로 청크를 서버로 부터 불러옴.

청크는 캐싱까지 지원한다.

- **import 함수 사용하기 (Dynamic splitting)**

  separates code where [dynamic import()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) statements are used  
  import() 함수를 사용하면 webpack이 자동으로 코드를 분리해서 청크로 만들어줌.

  <table>
  <tr>
  <th> 변경 전 </th>
  <th> 변경 후 </th>
  </tr>
  <tr>
  <td>

  ```jsx
  import myModule from "./myModule";

  myModule.doSomething();
  ```

  </td>
  <td>

  ```jsx
  import("./myModule").then((myModule) => {
    myModule.doSomething();
  });
  ```

  </td>
  </tr>
  </table>

- **entry point 지정하기 (Entry point splitting)**

  separates code by entry point(s) in the app

  ```jsx
  // webpack.config.js
  module.exports = {
    entry: {
      main: "./src/index.js",
      about: "./src/about.js",
      contact: "./src/contact.js",
    },
    // ...other configurations...
  };
  ```

  페이지마다 독립된 청크로 분리되어 빌드.

#### Next.JS

Next.JS에서는 기본적으로 페이지 기반의 자동 코드 스플리팅을 제공한다고 함.  
(페이지 별로 별도 청크가 분리)

추가적으로 dynamic 메서드를 통해서 청크를 나눌 수도 있음.

import()는 다른 점은 ssr 여부를 설정할 수 있음.  
(서버 사이드에서도 해당 컴포넌트를 렌더링할 것인지 여부를 정의)

```jsx
import dynamic from "next/dynamic";
import { useState } from "react";

const DynamicComponent = dynamic(
  () => import("../components/DynamicComponent"),
  { ssr: true }
);

function MyPage() {
  const [showComponent, setShowComponent] = useState(false);

  return (
    <div>
      <button onClick={() => setShowComponent(true)}>Show Component</button>
      {showComponent && <DynamicComponent />}
    </div>
  );
}

export default MyPage;
```

showComponent가 true일 때 그 때 DynamicComponent 청크를 불러오게 됨.

#### Nuxt.JS

Nuxt.JS 마찬가지로 자동 페이지 별 청크가 나누어지고, import()를 사용해서 코드 스플리팅 할 수 있음.

```
pages/
--| index.vue
--| about.vue
--| contact.vue
```

```jsx
// 컴포넌트 레벨
export default {
  components: {
    MyComponent: () => import("~/components/MyComponent.vue"),
  },
};
```

## Lazy-loading

> critical rendering path를 줄이기 위한 방법.
> 논블로킹 리소스를 필요할 때에만 로드할 수 있도록 하는 전략. (리소스의 로딩을 미룸)

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
☝ <strong>critical rendering path(CRP)</strong>

The Critical Rendering Path is the sequence of steps the browser goes through to convert the HTML, CSS, and JavaScript into pixels on the screen.

Critical Rendering Path(CRP)는 브라우저가 HTML, CSS, JavaScript 등의 웹 자원을 받아서 화면에 픽셀로 변환해 나가는 과정.  
DOM, CSSOM 생성 (HTML, CSS 파싱) → Render Tree 생성 → Layout → Painting … 등의 과정.

</aside>

보통 클릭, 스크롤링, 네비게이션 같은 유저 인터랙션 시 발생하도록 한다.

code-splitting과 조금 다른 점이라고 하면 code-splitting은 chunk 자체를 불러오는 것이고,

lazy-loading은 이미 있는 함수들, 컴포넌트들이 있으면 그게 브라우저에 그려지는 걸 특정 상황에만 나타나도록 미루는 것.

### React.lazy

**`React.lazy`** 는 React의 내장 기능으로, 동적 **`import()`** 를 사용하여 컴포넌트를 비동기적으로 로드.

```jsx
import React, { lazy, Suspense } from "react";

const LazyComponent = lazy(() => import("./LazyComponent"));

function App() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <LazyComponent />
      </Suspense>
    </div>
  );
}
```

- lazy(): Suspense 하위에서 불러와야 함. 컴포넌트가 로딩되지 않았을 때 fallback UI가 필요함.
- <u>사실 위의 예시는 lazy-loading도 맞고, import를 쓰면 실제로 청크로 분리되기 때문에 code-splitting도 맞음.</u>

```jsx
import React, { useState } from "react";
import SomeComponent from "./SomeComponent";

function App() {
  const [shouldShowComponent, setShouldShowComponent] = useState(false);

  const handleComponentLazyLoad = () => {
    setIsComponentVisible(true);
  };

  return (
    <div>
      <button onClick={handleComponentLazyLoad}>Load Component</button>

      {shouldShowComponent && <SomeComponent />}
    </div>
  );
}

export default App;
```

이런 식으로 import는 그대로 하되, 특정 상황에만 불러오도록 하면 lazy하게 로딩만 한 것.

그러나 클릭하기 전에는 애초에 chunk도 필요없으므로 코드 스플리팅을 쓰면 좋다. (물론 크지 않은 컴포넌트이면 그냥 보여주는 게 더 빠를 것 같음)

<hr>
