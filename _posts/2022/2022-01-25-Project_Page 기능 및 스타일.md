---
layout: single
title: "[Project] 번역가 페이지 기능 구현 (2)"
categories: Project
tag: [Project, React, 팀 프로젝트]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**팀 프로젝트 Page**

## React 팀 프로젝트

### 기능 구현

#### Translator 가능 언어 추가 기능 수정

가능 언어 추가 기능이 디자인이 변경되면서 로직이 조금 달라져야 할 것 같아 수정하였다.

원래 추가/삭제 버튼을 하나로만 두어서 배열의 가장 뒤쪽으로 추가하거나, 가장 뒤쪽에 있는 요소만 삭제해주었으면 되었다.

하지만, 디자인이 변경되면서 추가 버튼은 아래에 하나, 삭제 버튼이 각각의 select 옆에 붙게 되었다. (제일 처음 select 빼고)

이렇게 되면서 삭제를 누르면 가장 뒤에 있는 요소를 삭제하는 것이 아니라 그 해당 요소를 삭제하도록 바꾸어야 했다.

1. 해당 요소를 삭제하려면 어떤 것을 삭제할 것인지 id를 받아야 한다.

   index를 id처럼 사용하였다.

2. 중간에 있는 요소가 삭제되면, index 순서 (실제 배열이 돌아가는 index가 아닌 key로 가지고 있는 값)가 흐뜨러지므로 다시 순서를 0, 1, 2, 3 ..으로 맞춰줘야 한다.

   해당 부분일 아래와 같이 변경하였다.

   ```jsx
   // 변경 전
   setSelectInputs(selectInputs.slice(0, selectInputs.length - 1));
   ```

   ```jsx
   // 변경 전
   setSelectInputs(
     selectInputs.filter((el, idx) => idx !== index).map((el, index) => index)
   );
   ```

   받은 index 위치에 있는 요소를 삭제하고, 다시 index를 정리한다.

   ```jsx
   // 변경 전
   const tempLanguages = { ...languages };
   delete tempLanguages[Object.keys(tempLanguages).length - 1];
   setLanguages(tempLanguages);
   ```

   ```jsx
   // 변경 전
   const tempLanguages = { ...languages };
   delete tempLanguages[index];
   const entries = Object.entries(tempLanguages).map(([, lang], index) => [
     index.toString(),
     lang,
   ]);
   setLanguages(Object.fromEntries(entries));
   ```

   먼저 그 index 번째의 요소를 삭제한다. object 상태에서.
   <br>그러고 object를 entries로 배열로 변경한 후 index를 재정렬 해준다.
   <br>이후 다시 그 entry 배열을 object로 바꾸어 주어 language state를 set 해준다.

함수 전체 코드

```jsx
const removeSelectInput = (index) => {
  if (selectInputs.length === 1) return;

  setSelectInputs(
    selectInputs.filter((el, idx) => idx !== index).map((el, index) => index)
  );

  if (Object.keys(languages).length === 1) return;

  const tempLanguages = { ...languages };
  delete tempLanguages[index];
  const entries = Object.entries(tempLanguages).map(([, lang], index) => [
    index.toString(),
    lang,
  ]);
  setLanguages(Object.fromEntries(entries));
};
```

#### TranslationList language filter

#### TranslationList filter 컨테이너 height

filter 기능을 추가했는데, 요소가 fixed로 되어 있기 때문에 실제 내용(Wrap)에 padding을 줌으로써 모든 카드가 잘리지 않고 보이도록 해야한다.

그런데, 언어 선택에서 많은 언어들이 선택이 된다면 태그가 많아지게 되고, 3~4줄이 되어 height가 늘어나게 되었다.

fixed에서 height가 늘어났기 때문에 실제 내용을 담고 있는 Wrap 요소가 가려지게 된다.

그래서 filter 컨테이너의 height가 변함에 따라 Wrap의 padding-top 값을 변화시키도록 구현하고자 했다.

그러나 그냥 ref를 달아서 useEffect를 사용해서 height를 감지해보면 변하지 않는다.

따라서 ref에 해당 함수를 달아서 렌더링이 될 때마다 이 함수가 실행되도록 한다.

```jsx
const callbackRef = (element) => {
  if (element) {
    setHeight(element.getBoundingClientRect().height);
  }
};
```

이 callback func을 filterContainer의 ref로 설정한다.

callback func 안에서는 element의 height를 이용해서 height state를 set 해준다.

```jsx
<FilterContainer clicked={showModal} ref={callbackRef}>
  <button onClick={openModal}>
    <span>언어 선택</span>
    {showModal ? <ArrowUp /> : <ArrowDown />}
  </button>
  {capableLanguages.map((language) => (
    <Tag key={language} text={language} bgColor="#fff" color="#000" />
  ))}
</FilterContainer>
```

이제 set된 height state를 이용해서 styled-component에 props로 넘겨주면 동적으로 변하는 height 값을 이용하여 padding-top을 동적으로 설정할 수 있다.

```jsx
<Wrap paddingTop={height}>{/* 중략 */}</Wrap>;

const Wrap = styled.div`
  height: 100%;
  background-color: var(--light-gray);
  padding: ${(props) => props.paddingTop + 56}px 0 78px 0;
  position: relative;
  min-height: 100vh;
`;
```

### 기타 할일

번역가 가입 폼

- 가능언어 선택 디자인 시안대로 o
- 입력값 비어있는 것 처리 -> 함수로 빼기로 함, 회의 통해 확인 후에 적용예정
- 프로필 이미지 처리 (현재는 보여주기만)
- 로그인 로직 처리 (제출하기 클릭 시 로그인 페이지로 이동, 승인 후 로그인 시 번역 의뢰 리스트로 이동)

번역 의뢰 리스트

- 필터 기능 디자인대로 수정 o
- Modal Dropdown animation 구현 o
- Modal 띄어있을 시 scroll lock o => block component로 대체
- 가능 언어 받아와서 디폴트로 체크하기 -> 가능언어 받아오기 (mypage) o
- 견적 기간이 끝난 것들 삭제 안됨 -> 백에서 작업 예정
- Tag 많아질 때 어떻게 될 것인지

견적서 작성 폼

- 페이지 구성 및 스타일 적용 o
- date picker input 처리
- 입력값 비어있는 것 처리 -> 추후
- 파일 다운로드 구현 -> 추후

내 번역

- 완료

견적서 디테일

- 페이지 스타일 적용 o
- 상담하기, 작업완료 버튼 로직 확인
- 파일 다운로드 구현 -> 추후

채팅방 리스트

- lastChatDate는 항상 0으로 되어 있다. => 백엔드 구현 중
- isRead도 true true만 뜸 -> 백엔드 구현 중

채팅방

- ChatForm 640 일 때 input 크기 늘리기
- 클라이언트에서 상담하기 클릭 시 번역가 쪽에서 봇 챗 날리기 o => 다 되었는데, TranslationDetail에서만 offerPrice와 isText를 가져올 수 있다. 그래서 번역가, 클라이언트 쪽에서도 Chatlist에서도 offerPrice와 isText를 가져와야 하고, 클라이언트 쪽 견적서 디테일에서도 이 두 개가 넘어와야 한다.
- 채팅방에서 확정하기/작업완료 눌렀을 때 동작
- 채팅 메시지를 기반으로 이름을 보여주다보니 잘못되어있는 경우 많고, 처음에는 이름이 없음.
  => client 쪽에서 상담하기 눌렀을 때만 보내주기만 하면 끝
- 번역톡 알림 width 키우기
- ChatForm width 크기 변경

마이페이지

- 리뷰 data fetching 확인
- 설정 페이지 구현

공통

- 스크롤 디자인 적용
- 404 페이지 적용
- 리스트 없을 시 페이지 적용 -> 컴포넌트 생성
- 컴포넌트 props 추가된 부분 주석 추가
- 번역톡 로고 svg로
- 작업에 대한 완료창(모달)
- form validation 기준 정리
