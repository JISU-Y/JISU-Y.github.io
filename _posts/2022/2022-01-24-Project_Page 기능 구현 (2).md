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

#### TranslationList language filter

번역 리스트에서 언어를 기준으로 filtering 기능을 적용하기로 했다.

먼저 로직은 다음과 같다.

    1. 언어선택 클릭 시 모달 생성
    2. 모달 안에 checkbox (언어들)이 나온다.
    3. 선택된 언어들을 배열로 가져와서 태그로 만들어준다.
    4. 스타일링을 한다.

먼저 모달은 따로 많이 나와있으니 언급하지 않겠다.

모달 안에 checkbox를 어떻게 할 것인지, 스타일링을 위해서는 어떻게 바꿔야 하는지 생각보다 꽤 생각을 해야하는 작업이었다. (~~아닌가?~~)

```jsx
{
  languages.map((language) => (
    <CheckBoxWrap key={language} checked={capableLanguages.includes(language)}>
      <CheckBoxInput
        name="capableLanguages"
        id={language}
        onChange={handleChangeLanguages}
        label={language}
        checked={capableLanguages.includes(language)}
      />
      <span>{capableLanguages.includes(language) && <CheckedIcon />}</span>
    </CheckBoxWrap>
  ));
}
```

languages (모든 언어 배열)를 돌면서 Checkbox를 생성한다. 그런데, Checkboxinput의 네모 박스는 사용하지 않을 것이므로 icon을 따로 넣는다.

그 다음 input이 선택될 때 (onChange)일 때 checked된 것이면 배열에 value를 추가하고, checked가 풀린 것이면 해당하는 그 value를 지워준다.

```jsx
const handleChangeLanguages = (e) => {
  const { value, checked } = e.target;

  // check가 된거면 배열에 추가하고, 아니면 배열에서 지운다
  setCapableLanguages((prev) =>
    checked ? [...prev, value] : prev.filter((el) => el !== value)
  );
};
```

여기서 set 해준 capableLanguages를 이것이 바뀔 때마다 이제 실제로 진짜 보여줄 필터링된 Estimates를 set 해준다.

이 때, filtering 과정에서 beforeLanguage나 afterLanguage나 상관없이 포함된 요소만 찾아서 태그로 나타내도록 한다.

```jsx
useEffect(() => {
  // 선택된 것 없으면 원래 있던 것 다 보여주고 filter 못하도록 return (선택이 다 풀렸을 때는 다시 그냥 모든 것을 보여주도록)
  if (!capableLanguages.length) {
    setFilteredEstimates(estimates);
    return;
  }

  setFilteredEstimates(
    estimates.filter(
      (el) =>
        capableLanguages.includes(el.beforeLanguage) ||
        capableLanguages.includes(el.afterLanguage)
    )
  );
}, [capableLanguages]);

// return
{
  filterdEstimates.map((estimate) => (
    <EstimateCard
      key={estimate.id}
      // ...
    />
  ));
}
```

지금은 초기화가 되지 않아 빈 배열을 사용하고 있으나 빈 배열이 아닌 가능언어(from mypage)를 가져와서 초기 값으로 설정해주어야 한다.

#### AlwaysScrollToBottom

채팅방에서는 항상 Focus가 입력창 즉, 가장 아래에 있어야 한다.

하지만, 지금은 입력창이 Fixed이기 때문에 Input에 focus를 주어도 채팅의 가장 최신(가장 밑)의 채팅을 보여주지 못한다.

따라서 검색 후 해당 방법을 사용했다.

1. container에 fake div를 생성한다.
2. 그것에 ref를 달아 그곳으로 scroll이 이동하도록 한다.

```jsx
const AlwaysScrollToBottom = () => {
  const elementRef = useRef();
  useEffect(() => elementRef.current.scrollIntoView());
  return <div ref={elementRef} />;
};
```

해당 함수 (div 요소/컴포넌트를 return 해주는 함수)를 컴포넌트로 사용한다.

#### InitialChat Component 추가

채팅방이 만들어졌을 때를 기준으로 알림 채팅을 채팅방에 나타내는 기능을 추가했다.

    1. 클라이언트가 채팅방을 생성했을 때 시간
    2. 클라이언트 기준 번역가의 이름이 있어야 한다.
    3. 번역가 기준 유저의 이름이 있어야 한다.
    4. 클라이언트, 번역가 상관없이 항상 반대편(왼쪽)에 배치해야한다.

처음에 알림이 온 것이니까 번역가가 실제로 채팅을 보내는 것이 맞지 않을까 생각되었다.

하지만, 채팅방을 생성하는 사람은 무조건 클라이언트이다. 클라이언트가 채팅방이 생성되는 시점을 번역가는 알지 못하기 때문에, 누군가 저런 알림을 보내는 것이 어렵다고 생각했다.

그래서 이러한 노티를 정적으로 띄워주는 것이 좋을 것 같다고 생각했다.(형태만 채팅 형태)

따라서, 채팅방 이전 단계 (채팅방 리스트, 번역가에는 TranslationEstimateDetail, 클라이언트는 TranslatorDetail)에서 채팅 생성 시간, 상대방의 이름, isText(추가 필요)와 price(추가 필요)를 navigate로 넘겨주었다.

### 에러

배포 후에 TranslatorSignupForm 페이지에서

    TypeError: (0 , sh.useEffect) is not a function

에러가 발생했다.

로컬에서는 문제없이 잘 되었기에 원인이 무엇인지 고민이 되었다. 일단 에러는 useEffect를 가리키고 있었기 때문에 이 페이지에서 사용 중인 useEffect 때문일 것이라고 추측만 하였다.

그러고 문법적인 오류는 없다고 판단한 뒤 에러를 검색해 보았다.

이 에러는 useEffect를 잘못된 경로에서 import 해올 경우 발생하는 에러라고 똑같은 에러는 아니지만 유사한 에러를 발견하였고, import를 확인하였다.

다음 이것을 발견하였다.

```jsx
import React, { useState } from "react";
import { useEffect } from "react/cjs/react.development";
```

이렇게 자동완성의 무서움을 깨달았다.

당연히 알아서 잘 import 해주겠지라는 생각으로 자동완성 기능을 신나게 사용하고 있었지만, 이러한 실수도 생길 수 있다는 것을 알게 되었다.

생산성을 고려해서 자동완성을 안쓰진 않을 것이지만, 최소한 어떤 것이 자동 완성이 될 것인지, 확인을 해야겠다고 생각했다.

### 기타 할일

번역가 가입 폼

- 가능언어 선택 시 배열에 추가되도록 - 어떻게 추가하게 할 것인지 다시 이야기 필요하다 o
- 프로필 선택 시 이미지 바뀌도록 o
- 버튼 크기 변경 o

번역 의뢰 리스트

- 필터 기능 o
- 필터 모달 추가 o
- 선택 시 배열에 추가하여 태그로 나타내기 o
- 가능 언어 받아와서 디폴트로 체크하기 -> 가능언어 받아오기 (mypage)
- 견적 기간이 끝난 것들 삭제 안됨 ->

견적서 작성 폼

- 페이지 구성
- 버튼 크기 변경

내 번역

- 보낸 견적, 진행중, 완료 필터 기능 o

견적서 디테일

- 페이지 스타일 적용
- 파일 다운로드 구현

채팅방 리스트

- chatlistcard에 날짜 가져올 데이터가 없음 -> chatList에서 createAt이 채팅방 생성 날짜
- lastChatDate는 항상 0으로 되어 있다. => 백엔드 구현 중
- isRead도 true true만 뜸 -> 백엔드 구현 중
- chatlist 지금 확정하기가 되어야지만 내 채팅 리스트에 뜸 -> 확인 완료
- api/chatroom/client bad request 뜸 -> 완료
- 견적서 디테일 페이지에서 클라이언트 이름, 채팅 생성 시간 가져오기 o

채팅방

- 클라이언트에서 상담하기 클릭 시 번역가 쪽에서 봇 챗 날리기 o => 다 되었는데, TranslationDetail에서만 offerPrice와 isText를 가져올 수 있다. 그래서 번역가, 클라이언트 쪽에서도 Chatlist에서도 offerPrice와 isText를 가져와야 하고, 클라이언트 쪽 견적서 디테일에서도 이 두 개가 넘어와야 한다.
- 채팅 메시지를 기반으로 이름을 보여주다보니 잘못되어있는 경우 많고, 처음에는 이름이 없음.
  => o, client 쪽에서 상담하기 눌렀을 때만 보내주기만 하면 끝
- Client 쪽에서는 작업 완료 말고 확정하기 버튼 o
- bottom focus o

마이페이지

- 리뷰 data fetching 확인

공통

- 스크롤 디자인 적용
- 토글 메뉴 state 밖으로 빼서 props로 받기 o
- 의뢰 요청에서 fileInput에 useUploadName, label="파일 올리기(텍스트)" 속성 추가해주어야 한다. o

- 헤더 둘다 fixed로 변경 o
- 헤더 640px 이상으로 가면 height 80px으로 변경 o
- width: 100% 변경 o
- z-index: 5 o
- main 에는 hamburger 메뉴 삭제 o

- FileInput.jsx 하드코딩되어있는부분 수정 o

- prop 추가된부분 주석 추가해주세요.
- 채팅 Client에서 작업완료 버튼 안보이도록 수정

- 번역톡 로고 svg로
