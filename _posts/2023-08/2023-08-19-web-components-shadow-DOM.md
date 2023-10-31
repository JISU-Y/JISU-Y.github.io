---
layout: single
title: "[Browser] Web components - shadow DOM"
categories: Web
tag: [Web, Browser]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Web components - shadow DOM

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 shadow DOM 이라는 개념이 있던데, 얘는 뭐하는 애일까?
</aside>

## Web components

> 기능들을 캡슐화한 요소/태그/컴포넌트
>
> > 웹 컴포넌트를 구성할 때 반복성을 줄이고 **재사용성**을 높이기 위해 사용할 수 있는 컴포넌트

### 알아야 할 세 가지 개념

**Custom elements**

요소와 동작들을 커스텀하여 정의할 수 있는 API set  
(여러 개의 API를 세트로 사용해서 구현하니까 이렇게 표현한 듯)

**Shadow DOM**

메인 도큐먼트의 DOM 과는 별개로 렌더되는 캡슐화된 DOM 트리를 요소에 붙일 수 있게 하는 API set.

DOM 내부에 충돌 없이 style과 script를 적용할 수 있게 된다.

**HTML templates**

렌더되는 페이지에 보여지지 않는 마크업 태그들을 작성할 수 있는 요소/태그.

여기에 작성해둔 요소들을 재사용한다.

### 왜 씀?

- 기본 요소들의 브라우저 통일성이 떨어짐.
  - 요소/태그들의 look and feel이 브라우저 혹은 환경(window, mac) 마다 너무 다 다르다.  
    ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-08/19-browser-look-and-feel.png)
  - 그래서 통일화된 컴포넌트를 보여주기 위해 Javascript component를 사용한다. (script로 커스텀 컴포넌트를 추가/제거)
- Javascript component는 느림.
  - 컴포넌트가 복잡하면 복잡할수록 상당한 크기의 javascript 덩어리.
  - javascript이기 때문에 리소스 로드가 다 완료된 후 동작 및 적용하므로 느림.
- 기본 요소들은 기능이 다양하지 않음.
  - 느리니까 빠르게 하려면 HTML 을 써야 하는데, 아무리 요새 HTML 태그들 기능이 많아졌다고는 하지만 한계.
  - 그래서 **개발자가 HTML 엘리먼트를 만들 수 있게 해주는 것**이다.

### 특징

- 여러 기능들 및 요소들을 하나에 다 갈아 넣어 캡슐화.  
  하나의 캡슐로 쉽게 적용할 수 있게 한다.
- 네이티브 요소로 동작 → 빠르고 성능 좋음.

### 쓰는 방법

**Cutom elements**

window.customElements 를 사용  
(interface: CustomElementRegistry)

customElements.define 메서드

- `element name`: kebab-case로 작성해야 함. 하나의 단어여서는 안됨.
- `class object`: 요소의 동작이 정의된 클래스
- `options`: extends 속성을 포함한 옵션 객체 (`{ extends: "p" }`)

**Cutom elements 종류**

- **Autonomous custom elements: 자발적 / 독립형 커스텀 요소**

  표준 HTML 요소에서 상속받는 것이 아닌 아예 새롭게 생성한 요소  
  ex) `<custom-modal/>`

  ```jsx
  customElements.define("popup-info", PopupInfo);
  ```

- **Customized built-in elements: 커스텀 빌트인 요소**

  표준 HTML 요소를 상속받아 정의한 요소.

  ex) `<p is=”all-red” ></p>`

  ```jsx
  customElements.define("all-red", AllRed, { extends: "p" });
  ```

- 라이프 사이클 콜백 사용 가능
  - connectedCallback: 커스텀 요소가 요소에 append가 될 때마다 동작하게 하는 메서드
  - attributeChangedCallback: 커스텀 요소가 추가/제거/변경될 때마다 동작하게 하는 메서드
  - 등등 더 많다. ([참고](https://developer.mozilla.org/en-US/docs/Web/API/Web_components/Using_custom_elements#using_the_lifecycle_callbacks))

**Shadow DOM**

숨겨둔 DOM 트리를 실제 DOM 트리에 갖다 붙일 수 있도록 해준다.

shadow DOM tree는 메인 DOM 트리와 별개의 shadow root로 시작한다.  
이 아래로 모든 아무 요소들을 실제 DOM 처럼 갖다 붙이면 된다.

밖에서는 Shadow DOM 안에 있는 요소들을 가지고 놀 수 있지만,  
Shadow DOM 안에서는 밖에 있는 것들을 건들 수 없다.

```jsx
const shadowOpen = elementRef.attachShadow({ mode: "open" });
const shadowClosed = elementRef.attachShadow({ mode: "closed" });
```

open을 하면 `myCustomElem.shadowRoot` 를 통해 DOM에서 shadow DOM에 접근 가능해진다.

closed는 DOM에서 shadow DOM에 접근할 수 없도록 한 것.  
저렇게 접근해도 null 나옴 (video element 같은 것들이 closed되어 있는 경우)

### 해보기

<aside style='background-color : transparent; color: black; opacity: 0.8; border: 1px solid black; padding: 10px 20px'>
☝️ custom-modal 태그

모달 템플릿을 커스텀 태그으로 정의

</aside>

```jsx
class CustomModal extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: "open" }); // open mode로 DOM에서 shadow DOM 접근 가능하도록 shadow root 설정

    const wrapper = document.createElement("div");
    wrapper.setAttribute("class", "wrapper");

    const closeButton = document.createElement("button");
    closeButton.innerText = "X";
    closeButton.setAttribute("class", "closeButton");
    closeButton.setAttribute("tabindex", 0);

    wrapper.appendChild(closeButton);

    const content = document.createElement("span");
    content.setAttribute("class", "content");
    content.textContent = this.getAttribute("data-text");

    wrapper.appendChild(content);

    this.shadowRoot.appendChild(wrapper); // wrapper만 shadow DOM에 추가

    const style = document.createElement("style");
    style.textContent = `
      .wrapper {
        width: 150px;
        height: 200px;
        border-radius: 8px;
        background-color: blue;
        position: fixed;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
      }

      .closeButton {
        background-color: red;
        color: white;
        border: none;
        padding: 4px 8px;
        position: absolute;
        top: 8px;
        right: 8px;
      }

      .content {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 150px;
        color: gray;
      }
    `;

    this.shadowRoot.appendChild(style); // style을 shadow DOM에 추가
  }
}
```

- 이렇게 style을 안에서 지정할 수도 있고,
- css link 태그를 이용해서 외부에 있는 stylesheet를 가져와서 적용시킬 수도 있다.

```jsx
customElements.define("custom-modal", CustomModal);
```

이렇게 define 하면 `<custom-modal />` 을 사용할 수 있게 된다.

## Web components in JS lib/framework

> React나 Vue에서 쓰나?
> 왜 쓰는 것이고, 어떻게 활용할 수 있나?

[인프랩 참고]

기존에 있던 레거시 css 들이 전역적으로 적용되어 있는 앱이 있다고 하자.

여기서 새로운 마이크로 서비스 혹은 도메인을 추가한다고 했을 때, **전역적으로 적용된 스타일들이 방해**를 할 때가 있다.

ex) reset.css 들 엄청 다 달라서 갈아 엎기 참 힘든 친구. 이런 애들 처럼 못 갈아 엎겠는데, 그 산하에 스타일을 적용한다고 하면 여간 짜증나는 상황이 생길 수도..

그래서 `react-shadow`라는 라이브러리도 있다.

이를 이용해서 컴포넌트들(큰 컴포넌트 일 수도 있음)을 shadow DOM 산하로 가져와서 새출발할 수 있게 도와준다.

### 참고 링크

https://developer.mozilla.org/en-US/docs/Web/API/Web_components

https://developer.mozilla.org/en-US/docs/Web/API/Web_components/Using_custom_elements

https://developer.mozilla.org/en-US/docs/Web/API/Web_components/Using_shadow_DOM

https://tech.inflab.com/202208-shadow-root/

https://d2.naver.com/helloworld/188655

<hr>
