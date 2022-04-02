---
layout: single
title: "[Course / TIL] Wanted Pre Onboarding FE Course 1주차 - 모의 면접"
categories: Course
tag: [Course, TIL, Wanted, PreOnboarding, HTML, DOM, Javascript, 면접 대비]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Course

**Wanted Pre Onboarding FE Course**

## 모의 면접 대비

### link 태그와 script 태그의 위치

#### 일반적으로 CSS `<link>` 태그를 `<head></head>` 태그 사이에 위치시키고, JS `<script>` 태그를 `<body>` 태그가 끝나기 직전에 위치시키는 이유

1. **CSS `<link>` 태그를 `<head></head>` 태그 사이에 위치시키는 이유**

   브라우저는 HTML과 CSS를 한줄씩 읽고 실행하는 방식으로 파싱과 렌더링을 진행한다. 이 점진적 렌더링으로 인해 시각적인 부분을 얼마나 빨리 나타낼 수 있느냐를 따져서 사이트의 성능을 측정하는 사이트 최적화의 지표가 된다.

   `<head>` 태그 안에 CSS `<link>` 를 삽입하면, HTML과 CSS가 병렬적으로 렌더링되어 HTML과 CSS를 동시에 렌더링 할 수 있다.따라서 사이트에 렌더링 되는 시간도 빨라진다.

   반면, CSS `<link>` 태그를 `<body>` 뒷부분에 둔다면, HTML가 모두 렌더링된 이후 CSS를 렌더링하기 시작한다.
   따라서, 렌더링이 총 두번 진행되어 시간이 오래 걸리며
   첫번째 렌더링에서는 스타일되지 않은 HTML 요소들이 보이고, 두번째 렌더링에서는 스타일된 최종 HTML 요소가 보이게 된다.

   문제는 두번째 렌더링을 할 때, HTML 요소들이 재렌더링 되기 때문에 낭비가 심하고, 사용자에게 스타일되지 않은 HTML 요소를 보여주게 되 UX에 좋지 않다.

2. **JS `<script>` 태그를 `<body>` 태그가 끝나기 직전에 위치시키는 이유**

   브라우저는 `<script>` 태그를 만나면 HTML 파싱을 잠시 멈추고, `<script>` 를 다운로드하고 실행한다. 이 경우, 사용자에게 화면이 보여지기까지 시간이 늦어지기 때문에, HTML 요소를 모두 파싱하고 자바스크립트 파일을 다운로드하고 실행할 수 있도록 `<script>` 태그를 `<body>` 태그 맨 뒤에 두는 것이 바람직하다.

### intersection Observer API

#### intersection Observer API가 무엇인지?

1. **Intersection Observer API의 사용 이유**

- 페이지 스크롤 시 이미지를 lazy-loading 할 때
- 스크롤 시, 더 많은 컨텐츠가 로드되어 사용자가 페이지를 이동하지 않아도 되게 하는 infinite-scroll(무한 스크롤)을 구현할 때
- 광고의 가시성을 확인하여 광고 수익을 계산해야 할 때
- 사용자가 결과를 볼 것인지에 따라 애니메이션 동작 여부를 결정할 때<br>등등

  기존에는 특정 위치에 도달했을 때 어떤 액션을 취하도록 하기 위해서는 `addEventListener()`와 `scroll` 이벤트를 사용했다.

  그러나 이를 사용할 때는 다음과 같은 단점이 있다.

  1. scroll 이벤트는 단시간에 수백, 수천번 호출될 수 있기 때문에, DOM을 변경하는 등의 무거운 작업을 수행하기는 어렵다.
  2. 특정 지점을 관찰하기 위해 `getBoundingClientRect()` 함수를 사용해야 하는데, 이 함수는 잦은 reflow를 발생시킨다.

  결론적으로, scroll 이벤트나 getBoundingClientRect() 를 사용할 경우, 우리가 기대하는 효과를 내기 위해 너무 많은 리소스가 들어간다.

2. **Intersection Observer API란?**

   > Intersection Observer API는 관찰하고자 하는 타겟 요소와 최상위 문서의 뷰포트의 교차영역에서 변경이 발생할 때마다 비동기적으로 관찰하는 Web API 이다.

3. **Intersection Observer의 구현**

   Intersection Observer API 콜백함수는 다음과 같은 상황에서 실행된다.

   1. 타겟 요소가 뷰포트나 특정 요소(API에서는 root 요소)와 교차하는 경우
   2. observer가 최초로 타겟을 관측하도록 요청받을 때마다

   root 요소와 타겟 요소가 교차하는 정도를 **intersection ratio**라고 하며, 0.0~1.0 사이의 숫자로 표현한다.

4. **Intersection Observer API 사용법**

   ```
   const io = new IntersectionObserver(callback[, options])
   ```

- `callback`: 타겟 요소와 root 요소가 교차되었을 때 실행하는 함수

  - `entries`: IntersectionObserverEntry 객체의 리스트
  - `observer`: 콜백함수가 호출되는 IntersectionObserver

- `options`:
  - `root`: observer의 대상을 감싸는 element를 지정하는 옵션. `null` 값을 사용하면 default 로 브라우저의 뷰포트로 정해짐.
  - `rootMargin`: rootMargin에 따라 교차 영역을 설정. default 값은 `0px 0px 0px 0px`.
  - `threshold`: root 요소와 타겟 요소가 얼마나 겹쳐야 observer를 실행할지 설정. default 값은 0.
    <br>0.0인 경우 타겟 요소가 교차영역에 진입하는 시점에서 observer를 실행, 1.0인 경우, 타겟 요소 전체가 교차영역에 들어왔을 때 observer를 실행.

### 깊은 복사 vs 얉은 복사

#### 깊은 복사와 얕은 복사의 차이에 대해서 설명하세요. 자바스크립트에서 깊은 복사를 하는 방법은 무엇인가요?

자바스크립트에서 참조값인 객체를 복사할 때 나타나는 차이점이다.

쉽게 원본과 똑같은 것을 복사해서 별개로 사용하는 것과 원본을 가져와서 원본을 가리키는 변수명만 바꾸느냐 (결국 원본 건드림)의 차이다.

**얕은 복사 (Shallow copy)**

- 얕은 복사란 객체를 직접 대입해서 참조에 의한 할당이 이루어져 같은 데이터를 가리키게 하는 것이다. (주소가 같음)

- 따라서 A 객체를 B변수에 할당하면 B를 수정했을 경우 A도 변경된다.

**깊은 복사 (Deep copy)**

- 깊은 복사된 객체는 객체가 중첩되어 있는 상황일 때 내부 객체까지 모두 새로 생성된 것을 의미한다.
- 객체 안에 객체가 있을 경우에도 원본과의 참조가 완전히 끊어져 각각의 메모리에 할당이 되어 있다.
- 복사된 A객체와 B객체 중 어느 하나를 수정해도 다른 객체에 영향을 미치지 않는다.

  방법

  - spread 연산자를 사용하여 할당 (주소는 달라지나 1 depth까지만 가능하다)
  - Object.assign() 메서드 사용 (주소는 달라지나 1 depth까지만 가능하다)

  - 재귀함수를 이용한 복사
  - JSON.stringify()
  - 라이브러리 사용 (lodash)

## 회고 (TIL)

**2022.02.21 Daily 회고**

✏오늘 한 일

- HTML, DOM, JS 모의 면접 질문 정리 및 발표 연습
- 페어 모의 면점

⁉느낀 점

들은 것과 예전에 공부했던 것을 토대로 질문에 대한 답을 할 수 있었지만, 정리되지 않은 채로 답변을 하다 보니 전달력이 많이 떨어진다는 것을 느꼈다.

DOM에 대한 공부가 많이 부족하다. Observer api에 대해 추가로 공부해야 한다.

Javascript 이론을 정리만 하지 말고, 내가 말할 수 있는 형태로 한번 더 정리해야 한다.

🎃현재 나의 상태

HTML, DOM, Javascript 질문들은 어느 정도 들어본 수준. (하지만, 아직 일목요연하게 설명하는 능력은 부족해서 좀 더 내 것을 만드는 것이 필요할 것 같다.)

시간이 부족하여 모르는 부분 (Observer api 등)에 대해 처음 들어봤다는 점. DOM에 대한 공부가 더 필요하다.

<hr>
