---
layout: single
title: "[Course / TIL] Wanted Pre Onboarding FE Course 2주차 - 모의 면접 / 팀 과제 3 시작"
categories: Course
tag: [Course, TIL, Wanted, PreOnboarding, React, 면접 대비]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Course

**Wanted Pre Onboarding FE Course**

## 모의 면접 대비

### React 관련 질문 사항

#### React 개념과 장점, 그리고 컴포넌트란 무엇인가요?

개념: UI를 구축하기 위한 SPA Javascript 라이브러리입니다.

장점:
virtual DOM을 사용해서 어플리케이션의 직접 DOM을 조작하는 것에 비해 렌더링 속도를 향상시킬 수 있습니다. 단, 무조건 렌더링 속도가 향상되는 것은 아닙니다.

서버, 클라이언트 사이드 렌더링 지원이 가능합니다. SSR은 Next.JS 프레임워크를 사용할 수 있습니다.

컴포넌트의 가독성이 높고 간단하여 유지보수가 쉽습니다.

다른 프레임워크와 혼용도 가능합니다.

- 컴포넌트란?

  레고 블록과 같이 작은 단위로 만들어서 그것들을 조립하듯이 개발하는 방법입니다. 캡슐화, 확장성, 결합성, 재사용성과 같은 이점이 있습니다.

#### 리액트의 내부 작동 원리를 설명하세요. 재조정 (Reconciliation) 개념에 대해서 설명하세요.

React에서 DOM을 어떻게 렌더링하고 브라우저 이벤트를 어떻게 처리하나요?

실제로 DOM을 제어하지 않고 중간에 가상의 virtual DOM을 두어 virtual DOM이 변경이 될 때, 실제 DOM을 변경하도록 설계합니다. 이렇게 작업을 virtual DOM과 DOM을 비교해 DOM을 갱신하는 작업 Reconciliation 이라고 합니다.

이 때 virtual DOM을 갱신하는 방법은 setState() 메서드를 호출하는 방법과, Redux의 경우처럼 store가 변하면 다시 최상위 컴포넌트의 render() 함수를 호출해서 갱신하는 2가지 방법이 있습니다.

#### 리액트에 있는 라이프사이클과 각 라이프사이클의 역할을 설명하세요. Class Component의 생명주기 메소드에 대해서 설명하세요.

Class 형 컴포넌트의 메서드 기준으로 다음과 같은 라이프 사이클 메서드를 가지고 있습니다.

componentDidMount() 는 최초로 컴포넌트 객체가 생성될 때 한 번 수행

componentDidUpdate() 는 컴포넌트의 속성 값 또는 상태값이 변경되면 호출

componentWillUnmount() 는 이 컴포넌트가 소멸될 때 호출

render() 는 초기에 화면에 그려줄 때, 그리고 업데이트가 될 때 호출

함수형 컴포넌트는 useEffect를 사용해서 DidMount, Update, Unmount를 구현할 수 있습니다. 두번째 인자로 전달하는 dependency array가 비어있으면 Mount될 때만 동작하고, 안에 특정 state를 넣으면 그 state가 변경될 때 정확하게는 mutate 되었을 때 동작하게 됩니다.

함수형에서 Unmount는 useEffect의 첫번째 인자로 전달한 콜백 함수 내에서 return 문 안에 콜백 함수로 전달합니다. 이렇게 되면 컴포넌트가 unmount 되었을 때 실행되고 mutate된 state를 기준으로 다시 useEffect 콜백 함수가 실행되어 cleanup 함수라고 합니다.

함수형에서 render 메서드와 상응하는 것은 컴포넌트 함수 안에 return 문 안에 JSX 문법을 써주면 됩니다.

#### 리액트 라우터같은 Client Side Routing 에 대해서 설명하세요.

웹 페이지의 렌더링이 클라이언트 (브라우저) 측에서 일어나는 것입니다. html 등 리소스를 한꺼번에 요청하기 때문에 초기 렌더링 속도는 늦습니다. 페이지가 바뀔 때마다 그 html 리소스들을 서버에 요청하는 것이 아니라, 그 여러 페이지들을 담고있는 파일을 한꺼번에 다 불러오는 것입니다.

서버와 클라이언트 간의 데이터 트래픽이 감소하고 렌더링이 한번만 있기 때문에 페이지 이동이 빠르다는 장점이 있지만, SEO(검색최적화) 사용은 불가하다는 단점이 있습니다.

보안 관련해서는 쿠키에 사용자 정보를 저장해야 해서 위험 요소가 될 수 있습니다.

#### state를 직접 변경하지 않고 setState를 사용하는 이유에 대해서 설명하세요.

state는 immutable(불변성)을 유지해야하기 때문입니다.

컴포넌트는 현재의 this.state와 setState를 비교해서 업데이트가 필요한 경우에만 render 함수를 호출하는데 state를 직접 수정하게 되면 리액트가 render 함수를 호출하지 않아 상태 변경이 일어나도 렌더링이 일어나지 않을 수 있습니다.

#### Props Drilling 이란 무엇인가요?

React는 데이터가 단방향으로 흐르기 때문에 컴포넌트 사이에 부모 자식의 관계가 형성됩니다.

따라서, 부모에서 사용하고 있는 state를 자식 컴포넌트에서 사용할 때에 props로 넘겨주게 되는데, 이때 자식 컴포넌트의 depth가 깊어진다면 넘겨주는 props state를 사용하지 않아도 오로지 props 전달때문에 계속 props를 pass on 해주어야 합니다. 이것을 props driling이라고 하며 해결 방법은 전역 상태 관리를 할 수도 있고, 간단한 경우 컴포넌트 병합을 통해 해결할 수도 있습니다.

#### 리액트 Hooks의 장점은 무엇인가요? Class Component와 Function Component의 차이점에 대해서 설명하세요.

로직의 재사용이 가능하고 관리가 쉽습니다. 함수 안에서 다른 함수를 호출하는 것으로 새로운 hook을 만들어 볼 수도 있습니다.

기존의 class component 는 여러 단계의 상속으로 인해 전반적으로 복잡성과 오류 가능성을 증가시켰습니다. 또한, state를 사용하는 데에 무조건 this를 붙여야 하는 등 코드 상 깔끔하지 못한 점이 있었습니다.

하지만 function component에 hooks가 도입되며 class component가 가지고 있는 기능을 모두 사용할 수 있음은 물론이고 기존 class component 복잡성, 재사용성의 단점들까지 해결됩니다.

#### virtual DOM이 무엇인가요? virtual DOM이 좋은 이유에 대해서 설명하세요.

Virtual DOM은 실제 DOM 변화를 최소화 시켜주는 역할을 합니다.

virtualDOM을 이용하는 이유는 효율성 때문입니다. virtualDOM을 활용하면 실제 DOM을 직접 바꾸는 것보다 시간 복잡도가 현저히 낮아집니다.

먼저 브라우저는 HTML 파일을 스크린에 보여주기 위해 DOM 노드 트리 생성, 렌더트리 생성, 레이아웃, 페인팅 과정을 거칩니다. DOM 노드는 HTML의 각 엘리먼트와 연관되어 있기 때문에 HTML 파일에 20개의 변화가 생기면 DOM 노드가 변경되고 그 이후의 과정역시 20회 다시 이루어 집니다. 작은 변화에도 매우 복잡한 과정들이 다시 실행되기 때문에 DOM 변화가 잦을 경우 성능이 저하됩니다.

Virtual DOM은 뷰에 변화가 있다면, 그 변화가 실제 DOM에 적용되기 전에 Virtual DOM에 적용시키고 **최종 결과**만 실제 DOM에 전달합니다.

따라서 20개의 변화가 있다면 Virtual DOM은 변화된 부분만 가려내어 실제 DOM에 전달하고 실제 DOM은 그 변화를 1회로 인식하여 단 한번의 렌더링 과정만 거치게 됩니다.

예를 들어, body의 background가 white였다가 red 다시 white로 바뀌었다고 가정할 때 DOM에서는 색이 바뀔 때 모두 렌더링이 일어나지만

VDOM에서는 어차피 최종 이 white이기 때문에 렌더링을 하지 않아도 되어 효율성을 높일 수 있습니다.

#### JSX가 무엇인가요?

JSX의 확장 문법으로 React 엘리먼트를 생성하는 언어입니다. 자바스크립트 코드를 HTML처럼 표현할 수 있기 때문에 용이한 개발이 가능합니다.

JSX 문법을 브라우저가 인식할 수 있도록 트랜스파일러 바벨을 사용합니다.

#### 웹 성능 향상을 위해 최적화를 해 본 경험이 있나요? 혹은 useMemo와 useCallback 메소드를 활용해 최적화하는 원리에 대해서 설명하세요.

useMemo는 렌더링을 할 때마다 메모리가 많이 소모되는 값은 계산하지 않고 functional components를 최적화하는데 도움을 줍니다. dependency 리스트를 생성해 그 중 하나가 변경되면 바로 값을 계산합니다.

// useMemo를 사용한 예제

```jsx
import React, { useMemo } from "react";

const computeValueFromProp = (prop) => {
  // 에너지가 많이 소모되는 계산이 일어남
};

const ComponentThatRendersOften = ({ prop1, prop2 }) => {
  const valueComputedFromProp1 = useMemo(() => {
    return computeValueFromProp(prop1);
  }, [prop1]);

  // prop1이 변경될 때만 값을 계산
  return (
    <>
      <div>{valueComputedFromProp1}</div>
      <div>{prop2}</div>
    </>
  );
};
```

useCallback도 useMemo와 비슷합니다. useMemo는 값을 기억하는 대신 useCallback은 함수를 기억합니다.

예를 들어 1부터 변수까지 모두 더하거나 곱하는 연산을 가진 함수가 컴포넌트 내부에 있다고 가정할 때, 1부터 1억까지 계산하는 것은 매우 시간이 오래걸리며 메모리가 낭비될 수 있습니다. 따라서 이 변수가 1억이 바뀌기 전까지는 렌더링 될 때마다 계산하는 것이 아닌 useMemo 혹은 useCallback을 이용해서 dependency array에 변수 즉 state를 넣고 그 값이 변경될 때에만 연산하도록 하면 메모리 사용을 줄일 수 있습니다.

하지만, 무조건 사용하는 것이 좋지 않습니다. useMemo와 useCallback 또한 메모리를 사용해서 저장하는 것이기 때문에 렌더될 때마다 계산되어도 무리가 없는지, 어떤 것이 더 적합할 지 trade off를 고려하여 적용해야 합니다.

#### React 에서 상태 변화가 생겼을 때, 변화를 어떻게 알아채는지에 대해서 설명하세요.

React는 상태를 immutable 하게 변경하기 때문에 상태 객체의 주소값이 변경되면 상태가 변화되었다는 것을 알아챌 수 있습니다.

따라서 setState를 사용하지 않고 변수에 직접 할당을 한다면 주소값이 변경되는 것이 아니기 때문에 렌더링이 일어나지 않아 state를 변경할 때에는 setState 함수를 사용해야 합니다.

#### 여러가지 상태 관리 라이브러리(Apollo, Redux, MobX 등)의 차이점에 대해서 설명하세요.

Redux
<br>Flux개념을 바탕으로한 React에서 현재 가장 많이 사용되는 State 관리 라이브러리 입니다.

장점은 커뮤니티가 많고, thunk와 saga 등 미들웨어를 이용해서 비동기 처리도 가능하며
<br>store가 단일이고, store의 state를 변경하는 것은 dispatch를 통해서만 가능하기 때문에 오류를 비교적 쉽게 찾아낼 수 있다는 장점이 있습니다.
<br>하지만, 폴더와 파일의 개수가 늘어나고 작성해야 하는 코드가 장황한 단점이 있습니다.

따라서 facebook에서는 Recoil이라는 라이브러리를 또 만들었습니다.

Recoil은 문법이 비교적 쉽고, 코드가 간단한 장점이 있습니다.

MobX
<br>Redux와 또 다른 State관리 라이브러리입니다. 기본적으로 객체지향 느낌이 강하며 Component와 State를 연결하는 코드들(보일러 플레이트 코드)을 데코레이터(애노테이션)제공으로 깔끔하게 해결합니다.
하지만 데코레이션을 이제 권장하지 않는 것으로 알고 있습니다.

context api
<br>기본적으로 React에서 제공되는 전역 상태 관리 hook입니다.

라이브러리를 설치하지 않아도 되는 장점이 있습니다.

하지만, 렌더링 문제가 있습니다.

예를 들어, FirstName 컴포넌트에서는 NameContext.firstName 값만을 사용하고 FamilyName 컴포넌트에서는 NameContext.familyNmae 값만을 사용하지만, FirstName 컴포넌트에서 값을 수정시 FamilyName 컴포넌트에서 리렌더링이 발생합니다.

간단한 규모의 앱이라면 큰 문제가 되지 않겠지만, 퍼포먼스와 관련된 부분이라면 속도 저하 문제가 눈에 보일 수도 있습니다.

출처: https://jungpaeng.tistory.com/58 [개발자스러운 블로그]

Apollo
<br>Redux는 REST API를 사용하기 때문에 리소스의 크기가 서버에서 결정됩니다. 데이터 교환이 복잡하게 이루어지고, 엔드포인트 증가 및 overfetching과 underfetching 등의 문제점을 가지고 있습니다. 반면에 Apollo Client는 GraphQL을 기반으로 합니다. 서버에서는 어떠한 자원을 사용할 수 있는지 정의하고, 클라이언트에서 렌더링에 필요한 데이터를 요청하는 방식이기 때문에 꼭 필요한 데이터 교환이 이루어지기 때문에 매우 깔끔하며 효과적입니다.

#### useEffect 메소드로 componentWillUnmount 가 동작할 수 있는 방법에 대해서 설명하세요.

```jsx
useEffect(() => {
  console.log("컴포넌트가 화면에 나타남");
  return () => {
    console.log("컴포넌트가 화면에서 사라짐");
  };
}, []);
```

다음과 같이 useEffect 코드 내부에서 return 하는 익명 함수를 작성하는 방법으로 componentWillUnmount를 구현할 수 있습니다.

## 팀 과제 3

### StageLayout 제작

### 달력 기간 선택

#### 라이브러리 고르기

- react-datepicker
  Weekly Downloads: 1,186,987
  github stars: 6.4k
  Unpacked Size: 532 kB
  Last publish: 2 days ago

  custom header가 있어 커스텀 용이

  다운로드 수 많음 참고자료 많음

  업데이트 지속하고 있음

  datepicker로 고름

- react-date-range
  Weekly Downloads: 130,021
  github stars: 2.1k
  Unpacked Size: 293 kB
  Last publish: 6 months ago

  일단 기본 동작이 거의 유사함

  용량이 적음

- react-day-picker
  Weekly Downloads: 491,125
  github stars: 4.4k
  Unpacked Size: 631 kB
  Last publish: 21 days ago

#### 구현

## 회고 (TIL)

**2022.02.28 Daily 회고**

✏오늘 한 일

- React 모의 면접 질문 정리 및 발표 연습
- 페어 모의 면접
- 팀 과제 회의 및 수행
  - Layout 공용 컴포넌트 제작
  - 달력 기간 선택 피쳐 담당

⁉느낀 점

React 전반적인 내용은 알고 있지만 이것보다 질문이 깊어졌을 때를 대비해서 따로 준비해야겠다.

피드백으로 내용을 많이 말하다보니 요지에 벗어나게 되는 경우가 있다고 하셨다. 일단은 질문에 대한 대답만 정확히 한 후에 추가로 질문이 들어오면 그 때 답변해야겠다. 그리고 두괄식 말하기 연습.

달력 기간 선택을 맡았는데, 한번도 해보지 않은 기능이라 조금 걱정되지만 해본 기능보다 안 써본 것을 해보고 싶기 때문에 오히려 잘 된 것 같다.

🎃현재 나의 상태

이번주도 화이팅해야겠다.

이것도 오늘 안에 끝내고 싶은 마음이 있어서 마음이 급한 상태.

<hr>
