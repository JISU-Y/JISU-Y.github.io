---
layout: single
title: "[Project] React 개인 프로젝트 HomePage 및 PillSetting Page 구성"
categories: Project
tag:
  [
    Project,
    React,
    개발 환경 설정,
    개인 프로젝트,
    토이 프로젝트,
    Grid,
    Time Table,
  ]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**React 개인 프로젝트 - Home Page 및 PillSetting Page 구성**

## React 개인 프로젝트

### Home Page 구성

Home page에는 위에 줄에 card가 2개, 밑에 줄에 TimeLine 컴포넌트가 1개 들어가야 하므로 Grid로 구성하였다.

Time table event 라이브러리 사용

- 라이브러리를 사용하지 않고 구현하고 싶었지만, 그렇게 되면 구현 시간이 매우 늘어날 것으로 예상되었다.
  <br>며칠 뒤면 팀 프로젝트도 시작해야 하기 때문에 라이브러리를 사용하게 되었다.
  <br>일단, 색이 가장 무난하고 깔끔한 라이브러리를 선택했고, 시간 Range가 조정이 가능한 라이브러리를 선택했다.
  <br>문제는 폰트 사이즈를 바꾸기 어렵다는 것과 Table의 title?이 요일로만 되는 듯하다.(Date 객체로 요일 나타내도 상관은 없을 것 같다.)

지금 grid를 사용했는데, 밑에 Timeline 부분이 자리가 부족하여 거의 반쯤 가려져있는 상태이다.

해당 사항 픽스 필요하다. (일단 grid 1fr로 변경 필요)

### PillSetting Page 구성

약/영양제 추가하는 페이지를 구성했다.

약과 영양제는 컴포넌트를 재활용하여 사용했다.

그런데 문제는 Home Page에서 사용한 약/영양제 컴포넌트는 사실 간단하게 이름만 보여주면 되지만,
PillSetting Page 에서는 좀 더 자세하게 보여주어야 한다.

따라서, PillCard(organisms)를 수정하였다.

PillCard 안에 PillContent라는 molecule 단위의 컴포넌트를 추가로 생성하여 text와 Tag를 같이 사용할 수 있도록 했다.

Tag는 복용 빈도, 잔여량 등을 나타내는 데 사용될 것이다.

-> 문제는 Tag의 위치를 정하지 못하였다. 디자인을 제대로 못하고 시작하다보니 개발하면서 디자인을 하고, 배치하게 되므로 화면이 이상해지는 것 같다. 이 부분은 바로 픽스가 필요할 듯 하다.

- PillCard 안에 PillContent를 추가했으나 atom molecule을 어떻게 할지 고민이다.

  -> 구현 능력이 좋지 않은 상태이므로 일단 하나하나씩 구현을 먼저 한 다음에, 리팩토링 해 나가는 식으로 컴포넌트를 잘게 쪼개야 할 듯 하다.

#### Modal 추가

PillSetting Page에 Modal을 추가했다.

Modal Component는 일단 PillSetting에 두었다.

Modal Component 또한 molecule, atom 단위로 쪼개는 리팩토링이 필요하다.
<br>(구현 후에 리팩토링 하는 것으로 하자. 기술의 부채가 늘어난다.ㅠㅠㅠ)

Modal은 Form을 담고 있고,

약/영양제 선택, 약 이름, 빈도수 등을 사용자가 설정하여 제출하는 것으로 구현하였다.

Modal은 열고 닫는 부분까지만 구현하였다.

벌써부터 Redux가 필요한 상황이 생겼다. Modal을 여는 곳은 PillSetting 이고, 닫아야 하는 곳은 Modal이기 때문에

아직은 depth가 깊지 않지만, Modal은 재사용될 가능성이 있으므로 Redux로 상태 관리하는 것이 좋겠다.

(먼저 UI를 어느 정도 구현하고 넘어갈 것이다.)

### 할 것

- state 생성하여 Modal Form 기능 확인하기

- TimeLine 및 PillCard UI 수정하기

- Redux 추가하여 전역 상태로 관리하기

- Firebase 연동하기

- 구글 로그인 구현하기

- PillCard 열고 닫기 구현
