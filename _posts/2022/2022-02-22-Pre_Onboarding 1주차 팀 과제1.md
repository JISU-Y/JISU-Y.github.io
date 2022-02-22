---
layout: single
title: "[Course / TIL] Wanted Pre Onboarding FE Course 1주차 - study: DOM, 팀 과제"
categories: Project
tag: [Course, TIL, Wanted, PreOnboarding, DOM, 면접 대비]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Course

**Wanted Pre Onboarding FE Course**

## study

DOM과 DOM 조작에 대해 알아보도록 한다.

### DOM

- DOM (Document Object Model)
  > 브라우저의 렌더링 엔진이 HTML 문서를 파싱하여 브라우저가 이해할 수 있는 생성한 자료구조.
  > HTML 문서를 계층적 구조로 표현하고, 이를 제어할 수 있는 API를 제공.

1. 노드

   HTML 요소는 HTML 문서를 구성하는 개별적인 요소

   이 HTML 요소가 렌더링 엔진에 의해 파싱되면 DOM을 구성하는 요소 노드 객체로 변환된다.

   ![https://mdn.mozillademos.org/files/7659/anatomy-of-an-html-element.png](https://mdn.mozillademos.org/files/7659/anatomy-of-an-html-element.png)

   여기서 class=”nice”는 attribute로 attribute node로 변환, text contents는 text node로 변환된다.

   여기서 태그 사이에 text contents가 아니라 HTML 요소가 들어갈 수 있다.

   따라서 이러한 중첩 관계에 의해 계층적인 구조(부모-자식 관계)가 형성된다.

   계층적인 구조이므로 HTML 요소를 모두 객체화한 노드 객체들을 트리 자료구조로 구성할 수 있다.

   그리고, 이러한 트리 자료구조를 **DOM**이라고 한다. 따라서 **DOM 트리**라고도 불린다.

   ![https://poiemaweb.com/img/select-an-individual-element-node.png](https://poiemaweb.com/img/select-an-individual-element-node.png)

2. 노드 타입

   ```
   문서 노드(document node)
   요소 노드(element node)
   어트리뷰트 노드(attribute node)
   텍스트 노드(text node)
   Comment 노드
   DocumentType 노드
   DocumentFragment 노드 등
   총 12개 노드
   ```

   - 문서 노드: **DOM 트리의 루트 노드** (document 객체), window에 바인딩되어 있다. HTML 문서당 1개만 존재. 요소들에 접근하려면 문서 노드를 통해야 한다.
   - 요소 노드: HTML 요소를 가리키는 객체.
   - 어트리뷰트 노드: HTML 요소의 어트리뷰트를 가리키는 객체. HTML 요소 노드와 연결(부모와는 연결 X). 접근 시 HTML 요소 노드를 통해 접근
   - 텍스트 노드: HTML 요소의 텍스트를 가리키는 객체. 요소의 자식 노드이다. 텍스트 노드는 자식 노드를 가질 수 없어 DOM 트리의 최종단이다.

3. 노드의 상속 구조

   ![https://media.vlpt.us/images/niyu/post/2f26e6d0-b795-48a1-a22b-b822aa8fcb7e/image.png](https://media.vlpt.us/images/niyu/post/2f26e6d0-b795-48a1-a22b-b822aa8fcb7e/image.png)

   DOM을 구성하는 노드 객체는 브라우저 환경에서 추가적으로 제공하는 호스트 객체이다.

   | 객체 특성                                                           | 객체        |
   | ------------------------------------------------------------------- | ----------- |
   | 객체                                                                | Object      |
   | 이벤트 발생 객체                                                    | EventTarget |
   | 트리 자료구조 노드 객체                                             | Node        |
   | 브라우저가 렌더링할 수 있는 웹 문서 요소(HTML, SVG)를 표현하는 객체 | Element     |
   | 웹 문서의 요소 중 HTML 요소를 표현하는 객체                         | HTMLElement |

   모든 노드 객체는 Object, EventTarget, Node 인터페이스를 상속받는다. 따라서 모든 노드 객체에서 Object의 메서드, EventTarget의 메서드(addEventListener, removeEventListener ..), Node의 메서드(parentNode, childNodes ..)을 사용할 수 있게 된다. 이 제공되는 api를 통해 DOM을 조작하여 구조, 내용, 스타일을 동적으로 조작할 수 있게 되는 것이다.

- 요소의 취득

HTML의 구조, 내용, 스타일 등을 조작하려고 할 때 먼저 해당하는 요소를 취득해야한다.

- id 이용
  ```jsx
  document.getElementById("id");
  ```
  인수로 전달한 id 어트리뷰트 값을 갖는 딱 하나(첫번째 요소)의 요소 노드를 탐색하여 반환
- 태그 이름 이용
  ```jsx
  document.getElementsByTagName("div"); // 모든 요소 취득 시 '*'
  ```
  인수로 전달한 태그 이름을 갖는 모든 노드를 탐색해서 반환
  유사배열인 HTMLCollection 객체를 반환한다.
- class 이용
  ```jsx
  document.getElementsByClassName("className anothername");
  ```
  인수로 전달한 class 이름과 같은 모든 노드를 탐색하여 반환. HTMLCollection 객체로 반환된다.
- CSS 선택자 이용
  ```jsx
  document.querySelector(".foo");
  document.querySelectorAll(".bar"); // 모든 요소 취득 시 '*'
  ```
  인수로 전달한 css 선택자와 일치하는 요소 노드를 탐색해서 반환.
  querySelector는 하나의 요소가 반환되고, querySelectorAll은 모든 노드를 반환한다. 이 때 NodeList 객체로 반환된다.
  querySelector는 다른 취득 선택 메서드 보다 느리다. 하지만, CSS 선택자를 이용해서 직관적이고 구체화하여 노드를 취득할 수 있는 장점이 있다.
  따라서, 보통 요소에 id가 있는 경우 getElementById, 이 외의 경우 querySelector, querySelectorAll를 사용하는 것을 권장한다.

**HTMLCollection 과 NodeList**

DOM 컬렉션 객체로 두 객체 모두 DOM api가 여러 개의 결과값을 반환하며 유사 배열 객체이면서 이터러블이다.

HTMLCollection은 항상 노드 객체의 상태 변화를 실시간으로 반영하는 live 객체이고, NodeList는 대부분 non-live 객체로 동작하다 경우에 따라 live 객체로 동작한다. (childNodes가 반환하는 NodeList 객체는 live 객체로 동작)

HTMLCollection과 NodeList 모두 예상과 다르게 동작할 경우가 많으므로 배열로 변환하여 사용하는 것을 권장한다.(유사 배열이기 때문에 스프레드 문법으로 간단하게 배열로 변환 가능)

- 노드 탐색

요소 노드 취득 후 취득 요소를 기점으로 DOM 트리의 노드를 옮겨 다니며 다른 노드를 탐색하는 경우가 많다.

```jsx
parentNode, previousSibling, firstChild, childNodes 등등
```

모두 getter만 존재하여 읽기 전용이다.

- 자식 노드 탐색
  | 프로퍼티 | 설명 |
  | ----------------- | ----------------------------------------------------------------------------------------------------- |
  | childNodes | 자식 노드를 모두 탐색하여 NodeList에 담아 반환. 요소 노드 이외에 텍스트 노드도 포함되어 있을 수 있다. |
  | children | 자식 요소 중 요소 노드만 모두 탐색하여 HTMLCollection에 담아 반환한다. 텍스트 노드 미포함 |
  | firstChild | 첫번째 자식 노드 반환. 텍스트 노드이거나 요소 노드이다. |
  | lastChild | 마지막 자식 노드 반환. 텍스트 노드이거나 요소 노드이다. |
  | firstElementChild | 첫번째 자식 노드 반환. 요소 노드만 반환 |
  | lastElementChild | 마지막 자식 노드 반환. 요소 노드만 반환 |
- 부모 노드 탐색
  ```jsx
  node.parentNode;
  ```
- 형제 노드 탐색
  | 프로퍼티 | 설명 |
  | ---------------------- | ---------------------------------------------------------------------------------------------------------------- |
  | previousSibling | 부모가 같은 형제 노드 중 자신의 이전 노드를 탐색하여 반환. 요소 노드 이외에 텍스트 노드도 포함되어 있을 수 있다. |
  | nextSibling | 부모가 같은 형제 노드 중 자신의 다음 노드를 탐색하여 반환. 요소 노드 이외에 텍스트 노드도 포함되어 있을 수 있다. |
  | previousElementSibling | 부모가 같은 형제 노드 중 자신의 이전 노드를 탐색하여 반환. 요소 노드만 반환 |
  | nextElementSibling | 부모가 같은 형제 노드 중 자신의 다음 노드를 탐색하여 반환. 요소 노드만 반환 |
- 요소 노드 텍스트 조작

**nodeValue**

nodeValue는 setter와 getter가 모두 있는 접근자 프로퍼티다. 따라서 참조와 할당 모두 가능하다.

텍스트 노드에 사용해야 값을 참조할 수 있고, 요소 노드나 문서 노드에 nodeValue를 사용하면 null값을 반환한다.

따라서 취득한 요소 노드가 있으면 그 요소 노드의 firstChild를 사용하여 텍스트 노드를 취득하여 텍스트 value를 변경해주어야 한다.

```jsx
const textNode = document.getElementById(".foo").firstChild;

textNode.nodeValue = "HI";
console.log(textNode.nodeValue); // HI
```

**textContent**

마찬가지로 setter getter 모두 존재하는 접근자 프로퍼티다.

요소 노드의 태그가 시작부터 종료사이의 모든 텍스트를 반환한다. 안에 요소가 있더라도 마크업을 무시하고 텍스트만 반환한다.

```jsx
<div id="foo">
  Hello <span>World!</span>
</div>;

console.log(document.getElementById("foo").textContent); // Hello World!
```

따라서 **textContent를 사용하는 것이 더 간단**하다.

**innerText**

innerText는 textContent와 비슷하게 동작하지만, CSS에 따라 반환값이 달라질 수 있다.(visibility: hidden일 경우 텍스트를 반환하지 않는다.)

또한, css를 고려해야 하기 때문에 textContent보다 느리다.

- DOM 조작

DOM에 요소를 추가하거나 삭제, 교체하는 것을 말한다.

이렇게 DOM 조작은 리플로우와 리페인트가 발생되기 때문에 성능에 영향을 준다. 따라서 성능 최적화에 주의하여야 한다.

- innerHTML
  element.innerHTML 프로퍼티는 접근자 프로퍼티로 요소 노드의 HTML 마크업을 취득하거나 변경할 수 있다.
  해당 요소의 시작부터 종료태그 사이의 모든 HTML 마크업을 문자열로 반환한다.
  ```jsx
  element.innerHTML = "<span>danger</span>";
  ```
  문자열이 렌더링 엔진에 의해 파싱되어 요소 노드의 자식으로 DOM에 반영된다.
  DOM 조작이 쉽고 직관적인 장점이 있지만 크로스 사이트 스크립팅 공격에 취약하다.
  또한, 기존의 요소들을 제거해야 한다는 단점도 있다.
- insertAdjacentHTML
  기존 요소를 제거하지 않으면서 위치를 지정해 새로운 요소를 삽입한다.
  beforebegin, afterbegin, beforeend, afterend를 첫 번째 인수로 전달하고, 마크업 문자열을 두 번째 인수로 전달하여 위치를 지정해서 삽입, DOM에 반영한다.
  innerHTML보다 조금 더 빠르고, 위치를 지정할 수 있지만, 마크업 문자열을 파싱하므로 스크립팅 공격은 여전히 취약하다.
- createElement
  createElement 메서드는 요소 노드를 생성하여 반환한다. 매개 변수에는 태그 이름을 전달한다.
  하지만 create만 하면 DOM 요소에 추가되지 않고, 홀로 존재하는 상태가 된다. 따라서 DOM에 추가해주는 처리가 필요하다.
  createTextNode는 text를 받아서 요쇼 노드 하위에 텍스트 노드를 생성한다. 이 또한 해당 요소에 추가해주는 처리가 필요하다.
- appendChild
  생성된 노드를 실제 DOM에 반영되도록 처리해주는 메서드이다.
  ```jsx
  parent_node.appendChild(child_node);
  ```
  부모 노드에 자식 노드를 추가한다.

## 모의 면접 세션

키 포인트

1. 두괄식으로 답변하기
2. 모르는 건 모른다고 하기 (아는 선에서만 답변, 아는 척 금지)
3. 본인의 개발 경험을 예시로 들기

## 팀 프로젝트

### detail 페이지

#### 컴포넌트

- header: 뒤로가기, 페이지 제목, 끄기 버튼(기능X)

- 게시글 컴포넌트 배열로 나타나는 거

- 게시글 컴포넌트
  - 게시글 헤더 : 아이디, 게시 날짜, 신고하기
  - 이미지
  - description
    - 좋아요 버튼(기능O), 카운트, 공유 버튼(기능O), 하트(기능X)
    - 리뷰 별(기능X)
    - 옵션
    - 내용
    - 태그
    - 배송
  - 상품 리스트
  - 댓글 리스트
  - 댓글 form

## 회고 (TIL)

**2022.02.22 Daily 회고**

✏오늘 한 일

- DOM 내용 학습 및 블로그 정리
- 모의 면접 세션 참관
- 팀 미팅 및 프로젝트 논의
- 역할 분담 및 프로젝트 구현 시작

⁉느낀 점

DOM은 쉬우면서도 어려운 것 같다.
모르고 썼던 것이 성능 혹은 보안에 영향을 주는 것들이 있었다. (innerHTML 등)

모의 면접 세션 참가했을 때 질문에 답을 말해보았는데, 아는 것을 말하는 것이 정말 어렵다. 하루에 한 질문 씩은 연습해볼 것.

팀 인원 수가 많아 역할 분담하기 매우 어려웠다.
앞으로도 꽤 어려울 것 같으나 모두 좋으신 분들이라 5주 동안 같이 공부하며 개발에만 집중할 수 있지 않을까 생각한다.

🎃현재 나의 상태

프로젝트 과제를 보고 어떻게 나눌지 생각하는 것이 어렵지만 재미있다. 컴포넌트를 쪼개는 것도 재미있다.

빠르게 구현을 해야하는데 성능과 코드 품질에 많이 문제가 생길 것 같다. 아직까지는 코드 품질과 생산성을 다 잡지 못한다.

<hr>
