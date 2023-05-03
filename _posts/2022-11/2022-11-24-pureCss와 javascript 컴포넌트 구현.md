---
layout: single
title: "[Javascript] pure CSS vs Javascript components 구현 차이 및 장단점"
categories: Javascript
tag: [Javascript, CSS]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# CSS-only VS Javascript

컴포넌트 및 기능을 구현함에 있어 javascript를 사용하는 대신 pure css를 사용해서 구현해보고 차이점을 알아본다.

## why css-only?

> 생각보다 html과 css의 기능이 강력하여 javascript 없어도 기본적인 기능들은 구현이 가능한 경우가 많다.  
> 따라서 해석이 더 필요한 js보다 html과 css로만 구현하면 성능적인 이점을 가져갈 수 있다.

좋은 기능의 html 및 css의 예를 들면,

1. heading section 이동

   a 태그와 href 이용하여 구성할 수 있다.

   focus되어 있을 때 stying까지 가능하여 복잡한 js를 추가하지 않아도 더 빠르고 쉽게 오히려 성능이 좋게 만들 수 있다.

   ```html
   <a href="#section1">Jump to the Section1</a>

   <h1 id="section1">Section1</p>
   ```

2. input datalist (autocomplete)

   input의 검색 기록 및 마우스로 검색어 이동 등을 담당하는 컴포넌트는 구현이 까다로우나  
   html input 프로퍼티의 list와 태그인 datalist를 이용해서 구현할 수 있다.

   ```html
   <label for="browser">Choose your browser from the list:</label>
   <input list="browsers" name="browser" id="browser" />

   <datalist id="browsers">
     <option value="Edge"></option>
     <option value="Firefox"></option>
     <option value="Chrome"></option>
     <option value="Opera"></option>
     <option value="Safari"></option>
   </datalist>
   ```

   [https://www.w3schools.com/tags/tryit.asp?filename=tryhtml5_datalist](https://www.w3schools.com/tags/tryit.asp?filename=tryhtml5_datalist)

이렇게 추가적인 javascript 로직없이 구현하게 되면 개발 공수도 덜 들고, 성능도 훨씬 좋다.

그래서 이번에는 **css는 javascript를 대체할 수 있을까**에 초점을 맞추어서 기능 구현을 해보려고 한다.

## Pure CSS로 구현했을 때 더 좋은 점

- 빠른 렌더
- 간결한 코드
- 실패 코드의 프로덕트 영향

**빠른 렌더**

css는 javascript와 다르게 html과 병렬적으로 로드가 되고, 브라우저 입장에서 추가적인 해석이 필요하지도 않다.  
따라서 parsing이 더 빠르고 렌더도 더 빨리된다.

css가 변경되면 리플로우, 리패인팅을 거쳐서 렌더링이 된다.  
(물론 어떤 속성을 변경했느냐에 따라서 리플로우, 리페인팅 둘 다할 수 있고, 리페인팅만 할 수도 있지만)

js로 했을 경우는 html 자체가 새로 생겼다가 없어졌다가 하므로 DOM / CSSOM 생성부터 다시 될 것이다. (element 유/무)  
그렇기 때문에 보통의 경우에 **css로 구현했을 때 더 빠를 수 밖에 없게 된다!**

**간결한 코드**

코드도 간결하여 굳이 상태를 저장하거나 추가적인 javascript logic을 구성하지 않아도 된다.

**실패 코드가 결과물에 미치는 영향**

오류가 나더라도 위험성이 덜하다.

javascript로 구현할 때 잘못 구현하거나 버그가 날 경우 에러들이 발생하게 되고, 심한 경우에는 프로덕트가 터지는 등 문제가 생길 수 있다.  
그러나 css로 구현했을 때 semicolon을 안하거나 잘못 구현했다하더라도 브라우저는 속성을 무시한다.

따라서 이상한 에러를 뿜거나 예측 불가능한 행동을 하지는 않는다.

### Pure CSS with React!

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
📌 개발 환경

template: cra typescript template 사용  
style: vanilla css

</aside>

react로 구현을 하려고 한다.

css와 javascript를 비교한다해놓고 css-in-js를 쓰는 건 어불성설이라고 생각이 되어서, css를 사용해서 구현할 예정이다.

_(사실 css in js도 성능에 대한 사람들의 회의가 꽤 있어왔고, 실제로도 체감할 수 있는 정도의 차이라고 하니 여기서 더 깊게 알아보지는 않겠지만 styled-components를 사용하지 않고 css를 사용해보았다.)_

**구현:**

- **Toggle button**
  <img src="https://user-images.githubusercontent.com/80020227/152763537-795cab62-9cd9-4e8b-9cdd-3ef7da3bf5f7.gif" alt="toggle-button" />

  클릭 시 스위치와 같이 왼쪽 오른쪽으로 이동하며 on-off를 나타내주는 컴포넌트

  **js:**

  ```jsx
  function Toggle() {
    return (
      <div className="toggle-container">
        <input type="checkbox" id="button" />
        <label className="switch-rail" htmlFor="button" />
      </div>
    );
  }

  export default Toggle;
  ```

  **css:**

  ```css
  .toggle-container {
    display: flex;
    margin-top: 50px;
  }

  .switch-rail {
    padding: 0;
    width: 90px;
    height: 45px;
    border-radius: 22.5px;
    position: relative;
    transform: translateX(0);
    background-color: gray;
    cursor: pointer;
  }

  .switch-rail::after {
    content: "";
    width: 48px;
    height: 48px;
    position: absolute;
    top: -2px;
    left: -2px;
    background-color: white;
    border-radius: 50%;
    border: 1px solid purple;
    transition: all 0.5s linear;
    z-index: 1;
    display: block;
  }

  /*  실제 input은 보이지 않게 */
  input[type="checkbox"] {
    display: none;
  }

  /* click 되었을 때(checkbox가 check 되었을 때) Switch */
  input[type="checkbox"]:checked ~ .switch-rail::after {
    /* switch에서 동그란 버튼 위치 오른쪽으로 옮김 */
    left: 50px;
  }
  ```

- **Tooltip**

  <iframe src="https://codesandbox.io/embed/bold-breeze-hbrn2q?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="bold-breeze-hbrn2q"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

  특정 컴포넌트 (여기서는 button) hover 시 설명을 나타내주는 컴포넌트

  **js:**

  ```jsx
  function Tooltip() {
    return (
      <div className="tooltip-container">
        <button className="unknown-button" aria-label="아무버튼도 아니지">
          무슨 버튼일까
        </button>
      </div>
    );
  }

  export default Tooltip;
  ```

  **css:**

  ```css
  .tooltip-container {
    margin-bottom: 100px;
  }

  .unknown-button {
    position: relative;
  }

  .unknown-button::before {
    content: attr(aria-label);
    opacity: 0;
    position: absolute;
    top: 30px;
    right: -90px;
    font-size: 14px;
    width: 100px;
    padding: 10px;
    color: #fff;
    background-color: #555;
    border-radius: 3px;
    pointer-events: none;
    line-height: 1.4em;
  }

  .unknown-button:hover::before {
    opacity: 1;
  }
  ```

- **Tab**
  <iframe src="https://codesandbox.io/embed/hungry-tu-o12yej?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="hungry-tu-o12yej"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

  메뉴를 클릭하면 그에 따른 컨텐츠를 보여주는 Tab 컴포넌트

  **js:**

  ```jsx
  const menu = [
    { menu: "Tab1", contents: "Tab 1 Contents" },
    { menu: "Tab2", contents: "Tab 2 Contents" },
    { menu: "Tab3", contents: "Tab 3 Contents" },
  ];

  function Tabs() {
    return (
      <div className="tab-container" role="tablist">
        <div className="tab-menu-wrapper">
          {menu.map((el, index) => (
            <div className="menu" key={el.menu} role="tab">
              <input
                type="radio"
                name="radioTab"
                id={el.menu}
                onChange={(e) => console.log(e.target)}
              />
              <label htmlFor={el.menu}>{el.menu}</label>

              <div className="tab-contents-wrapper" tabIndex={index + 1}>
                {el.contents}
              </div>
            </div>
          ))}
        </div>
      </div>
    );
  }
  ```

  css:

  ```css
  .tab-container {
    width: 100%;
    max-width: 640px;
    margin-bottom: 100px;
  }

  .tab-menu-wrapper {
    display: flex;
    width: 100%;
    font-weight: bold;
  }

  .menu {
    display: flex;
    flex-direction: column;

    width: calc(100% / 3);
    background-color: #e3e3e3;
    color: #bcbcbc;
    padding: 15px 10px;
    cursor: pointer;
    transition: all 0.3s linear;
  }

  .tab-contents-wrapper {
    position: absolute;
    left: 0;
    top: 5rem;
    padding: 1em 1.2em;
    color: #333;
    z-index: -1;
    opacity: 0;

    z-index: -1;
    opacity: 0; /* hides the tab content by default */
  }

  label {
    padding: 1em;
    border: none;
    border-radius: 6px 6px 0 0;

    background-color: #0094a7;

    color: #fff;
    font-size: 1em;

    cursor: pointer;
  }

  /* hide the radio buttons visually*/
  [type="radio"] {
    position: absolute;
    height: 0;
    width: 0;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
  }

  /* change color of active tab */
  [type="radio"]:checked ~ label {
    background: #007584;
  }

  /* makes the active tab's content visible */
  [type="radio"]:checked ~ .tab-contents-wrapper {
    opacity: 1;
    z-index: 1; /* increase the z-index so the content is in focus*/
  }

  [type="radio"]:focus + label {
    outline: 2px dotted black;
  }

  /* trick to avoid focus line on click*/
  [type="radio"]:focus:not(:focus-visible) + label {
    outline: 2px dotted transparent;
  }
  ```

**이외에도:**

**sidebar menu open/close**

**accordion**

**image loader**

심지어 **modal** 까지도 구현 가능하다.

보통 input radio를 통해서 구현하게 된다.

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
🤔 근데,,

이쯤되면 이렇게 좋은데 왜 안 쓰지라는 의문이 들게 된다.  
진짜 css로만 구현하면 장점밖에 없을까?

</aside>

## Pure CSS로만 구현하면 안 좋은 점

- 오히려 JS가 더 빠를 수도 있음
- 가독성 및 유지보수
- 확장성
- 상태 유지
- 웹 접근성

등 꽤 많은 단점을 보유하고 계시겠다.

**오히려 JS 로직으로 구현하는 것이 더 빠른 경우도 있다**

Initial rendering은 구현 기능과 초기 상태값에 따라서 오히려 js로 구현하는 것이 더 빠른 경우가 있을 수 있다.

예를 들면,

어떤 특정 요소를 show / hide 하는 기능의 컴포넌트를 구현한다고 했을 때,  
show hide를 css로 했을 때와 js로 상태값으로 했을 때 대충 아래와 같이 될 수 있다.

```html
<div>
  <Component className="hide" />
</div>
```

```html
<div>{showComponent && <Component />}</div>
```

여기서도 기본적으로는 css가 더 빠른 것은 맞다.

그런데 만약에 showComponent의 initial state가 false라면..?

그렇다면 렌더링될 때 `Component` 자체가 그려지지 않으므로 오히려 js로 구현했을 때가 더 빨라지게 되는 것이다.

**가독성 및 유지보수**

가독성과 다른 개발자가 보았을 때 이해 가능한 코드, 읽기 좋은 코드일까?

css로 구현을 하는데, 아무리 깔끔하게 classname을 부여하고, 여러 방법의 css 방법론들을 도입을 한다고해서 JS 로직보다 직관적으로 이해하기 쉬울까는 아닐 것 같다.

이렇게 되면 유지보수 또한 어려워지겠다.

**확장성**

토글 기능 정도면 확장성을 굳이 따져야 하나 싶지만, 구현은 확장될 가능성을 항상 열어두어야 할텐데

css로만 구현하게 된다면 그 이상의 기능들은 구현하기 어려워진다.

**상태 유지**

state를 저장하지 않으니 상태가 유지되거나 해야하는 상황에서는 사용하기 힘들다.

**조금은 치명적인 웹 접근성**
웹 접근성은 좋을까?

hover해서 tab menu를 보여주는 동작의 경우 css로 구현하면 더 빠를 수는 있겠으나 tab으로 했을 때는 hover를 css알 수 없기 때문에 키보드만 사용하는 유저에게는 메뉴를 보여주지 못하는 대참사가 발생할 수 있다.

따라서 웹 접근성 관점에서는 javascript를 사용하는 것이 더 좋다.

## Pure CSS로만 구현하면 아예 안되나?

그래서 언제 사용하면 좋을까라는 의문이 든다.

칼처럼 딱 잘라서 이때는 css만 사용하는 것이 옳고, 이 때는 js만 사용하는 것이 옳은 상황은 없다.

[아티클](https://gomakethings.com/when-to-use-css-vs.-javascript/)에 따르면

#### Javascript를 웬만해서는 사용하는 것이 좋을 때:

유저와 interaction이 있는 경우 특히 hovering, focusing, clicking 같은 경우는 javascript를 사용하는 것이 좋다고 한다.

이유는 위와 같이 접근성 문제.

#### CSS를 사용하면 좋을 때:

animation이나 visibility 혹은 외형적으로 변형이 있는 경우는 css를 이용하는 편이 좋겠다.

컴포넌트의 움직임이나 show/hide 같은 경우는 css를 변경하는 것이 렌더 성능에 더 좋으므로 css를 이용하는 편이 좋다.

**그러나 둘을 잘 보고 사용하기 괜찮은 경우는 반대의 경우에도 사용해도 좋다!**

이를 테면 tooltip 같은 경우는 hover할 때와 focus될 때 시각적으로 보여주고, 스크린 리더기에서는 aria label 등 aria tag를 이용해서 오히려 접근성과 성능 둘 다 잡을 수도 있겠다.

### 참고 아티클

[https://gomakethings.com/when-to-use-css-vs.-javascript/](https://gomakethings.com/when-to-use-css-vs.-javascript/)

[https://blog.logrocket.com/css-only-components/](https://blog.logrocket.com/css-only-components/)

[https://www.youtube.com/watch?v=tKj3xsXy9KM](https://www.youtube.com/watch?v=tKj3xsXy9KM)

<hr>
