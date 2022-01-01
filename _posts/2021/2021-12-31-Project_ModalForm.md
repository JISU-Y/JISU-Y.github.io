---
layout: single
title: "[Project] React 개인 프로젝트 Modal Form 및 UI 수정"
categories: Project
tag: [Project, React, 개발 환경 설정, 개인 프로젝트, 토이 프로젝트]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**React 개인 프로젝트 - Modal Form 및 UI 수정**

## React 개인 프로젝트

### Modal Form

find와 inclueds의 차이점이 무엇이지?

- find
  <br>조건을 만족하는 첫번째 요소의 값을 반환
  <br>만족하는 요소가 없다면 undefined 반환

```jsx
const array1 = [5, 12, 8, 130, 44];

const found = array1.find((element) => element > 10);

console.log(found); // 12
```

- includes
  <br>전달한 요소가 있는지 없는지를 판단
  <br>해당 요소가 있다면 true, 아니면 false 반환

```jsx
const pets = ["cat", "dog", "bat"];

console.log(pets.includes("cat")); // true

console.log(pets.includes("at")); // false
```

model form 완성하였다.

```jsx
const initialState = {
  type: "약", // 약 / 영양제 선택
  name: "", // 약 이름
  freq: "하루에 n번", // 복용 주기
  freqDetail: "", // 복용 주기 상세 (하루에 n번에서 n)
  many: 0, // 복용량
  time: "", // 복용 시간 (20:00)
  left: 0, // 잔여량
};
```

modal form data state 저장
<br>redux로 Home PillSetting에서 구독완료

수정, 삭제 기능 필요

<br>

- input tag 작성하다가 오류 발생

  ```
  Error: input is a void element tag and must neither have `children` nor use `dangerouslySetInnerHTML`.
  ```

  - styled-component 사용 시 발생하는 문제로, input 등과 같이 자식을 가질 수 없는 태그에 자식을 넣었을 때 발생하는 에러이다.

  - 해결책: input tag 안에 값을 제거한다.

  - input에 text를 담고 싶다면 \<input /> 하고 아래에 \<label for="" />로 작성한다.

#### Home page

- timetable 밑에 잘려서 home에 wrap으로 싸서 스크롤 만듦

```jsx
const ContainerWrap = styled.div`
  width: 100%;
  height: 100%;
  overflow-y: auto;
  scrollbar-width: none;
  ${ContainerWrap}::-webkit-scrollbar {
    display: none;
  }
`;
```

scrollbar 없애는 코드 (firefox, webkit)

- timetable 커스텀 필요 => timetable 커스텀이 아니라 time table 뺄 것

### 할 것

- TimeLine 빼고 새로운 디자인 적용하기

- 약 컴포넌트에서 redux 통해 update, delete 구현하기

- Firebase 연동하기

- 구글 로그인 구현하기
