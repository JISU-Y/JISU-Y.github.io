---
layout: single
title: "[Project] React 개인 프로젝트 Page 생성"
categories: Project
tag:
  [
    Project,
    React,
    개발 환경 설정,
    개인 프로젝트,
    토이 프로젝트,
    atomic design,
    React-Router,
  ]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**React 개인 프로젝트 - Component 구조 완성 및 Page 생성**

## React 개인 프로젝트

### Component 생성

atoms, molecules, organisms 마다 컴포넌트 생성

이제 재사용성이 어떤 것 인지 조금은 알 것 같다.

하지만 구현하는 것은 정말 어렵다.

어떻게 사용 될 지 감이 잡히지 않아 조금 어려운 것 같다.

일단 너무 잘게 쪼개는 데에만 정신 팔리지 말고, 일단 구현먼저 해보고 리팩토링 해나가는 식으로 해보자.

#### prop-types 추가

~~처음에 보고 prototype 인줄 안 바보~~

- prop-types란?
  프로젝트 규모가 커지고, 파일이 많아지면 생각지 못한 곳에서 에러가 발생하게 된다. 따라서 propTypes를 활용하여 타입을 확인함으로써 에러를 예방하는 것이다.

Javascript에서는 Typescript처럼 type을 따로 검사하는 것이 없기 때문에 prop type 활용을 권장하고 있다.

부모로 부터 전달받을 props를 미리 type을 지정해두고, 전달 받았을 때 그 Type을 검사하여 에러 여부를 판단한다.

```jsx
  optionalArray: PropTypes.array,
  optionalBool: PropTypes.bool,
  optionalFunc: PropTypes.func,
  optionalNumber: PropTypes.number,
  optionalObject: PropTypes.object,
  optionalString: PropTypes.string,
  optionalSymbol: PropTypes.symbol,

  optionalNode: PropTypes.node, // 숫자(numbers), 문자(strings), 엘리먼트(elements), 또는 이러한 타입들(types)을 포함하고 있는 배열(array) (혹은 배열의 fragment)

  optionalElement: PropTypes.element // React 엘리먼트.
```

기본적인 type 이 외에는 찾아보면서 사용하면 될 듯 하다.

#### styled-components 추가

styled-components 추가 후 스타일 적용하기 시작.

일단, component를 구분할 수 있을 정도로만 해두고, 추후에 animation이나 media query 적용이 필요하다.

#### Component (Pill Card) 생성

일단 "약"과 "영양제"에 대한 Card를 생성했다.

![]('../assets/images/211229_home_component.JPG')

### Page 생성

#### React-Router 추가

React-Router 추가 후 Page 4개(홈, 약, 내 정보, 404 페이지) 생성

Navbar에 Link 추가하여 Routing 확인

### 할 것

timeline (timetable) 추가하기 - 라이브러리 고민

page 화면 고정적으로 맞추기(지금 contents 달라질 때마다 height가 바뀜)
