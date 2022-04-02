---
layout: single
title: "[Project] 번역가 페이지 기능 디테일"
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

### 리팩토링

#### Header 배치 리팩토링

Header가 페이지마다 변형이 꽤 많이 되어서, display flex를 씀에도 불구하고 내부 요소를 absolte로 처리를 해주었다.

이를 리팩토링하고자 했다.

먼저, 기존 코드이다.

```jsx
<Container shouldWrap={location !== "/"}>
  {location === "/" || <HamburgerMenu />}
  {title ? <Title>{title}</Title> : <Logo />}
  {useReloadButton && (
    <SvgWrap>
      <ReloadIcon onClick={reloadEvent} />
    </SvgWrap>
  )}
  {useSettingButton && (
    <SvgWrap>
      <SettingIcon onClick={settingEvent} />
    </SvgWrap>
  )}
</Container>
```

SvgWrap(div)로 svg를 따로따로 감싸고, useReloadButton, useSettingButton display flex를 그대로 사용하려면 이 두 개도 true일 때만 보여지게 하면 안되었다.

```jsx
<Container shouldWrap={location !== "/"}>
  {location === "/" || <HamburgerMenu />}
  {title ? <Title>{title}</Title> : <Logo />}
  <SvgWrap>
    {useReloadButton ? (
      <ReloadIcon onClick={reloadEvent} />
    ) : useSettingButton ? (
      <SettingIcon onClick={settingEvent} />
    ) : null}
  </SvgWrap>
</Container>
```

SvgWrap 안에 icon이 props에 따라 정해진 하나만 들어가도록 했다.

```jsx
const Title = styled.h2`
  font-size: var(--fs-20);
  font-weight: bold;
  // 밑에 모두 지움
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
`;
```

이후 Title을 absolute로 설정해두었던 것을 지웠다.

### 스타일 디테일 적용

#### scroll style 적용

```jsx
body::-webkit-scrollbar{
    width: 6px;
}

body::-webkit-scrollbar-thumb{
    height: 15%;
    background-color: rgba(61, 80, 255, 0.3);
    border-radius: 10px;
}

body::-webkit-scrollbar-track{
    background-color: var(--white);
}
```

scrollbar의 width를 정하고, thumb으로 scrollbar의 높이와 색, 형태를 지정한다.

track 색도 바꾸어준다.

### 기타 할일

번역가 가입 폼

- 입력값 비어있는 것 처리 -> 함수로 빼기로 함, 회의 통해 확인 후에 적용예정
- 프로필 이미지 처리 (현재는 보여주기만)
- 로그인 로직 처리 (제출하기 클릭 시 로그인 페이지로 이동, 승인 후 로그인 시 번역 의뢰 리스트로 이동)
- 파일 input 또 안됨 (프로필 파일 올릴 때와 text 파일 올릴 때 어떻게 할 지 봐야함) -> handle change의 위치
- 지금 career 빈문자열로 넣어주어야 한다. 나중에 career는 지워야함

번역 의뢰 리스트

- 견적 기간이 끝난 것들 삭제 안됨 -> 백에서 작업 예정
- Tag 많아질 때 어떻게 될 것인지

견적서 작성 폼

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
- 채팅방 리스트 불러오기 클라이언트 쪽 이상함 (JISU 번역가만 모두 가져옴)
- 호버 시 마우스 모양 그대로, 호버 시 스타일 처리 o

마이페이지

- 리뷰 data fetching 확인 => review들 가져올 때 왜 translatorId가 맞지 않지? 내꺼는 2인데 8로 요청해야 옴
- 그리고 작업 완료 많이 눌렀는데 왜 번역이 0건?

공통

- 스크롤 디자인 적용 o
- 헤더 리팩토링 o
- reload 기능 활성화 o
- Navigation 처리 o
- 햄버거 메뉴
- 컴포넌트 props 추가된 부분 주석 추가
- form input validation 기준 정리
- 스켈레톤 UI or 스피너 적용
- 회원탈퇴 및 로그아웃 구현
