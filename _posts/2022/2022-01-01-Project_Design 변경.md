---
layout: single
title: "[Project] React 개인 프로젝트 Design 변경 및 CRUD 구현"
categories: Project
tag: [Project, React, 개발 환경 설정, 개인 프로젝트, 토이 프로젝트]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**React 개인 프로젝트 - Design 변경 및 CRUD 구현**

## React 개인 프로젝트

### Design 변경

Design 및 UI/UX 변경하여 Figma 다시 그렸다.

확실히 구성을 잡고 가니 UI를 구현하는 시간이 조금 줄어들었다.

애초에 UI UX 구성이 되어 있어야 개발하는 중간에 고민하지 않아도 되는 것이다.

새삼 개발만 특히 중요한 것이 아닌 것을 깨닫게 된다! (추가로, UI/UX 공부도 하고 싶다.)

#### 기능 구현 시작

pill에 대한 CRUD 부터 구현해보기로 했다.

PillSetting에서 약을 추가하면(Pill Card 생성) PillSetting 및 Home화면에도 보이도록 한다.

하지만, Home 화면에서는 간소화된 info로, PillSetting 화면에서는 모든 info를 나타내도록 구성을 달리 가져갔다.

여기서 컴포넌트 재사용성이 중요하다는 것을 느꼈다.

아마 이전 프로젝트 같았으면 별개의 컴포넌트를 생성했을 것이다.

이제 컴포넌트의 범용성이 중요한 이유를 조금은 알 것 같다.

#### CRUD

추가하는 것은 어렵지 않다. redux의 아키텍쳐도 확실히 알게 되었고, 사용할 수 있게 되었다.

(redux toolkit은 따로 봐야할 것 같다는 것은 함정)

delete 까지는 바로 진행이 되었지만,

테스트하는 데에 너무 오래 걸리고 (새로고침할 때마다 새로 약들을 추가해주어야 하니 불편하다... 이래서 unit test 를 쓰는 것인가.)
이제 데이터 저장도 해야했기 때문에 firebase 작업을 하기 시작했다.

#### firebase & firestore

firebase는 공식문서를 참조하여 가장 최신 버전 (v9 이상)으로 진행했다.

- firebase add 했는데 갑자기 해당 에러가 발생하였다.

  ```
  ./node_modules/history/node_modules/@babel/runtime/helpers/esm/extends.js Module build failed
  ```

  찾아보니 (Babel's runtime is a production runtime that ships with your code so it can't just be left out because it runs on the client.)
  babel/runtime은 client에서 동작하는 거라서 devDependencies말고 regular dependencies에 있어야 한다라는 뜻이라고 한다.

  일단, babel/runtime을 dependency에 add하여 해결하였다. (yarn add @babel/runtime 로 해결)

  참고: https://stackoverflow.com/questions/57737270/how-to-fix-module-not-found-cant-resolve-babel-runtime-helpers-objectwitho

db는 firestore를 사용 예정이다.

realtime database와 firestore 중 firestore를 선택한 이유는 다음과 같다.

1. 일단 realtime database의 단점(json 트리로 인한 복잡한 계층적 데이터를 관리하기 어려움 등 여러가지)를 보완해서 후속으로 나온 것이 firestore이다. 따라서 firestore를 쓰는 것이 좀 더 보완점을 가진 db를 선택하는 것이라고 생각했다.
2. 결정적으로, realtime database는 데이터를 반환할 때 해당 데이터의 모든 depth의 데이터를 다 반환해준다. 따라서, 큰 데이터를 불필요하게 가져오는 점이 안 좋다고 생각했다.
   <br> 이에 반해 firestore는 쿼리 시 정렬, 필터링 조건문을 동시에 사용할 수 있다는 장점과 특정 컬렉션이나 도큐먼트만 반환하기 때문에 성능 문제가 발생하지 않는다.

이와 같은 이유로 firestore를 사용하기로 했다.

기술을 쓸 때 하나하나 찾아보는 습관을 들이는 것이 좋은 것 같다. 나중에 찾아보면 구태여 나랑 관련도 없는 걸 공부하는 느낌이 드는데, 이렇게 내가 필요한 기술을 사용하기 전 장단점을 비교하니 공부도 잘 되고, 더 주의깊게 보게 되는 것 같다.

참고) firestore asia-northeast3 한국, test mode로 추가

- firestore 추가하고 redux saga 추가하였는데, 어떻게 쓰는지 아직 너무 모르겠어서 공부를 따로 각 잡고 한 다음에 추가해야할 것 같다.
  <br>생각보다 매우 어렵다. 그래도 처음 redux를 접했을 때 보다는 어느 정도 궁예질이 가능한 정도이나
  <br>generator 개념도 그렇고, 이것을 가지고 api 요청, CRUD 구현은 어떻게 하는지, 외부에서 어떻게 사용하는지(thunk처럼 함수마다 export 해서 사용하는 건 아닌 것 같고..) 아직 매우 감이 오지 않는다.

### 할 것

- 리스트들 무한 스크롤 구현

- redux-saga 공부하고 적용하기

- firestore db 연동하기, 데이터 저장하기(CRUD 모두)

- 그 다음에 오늘 먹어야 하는 것들만 보여주기 (아마 dayJS를 사용하는 것이 좋을 것 같다.)

- 그 다음에 이번주에 먹을 것들 동적으로 보여주기 (먼저 form에서 며칠마다, 요일마다 입력 받는 것도 제대로 구현해야함)

- 오늘 먹을 것 리스트 밀어서 먹는(완료하기) 기능 추가

- 구글 로그인 구현하기
