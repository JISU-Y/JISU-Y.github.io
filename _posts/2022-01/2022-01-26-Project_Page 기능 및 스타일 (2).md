---
layout: single
title: "[Project] 번역가 페이지 기능 구현 (4)"
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

#### TranslatorMyPage 구현

TranslatorSignupForm 페이지와 거의 동일하다.

그래서 템플릿화 시키면 좋을 것 같다고 생각되나, 일단 api를 먼저 확인해야해서

페이지를 새로 만들었다.

리팩토링 시 동일한 부분을 템플릿화하여 각 페이지에 사용하면 좋을 것 같다.

#### setting 페이지 구현

```jsx
useEffect(() => {
  const fetchMyInformation = async () => {
    const {
      data: { data: infoData },
    } = await apis.getTranslatorMypage();
    const { name, language, introduce, cashPossible, isBusiness, taxPossible } =
      infoData;

    // 원래 갖고 있던 데이터를 form에 넣어줌
    setFormData((prev) => ({
      ...prev,
      name,
      language,
      introduce,
      cashPossible,
      isBusiness,
      taxPossible,
    }));

    // 가능언어 원래 가지고 있던 데이터로 설정
    const languages = language.split(", ");
    const initialSelectInputs = new Array(languages.length)
      .fill(0)
      .map((el, index) => index.toString());
    setSelectInputs(initialSelectInputs);
    const initialLanguages = Object.fromEntries(
      languages.map((el, index) => [index.toString(), el])
    );
    setLanguages(initialLanguages);
  };
  fetchMyInformation();
}, []);
```

일단 번역가의 정보를 담고 있는 getTranslatorMypage api로 데이터를 가져와서 formData에 넣어준다.

그러고 사용하는 각각의 input의 value에 formData.language, formData.introduce 등등 이렇게 설정한다.

그러면 기본 값으로 원래 제출했었던 설정이 나타난다.

근데 여기서 추가로 language가 문제가 된다.

mySQL에는 배열형태가 저장이 되지 않고, 문자열 형태로 저장이 되기 때문에 데이터가 (한국어, 영어, 프랑스어) 이렇게 쉼표를 구분자로 오게 된다.

하지만 select 로 기본값을 나타내야 하므로 배열로 변환해서 저장해야한다.

```jsx
const languages = language.split(", ");
const initialSelectInputs = new Array(languages.length)
  .fill(0)
  .map((el, index) => index.toString());
setSelectInputs(initialSelectInputs);
const initialLanguages = Object.fromEntries(
  languages.map((el, index) => [index.toString(), el])
);
setLanguages(initialLanguages);
```

language를 먼저 , 를 기준으로 split 하여 배열로 만든다.

['한국어', '영어'] 로 변환이 되었으면 그 배열의 개수대로 index와 요소(string)가 같은 배열을 만들어 setSelectInputs 해준다.

selectInputs이 select 컴포넌트를 map 돌릴 때 사용하는 state인데, 내용이 ['0','1' ...] 이런 식으로 되어 있다.

그 다음 select에 실제로 들어가는 값을 정해줄 것인데, 그 state는 languages(Object 형)이다.

따라서 split 해서 만든 배열을 [['0','한국어'], ['1','영어']]로 만들고, object로 변환하여 languages state에 set한다.

이렇게 해주면 select 에도 원래 제출했던 데이터를 기본값으로 띄워줄 수 있다.

#### 기타 페이지 추가

- 404 페이지 추가 및 적용

switch와 동일하게 Routes 안에서도 path='\*'를 이용해서 가장 아래쪽에 두어 찾고자 하는 path가 없을 때 404 페이지를 띄우도록 한다.

```jsx
<Routes>
  {/* common */}
  <Route path="/" element={<Login />} />
  {/* client */}
  <Route path="/client/main" element={<ClientHome />} />
  {/* translator */}
  <Route path="/translator/signup" element={<TranslatorSignupForm />} />
  {/* 404 page */}
  <Route path="*" element={<NotFound />} />
</Routes>
```

- list 없을 경우 띄워주는 컴포넌트 추가

#### 번역톡 알림 기능 관련 고찰

사실 이 기능은 기능이라고 하기 보다 구현하려고 했을 때 애초에 그냥 정보를 나타내는 한 컴포넌트에 불과하도록 만들려고 했다.

하지만, 실제로 챗봇 기능을 만드려면 어떻게 가능할까.. 잘 모르겠다.

그래서 일단 이 서비스에서 크리티컬한 기능은 아니기 때문에 그냥 무슨 채팅방인지만 알 수 있도록 정적으로 나타내는 컴포넌트를 만들었다.

그런데 문제는 이렇게 구현하다보니, 채팅방에 접근할 수 있는 경로가 다양한데 (번역가-견적서 디테일/채팅방 리스트, 클라이언트-번역가정보/채팅방 리스트) 총 4개의 경로에서 모두 번역톡 알림 컴포넌트에 필요한 정보들을 넘겨주어야 한다.

개발 중 이것이 매우 구현하기 번거롭고 헷갈렸다.

검색을 해봐서 저런 챗 알림 기능 어떻게 구현할 수 있을지 찾아봐야 겠다.

### 기타 할일

번역가 가입 폼

- 입력값 비어있는 것 처리 -> 함수로 빼기로 함, 회의 통해 확인 후에 적용예정
- 프로필 이미지 처리 (현재는 보여주기만)
- 로그인 로직 처리 (제출하기 클릭 시 로그인 페이지로 이동, 승인 후 로그인 시 번역 의뢰 리스트로 이동)
- 파일 input 또 안됨 (프로필 파일 올릴 때와 text 파일 올릴 때 어떻게 할 지 봐야함)
- 지금 career 빈문자열로 넣어주어야 한다. 나중에 career는 지워야함

번역 의뢰 리스트

- 견적 기간이 끝난 것들 삭제 안됨 -> 백에서 작업 예정
- Tag 많아질 때 어떻게 될 것인지

견적서 작성 폼

- date picker input 처리 o
- 입력값 비어있는 것 처리 -> 추후
- 파일 다운로드 구현 -> 추후

내 번역

- 카드 희망날짜 색/배치 변경 (Card 컴포넌트)

견적서 디테일

- 파일 다운로드 구현 -> 추후

채팅방 리스트

- lastChatDate는 항상 0으로 되어 있다. => 백엔드 구현 중
- isRead도 true true만 뜸 -> 백엔드 구현 중

채팅방

- 클라이언트에서 상담하기 클릭 시 번역가 쪽에서 봇 챗 날리기 o => 다 되었는데, TranslationDetail에서만 offerPrice와 isText를 가져올 수 있다. 그래서 번역가, 클라이언트 쪽에서도 Chatlist에서도 offerPrice와 isText를 가져와야 하고, 클라이언트 쪽 견적서 디테일에서도 이 두 개가 넘어와야 한다.
- 채팅 메시지를 기반으로 이름을 보여주다보니 잘못되어있는 경우 많고, 처음에는 이름이 없음.
  => client 쪽에서 상담하기 눌렀을 때만 보내주기만 하면 끝
- 채팅방에서 확정하기/작업완료 눌렀을 때 동작 o (클라이언트 쪽에서 접근했을 때 데이터 전달 필요)
- 번역톡 알림 width 키우기 o
- ChatForm width 크기 변경 o

마이페이지

- 마이페이지 구현 o
- 설정 페이지 구현 (UI만 o)
- mypage modify api not found 뜸 o update로 이름 바뀜
- submit 한 이후 다시 마이페이지로 돌아가도록 o
- 리뷰 data fetching 확인 => review들 가져올 때 왜 translatorId가 맞지 않지? 내꺼는 2인데 8로 요청해야 옴
- 그리고 작업 완료 많이 눌렀는데 왜 번역이 0건?

공통

- 404 페이지 적용 o
- 번역톡 로고 svg로 o
- 리스트 없을 시 페이지 적용 -> 컴포넌트 생성 o
- 스크롤 디자인 적용
- 컴포넌트 props 추가된 부분 주석 추가
- form validation 기준 정리
- 헤더 리팩토링
- 햄버거 메뉴
