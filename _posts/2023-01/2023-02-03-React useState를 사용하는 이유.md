---
layout: single
title: "[React] React - useState를 사용하는 이유: state와 setState"
categories: React
tag: [React, hooks]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# useState를 사용하는 이유: state와 setState

💡 면접에 자주 나오는 질문이기도 하고, 항상 제대로 알지 못하면서 사용하는 React hook들을 알아보려고 한다.  
이번 포스팅은 useState에 대해 작성했다.

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
⚠️ warning!

useState를 사용하는 이유 자체가 딱딱 떨어지게 몇 가지로 설명되는 것은 아니라고 생각합니다.  
일단 조금이라도 더 근본적이고 사실에 가까울 수 있도록 공식문서를 참조했고, 이해한 결과를 설명했습니다.  
따라서 각자 판단하길 바라며 혹시나 잘못된 정보가 있는 경우 댓글을 통해서 알려주시면 감사하겠습니다! 🙇

</aside>

## React에서 컴포넌트마다 데이터를 다룰 때

React에서 컴포넌트마다 데이터/상태/변수를 사용할 때 `useState` hook을 이용한다.

왜 `const variable = false` 와 같이 변수로 선언하여 데이터를 할당하지 않고,  
state를 이용해서 변수를 사용해야 할까?

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
👉 문서를 읽고 파악한 이유는 다음과 같다.  
  
- React에서는 <strong>지역 변수로 화면을 리렌더링 할 수 없기</strong> 때문.
- React에서 <strong>불변성</strong>을 지켜야 함.
- 컴포넌트마다 <strong>private한 state</strong>를 가질 수 있도록 해줌.
</aside>

<hr />

## React에서는 지역 변수로 리렌더링 할 수 없다.

예를 들어 화면에 동의할 것인지 체크하는 컴포넌트를 사용자에게 보여준다고 해보자.

동의를 해야 다음 단계로 넘어갈 수 있도록 버튼이 활성화 된다고 해보자.

그런데 동의라고 쓰여있는 input을 누르면 버튼이 활성화되어야 한다.

즉, **state가 변경됨에 따라 화면(컴포넌트)이 render 되어야 한다**는 뜻이다!

```jsx
// Agreement.jsx
export default function Agreement() {
  let isChecked = false;

  const handleCheckboxChange = (e) => {
    isChecked = e.target.checked;
  };

  return (
    <div className="agreementContainer">
      <div className="agreementContent">약관 내용</div>
      <label className="checkboxCard">
        <input type="checkbox" id="checkTest" onChange={handleCheckboxChange} />
        <label htmlFor="checkTest">동의</label>
      </label>
      <button className={`button ${isChecked && "nextButton"}`}>
        {isChecked ? "다음 단계" : "먼저 동의해주세요."}
      </button>
    </div>
  );
}
```

![agreement_wrong]({{ site.url }}{{ site.baseurl }}/assets/images/2023-02/agreement_wrong.gif)

“동의” 체크 박스를 클릭했을 경우, “먼저 동의해주세요” 버튼이 “다음 단계” 버튼으로 색과 함께 변경되어야 한다.

그냥 변수 `isChecked`를 사용했을 때, Mount 되었을 때는 반영된다.
(컴포넌트가 생성될 때 지역 변수도 선언되므로)

하지만 input change 이벤트를 통해 이벤트 핸들러에서 변수 `isChecked`를 재할당했지만,

**화면에는 반영되지 않는다. (업데이트 되지 않는다.)**

### 🤷 **왜 그럴까???**

React [공식 문서](https://beta.reactjs.org/learn/state-a-components-memory#when-a-regular-variable-isnt-enough)에는 다음과 같이 설명하고 있다.

1. 지역 변수는 **렌더될 때마다 새로** 만들어진다.

   React가 렌더를 한다고 하면 처음부터 싹 다 렌더시킨다. 그렇기 때문에 지역 변수를 변경해도 반영되지 않는다.

2. **변수만 변한다고 렌더링이 실행되는 것은 아니다.**

   React는 변수가 변한다고 컴포넌트를 렌더하지 않는다.

### 🙄 그럼 어떻게 해?

1. 렌더할 때마다 **데이터가 유지**되도록 해야한다.
2. 데이터가 **새로 적용되면 React가 컴포넌트를 렌더**하도록 해야한다. (**리렌더링** 시킨다.)

이를 **React에서는 `useState` hook을 통해 제공**하고 있다.

useState의 첫 번째 인자(state) → 렌더될 때마다 데이터를 유지시켜 화면에 반영

useState의 두 번째 인자(setState) → 새로 바뀐 값으로 리렌더링을 시킬 수 있는 것이다.

```jsx
// Agreement.jsx
import { useState } from "react";

export default function Agreement() {
  let [isChecked, setIsChecked] = useState(false);

  const handleCheckboxChange = (e) => {
    setIsChecked(e.target.checked);
  };

  return (
    <div className="agreementContainer">
      <div className="agreementContent">약관 내용</div>
      <label className="checkboxCard">
        <input type="checkbox" id="checkTest" onChange={handleCheckboxChange} />
        <label htmlFor="checkTest">동의</label>
      </label>
      <button className={`button ${isChecked && "nextButton"}`}>
        {isChecked ? "다음 단계" : "먼저 동의해주세요."}
      </button>
    </div>
  );
}
```

![agreement_right]({{ site.url }}{{ site.baseurl }}/assets/images/2023-02/agreement_right.gif)

“동의” 체크 박스를 클릭했을 경우, “먼저 동의해주세요” 버튼이 “다음 단계” 버튼으로 색과 함께 변경된다.

---

## React에서 **불변성을 지켜주어야 한다.**

### 불변성(immutability)이란?

[MDN](https://developer.mozilla.org/en-US/docs/Glossary/Immutable)에서는 다음과 같이 설명하고 있다.

> An immutable value is one whose content cannot be changed without creating an entirely new value.  
> 즉, immutablility는 완전 **새로운 변수를 생성하지 않는 이상 그 내용/값을 바꿀 수 없다는 것.**

**원시 타입(primitive type)**

Javascript에서 **원시 타입은 immutable**하다.

원시 타입으로 변수를 생성하면 값을 변경할 수 없다.

⇒ let, var로 생성하면 값 변경할 수 있는 것은 메모리 영역을 새로 할당받기 때문에 그런 것.

```jsx
let str = "primitiveString"; // javascript에서 string은 primitive type 중 하나.
str = "primitive-string"; // 새로운 메모리 영역을 할당받음.
```

**객체 타입(object type)**

**객체 타입은 mutable**하다.

변수를 생성하면 그 메모리 영역 그대로 담겨 있는 내용/값을 바꿀 수 있다. (mutate 한다.)

⇒ 원본에 영향이 가게 된다.

object, array 모두 const 키워드로 선언하고도 잘 바뀐다.

```jsx
const stringArr = ["a", "b", "c"];
stringArr.push("d"); // 메모리 영역 그대로 원본을 변경한다.
```

### React에서 state

state는 객체이다.

> `props` and `state` are both plain JavaScript objects.  
> 출처: [React 공식 문서](https://reactjs.org/docs/faq-state.html#what-is-the-difference-between-state-and-props)

따라서 state를 직접 mutate 할 수 있다.

그런데 state에서 직접 mutate한다면 값을 변경하더라도 **메모리 영역은 그대로 있고 값만 바뀌는 것**이다.

이렇게 되면 **React는 state가 변경되었는지 알 수가 없다.**

```jsx
// Agreement.jsx
import { useState } from "react";

export default function Agreement() {
  let [isChecked, setIsChecked] = useState(false);

  const handleCheckboxChange = (e) => {
    isChecked = e.target.checked;
  };

  return (
    <div className="agreementContainer">
      <div className="agreementContent">약관 내용</div>
      <label className="checkboxCard">
        <input type="checkbox" id="checkTest" onChange={handleCheckboxChange} />
        <label htmlFor="checkTest">동의</label>
      </label>
      <button className={`button ${isChecked && "nextButton"}`}>
        {isChecked ? "다음 단계" : "먼저 동의해주세요."}
      </button>
    </div>
  );
}
```

![agreement_wrong_mutate]({{ site.url }}{{ site.baseurl }}/assets/images/2023-02/agreement_wrong_mutate.gif)

“동의” 체크 박스를 클릭했을 경우, “먼저 동의해주세요” 버튼이 “다음 단계” 버튼으로 색과 함께 변경되어야 하지만 변경되지 않는다.

🥰 React에서 **안전한 상태 관리**를 위해 **불변성**을 지켜주어야 한다.  
⇒ React 몰래 state를 mutate하지 말아야 한다.

setState는 React가 **immutability를 지키면서 state를 업데이트**할 수 있도록 해준다.

---

## 컴포넌트마다 **private한 state**를 가질 수 있도록 해준다.

> state는 해당 컴포넌트만을 위한 프라이빗한 변수이다.  
> 출처: [React 공식 문서](https://beta.reactjs.org/learn/state-a-components-memory#state-is-isolated-and-private)

### 컴포넌트 재활용

> 특정 화면에서 **컴포넌트를 여러 번 렌더**(컴포넌트 재활용)시켜도 각각 따로 state를 가지고 있어야 한다.

**useState로 관리한 state**

컴포넌트마다 각기 다른 state를 갖게 된다.

한 컴포넌트의 state가 변경되어도 다른 컴포넌트의 state에 영향이 가지 않는다.

```javascript
// App.jsx
import Agreement from "./Agreement";
import "./styles.css";

export default function App() {
  return (
    <div className="App">
      <Agreement />
      <Agreement />
    </div>
  );
}

// Agreement.jsx
import { nanoid } from "nanoid";
import { useState } from "react";

export default function Agreement() {
  const uniqueId = nanoid();
  const [isChecked, setIsChecked] = useState(false);

  const handleCheckboxChange = (e) => {
    setIsChecked(e.target.checked);
  };

  return (
    <div className="agreementContainer">
      <div className="agreementContent">약관 내용</div>
      <label className="checkboxCard">
        <input
          type="checkbox"
          id={`checkTest ${uniqueId}`}
          onChange={handleCheckboxChange}
        />
        <label htmlFor={`checkTest ${uniqueId}`}>동의</label>
      </label>
      <button className={`button ${isChecked && "nextButton"}`}>
        {isChecked ? "다음 단계" : "먼저 동의해주세요."}
      </button>
    </div>
  );
}
```

![agreement_duplication_right]({{ site.url }}{{ site.baseurl }}/assets/images/2023-02/agreement_duplication_right.gif)

왼쪽의 “동의” 체크박스와 오른쪽의 “동의” 체크박스는 각각 독립적으로 동작한다.

- `nanoid`로 `uniqueId`를 생성하여 사용한 것은 `input의 id` 와 `label의 htmlFor` 때문이다.
  `input id / label htmlFor`를 string으로 하드코딩 해두면
  컴포넌트가 여러 개 생성되었을 때 모두 같은 `input id / label htmlFor`를 갖게 된다.
  그러면 `동의` (label 요소)를 클릭했을 때 가장 첫 번째 input만 변경되어 첫 번째 컴포넌트만 state가 변경되는 현상이 생긴다.

**그냥 변수 사용**

모듈 위에 변수를 선언했다.

재활용되는 컴포넌트에서 그냥 변수를 사용한다면 변수를 공유한다는 것을 알 수 있다.

```javascript
// Agreement.jsx
import { nanoid } from "nanoid";
import { useState } from "react";

let count = 0;

export default function Agreement() {
  const uniqueId = nanoid();
  const [isChecked, setIsChecked] = useState(false);

  const handleCheckboxChange = (e) => {
    setIsChecked(e.target.checked);
    count++;
  };

  return (
    <div className="agreementContainer">
      <div className="agreementContent">약관 내용</div>
      <label className="checkboxCard">
        <input
          type="checkbox"
          id={`checkTest ${uniqueId}`}
          onChange={handleCheckboxChange}
        />
        <label htmlFor={`checkTest ${uniqueId}`}>동의</label>
      </label>
      <button className={`button ${isChecked && "nextButton"}`}>
        {isChecked ? "다음 단계" : "먼저 동의해주세요."}
      </button>
    </div>
  );
}
```

![agreement_duplication_wrong]({{ site.url }}{{ site.baseurl }}/assets/images/2023-02/agreement_duplication_wrong.gif)

“동의”를 클릭할 때마다 약관 내용 옆 숫자가 1씩 증가하도록 해두었다. 다른 컴포넌트 임에도 변수가 공유되고 있다.

- `동의`에 대한 `boolean`값을 useState로 관리한 이유는 리렌더시켜서 `count`를 보기 위함이었다.
- 사실 지역 변수를 컴포넌트 안에 선언하면 각각 따로 `count`가 측정된다.
  하지만 생각해보면 함수 안에 선언해둔 것인데 스코프가 다르니 어찌보면 당연한건가? 싶다. 🤔

재활용되는 여러 컴포넌트에서 state를 싱크 맞추고 싶다면 부모 컴포넌트에서 state를 생성해서 뿌려주는 것을 맞는 방법으로 소개하고 있다.

---

## useState를 사용하면서 의문이었던 부분들

### setState로 업데이트하고 바로 state 확인하고 싶을 때 왜 그전 값을 그대로 보여줄까?

```jsx
// Agreement.jsx
import { nanoid } from "nanoid";
import { useState } from "react";

export default function Agreement() {
  const uniqueId = nanoid();
  const [isChecked, setIsChecked] = useState(false);
  const [count, setCount] = useState(0);

  const handleCheckboxChange = (e) => {
    setIsChecked(e.target.checked);

    setCount(count + 1);
    console.log(count); // 1이 더해진 count가 왜 안나올까?
  };

  return (
    <div className="agreementContainer">
      <div className="agreementContent">약관 내용 {count}</div>
      <label className="checkboxCard">
        <input
          type="checkbox"
          id={`checkTest ${uniqueId}`}
          onChange={handleCheckboxChange}
        />
        <label htmlFor={`checkTest ${uniqueId}`}>동의</label>
      </label>
      <button className={`button ${isChecked && "nextButton"}`}>
        {isChecked ? "다음 단계" : "먼저 동의해주세요."}
      </button>
    </div>
  );
}
```

`setState`는 **다음에 렌더될 화면**을 위해 state **변수를 업데이트** 해준다.

그러니까 setState를 부르고 바로 다음 줄에 console을 찍어봤자

아직 다음 화면이 렌더가 되지 않았으므로 100개를 찍어도 이전 state가 찍히는 것이다.

### 똑같은 값으로 state를 업데이트했을 때는 리렌더가 될까?

Nope.

```jsx
// Agreement.jsx
import { nanoid } from "nanoid";
import { useState } from "react";

export default function Agreement() {
  const uniqueId = nanoid();
  const [isChecked, setIsChecked] = useState(false);
  const [count, setCount] = useState(0);

  const handleCheckboxChange = (e) => {
    // setIsChecked(e.target.checked); // count에 대한 re-render 확인하기 위해 주석처리
    setCount(0);
  };

  return (
    <div className="agreementContainer">
      <div className="agreementContent">약관 내용 {count}</div>
      <label className="checkboxCard">
        <input
          type="checkbox"
          id={`checkTest ${uniqueId}`}
          onChange={handleCheckboxChange}
        />
        <label htmlFor={`checkTest ${uniqueId}`}>동의</label>
      </label>
      <button className={`button ${isChecked && "nextButton"}`}>
        {isChecked ? "다음 단계" : "먼저 동의해주세요."}
      </button>
    </div>
  );
}
```

![count_no_render_highlight]({{ site.url }}{{ site.baseurl }}/assets/images/2023-02/count_no_render_highlight.gif)

브라우저 React debug tool로 렌더링을 확인해보면 리렌더가 일어나지 않는다.

사실 React에서 최적화로 전달된 새로운 값이 **이전 state와 동일하다면 리렌더를 하지 않는다.**

### setState에서 값을 전달하는 것과 함수를 전달하는 것의 차이는!?

**그냥 값을 전달**

```jsx
const handleButtonClick = () => {
  setCount(count + 1);
};
```

state를 그 **새로운 값으로 변경**

**함수를 전달**

```jsx
const handleButtonClick = () => {
  setCount((count) => {
    return count + 1;
  });
};
```

**updater function**으로 인식하고, **현재 state에 접근**할 수 있도록 해준다.

- 유일한 인자로 현재 state를 사용하면 되고,
  pure하게 유지한 채 값을 수정하고 변경된 값을 반환해주어야 한다.

React는 이 updater function들을 **queue에다가 넣어 놓는다.**

이후 다음 **렌더를 거치면서 queue에 있던 updater들을 차례대로 계산**해서 state에 적용해준다.

### setState는 비동기적으로 동작하나?

**React는 state 업데이트를 일괄 적용(batch)한다.**

setState를 부른 이벤트 핸들러들이 모두 다 실행되고, setState를 요청하기를 기다렸다가 다 되면 **한 번에 업데이트** 된다.

⇒ **비동기적으로 동작**한다는 이야기가 여기서 나온 것이다.

```jsx
const handleButtonClick = () => {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
};
```

setState했다고 해서 미리 state 업데이트 해서 리렌더되고,
그 다음 setState 할 때 또 리렌더되면 안되기 때문이다.

⇒ **마구잡이 리렌더를 막음**

**그래서 위에 코드처럼 setCount를 동기적으로 의도한대로 count를 증가시키고 싶을 때는 어떻게 할까?**

이것이 위에서 설명한 setState에 함수를 전달하여 state를 업데이트 하는 것이 좋은 이유이다.

```jsx
const handleButtonClick = () => {
  setCount((count) => count + 1);
  setCount((count) => count + 1);
  setCount((count) => count + 1);
};
```

setCount를 3번 부르면 전달된 updater function들이 queue에 저장되고, 다음 렌더될 때 차례대로 계산되어 (count + 1) 3번이 모두 적용되어 3이 증가한다.

### 우리가 State 사용할 때 주의할 점

React [공식 문서에 성능 최적화 부분](https://reactjs.org/docs/optimizing-performance.html#the-power-of-not-mutating-data)에 나와 있다.  
→ 사실 성능을 최적화 한다기 보다 불변성을 지켜 안전성을 유지한다는 이야기 같다.

```jsx
const [alphabetArr, setAlphabetArr] = useState(["a", "b", "c"]);

const addAlphabetWrong = () => {
  alphabetArr.push("d");
};

const addAlphabetRight = () => {
  setAlphabet((prevAlphabetArr) => [...prevAlphabetArr, "d"]);
};
```

immutability를 지켜야 하므로 setState를 사용해서 state를 업데이트할 때  
직접 object/array를 mutate하지 말고, immutable하게 spread syntax를 사용해서 업데이트하면 더욱 안전하다.

### 참고 아티클

[https://beta.reactjs.org/learn/updating-objects-in-state](https://beta.reactjs.org/learn/updating-objects-in-state)

[https://beta.reactjs.org/learn/state-a-components-memory](https://beta.reactjs.org/learn/state-a-components-memory)

[https://reactjs.org/docs/faq-state.html](https://reactjs.org/docs/faq-state.html)

[https://beta.reactjs.org/reference/react/useState](https://beta.reactjs.org/reference/react/useState)

[https://reactjs.org/docs/optimizing-performance.html#the-power-of-not-mutating-data](https://reactjs.org/docs/optimizing-performance.html#the-power-of-not-mutating-data)

[https://developer.mozilla.org/en-US/docs/Glossary/Immutable](https://developer.mozilla.org/en-US/docs/Glossary/Immutable)

[https://medium.com/analytics-vidhya/why-we-should-never-update-react-state-directly-c1b794fac59b](https://medium.com/analytics-vidhya/why-we-should-never-update-react-state-directly-c1b794fac59b)

<hr>
