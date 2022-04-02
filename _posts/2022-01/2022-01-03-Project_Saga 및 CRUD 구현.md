---
layout: single
title: "[Project] React 개인 프로젝트 Saga 및 CRUD 구현"
categories: Project
tag:
  [
    Project,
    React,
    개인 프로젝트,
    토이 프로젝트,
    Redux-saga,
    firebase,
    firestore,
  ]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-12/01_1.png)

# Project

**React 개인 프로젝트 Saga 및 CRUD 구현**

## React 개인 프로젝트

### Redux-saga

#### Redux-saga 적용

redux saga 너무 어렵다..

참고: https://www.youtube.com/watch?v=6GvMQ0vMXQU

https://velog.io/@namezin/To-Do-App

https://kyounghwan01.github.io/blog/React/redux/redux-saga/#react%E1%84%8B%E1%85%A6%E1%84%89%E1%85%A5-saga-%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%E1%84%92%E1%85%A1%E1%84%80%E1%85%B5

위의 강의와 예제를 통해서 일단 구현은 성공했다.

사실 제너레이터를 공부하겠노라 하고 saga를 선택했지만, 너무 급해서 일단 구현이라도 꾸역꾸역 해버렸다.

조금은 속상하다.

하지만, 덕분에 일단 진도는 어느정도 나갈 수 있게 되었다.

제너레이터와 Saga는 다시 공부해보는 것으로 하자. (다른 글에서 좀 더 정확하게 정리하여 따로 올릴 것)

- saga 추가하고 실행 에러 발생

https://github.com/redux-saga/redux-saga/issues/280

이거 참고하여 해결 -> (core.js 괜히 설치함...)

```
import "regenerator-runtime/runtime";
```

이것을 제너레이터 함수 (pills)의 가장 최상단에 import 하였다.

babel-polyfill을 사용하지 않고 싶을 때 import하는 것.

#### CRUD 구현 완료

firestore db에 저장 및 crud 구현 완료

#### Form data 수정 중

날짜마다 보여줄 약 다르게 하는 것 구현하기 위해서 Form data 수정 필요

현재 한 줄에 하나씩 있어 가독성 떨어짐 -> 컴팩트 사이즈로 변경 필요

### 할 것

- form data 변경

  - n일 마다 n번, 요일 나타내는 것 변경
  - n번 입력되면 시간 선택 n개 나타내기
  - 시간 15분 마다 선택할 수 있도록 변경
  - 너무 길다. 컴팩트 한 사이즈로 리팩토링

- 그 다음에 오늘 먹어야 하는 것들만 보여주기 (아마 dayJS를 사용하는 것이 좋을 것 같다.)
- 시간 순서대로 Today에 나타내기
- 그 다음에 이번주에 먹을 것들 동적으로 보여주기 (먼저 form에서 며칠마다, 요일마다 입력 받는 것도 제대로 구현해야함)
- 오늘 먹을 것 리스트 밀어서 먹는(완료하기) 기능 추가

- 구글 로그인 구현
