---
layout: single
title: "[Project] 번역가 페이지 style 적용 (2)"
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

### style 적용

#### ChatList 페이지 구현

스타일 모두 적용

#### Chat, TranslatorSignupForm 페이지 스타일 적용

Chat 페이지는 안에 컴포넌트들(ChatDate, ChatBalloon, 이미지)가 아직 수정이 필요하다.

TranslatorSignupForm은 기능(가능언어 추가, 이미지 업로드 시 이미지에 나타내기) 제외 스타일은 완료하였다.

### 기능 추가

#### Chat 날짜별 묶기

서버에서 가져오는 대화 내용 리스트를 하나씩 돌아서 날짜를 키 값으로 그 날짜의 대화내용의 배열을 value로 하는 객체를 생성한다.

그 객체를 돌면서 데이터 가공해서 나타내면 끝이다.

1. 서버에서 가져오는 데이터

`(31) [{…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}]`

저 객체 하나하나가 대화 내용이다. 현재 이 채팅방에는 총 31개의 대화 내용이 있는 것.

2. 대화 내용 리스트 맵 돌려서 객체 생성하기

```jsx
const makeSectionByDate = (chatList) => {
  const sections = {};
  chatList.forEach((chat) => {
    const monthDate = chat.createdAt.split("T")[0];
    if (sections[monthDate]?.length) {
      // sections[monthDate]가 이미 있는 경우에는 저기에 chat을 push해주고 없으면 값 부여
      sections[monthDate] = [...sections[monthDate], chat];
    } else {
      sections[monthDate] = [chat];
    }
  });
  setChatSections(sections);
};
```

createdAt의 타임 format은 '2022-01-18T13:50:41.000Z'이다. 이 string을 그대로 사용하여 키 값으로하면 모든 채팅 내용이 다 다른 시간을 가지고 있기 때문에 날짜마다 채팅을 묶을 수 없게 된다.

그래서 T앞의 날짜를 따와서 그것을 키 값으로 하여 그 날짜에 해당하는 대화 내용을 value에 리스트로 만들어 집어넣는다.

```
2022-01-18: (8) [{…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}]
2022-01-19: (10) [{…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}]
2022-01-20: [{…}]
2022-01-21: (12) [{…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}]
```

section을 출력해보면 이렇게 나옴.

3. section으로 묶은 데이터 map 돌려서 나타내기

```jsx
<ChatWrap>
  {Object.entries(chatSections).map(([date, chatData]) => (
    <div key={date}>
      <ChatDate date={date} />
      {chatData.map((chatContent) => (
        <ChatBalloon
          key={chatContent.id}
          name={chatContent?.User?.username ?? "???"}
          profileUrl={DefaultProfile}
          date={chatContent.createdAt}
          chat={chatContent.chat}
          isSelf={chatContent?.User?.auth === auth} // 내 auth와 채팅의 auth가 같을 때 채팅 오른쪽에 배치
          auth={auth}
        />
      ))}
    </div>
  ))}
</ChatWrap>
```

entries로 object를 배열로 변환하여 순회한다.

그 안에 요소의 데이터는 [date, chatData]이다.

date는 키 값이니 (0000-00-00)의 포맷을 가지는 string이고, chatData는 대화 내용 객체를 가지는 배열이다.

일단 먼저 date를 먼저 돌린다.

그리고 그 안에서 그 chatData를 또 순회하여 대화 내용을 나타낸다.

[참고] https://steadily-worked.tistory.com/593

무한 스크롤도 구현해야한다.

#### Socket.on 및 useEffect 무한 루프 개선

socket on을 useEffect 안에 무한 루프를 돌려서 실시간으로 되는 것 처럼 만들어 놓았었다..

이렇게 하니 채팅을 같이 두어번만 쳐도 렉이 걸렸다.ㅋㅋ 당연히...

그래서 socket 사용과 useEffect안의 setState를 개선하기로 했다.

1. socket.io-client 설정 리팩토링

```jsx
let socket;

useEffect(() => {
  // storage auth 받아오기
  setAuth(sessionStorage.getItem("auth"));

  // socket init - connection
  socket = io("http://52.79.79.67:3000/chat");

  socket.on("connect", () => {
    console.log(`room${roomId} connected`);
  });

  socket.on("join", (message) => console.log(message));

  socket.emit("join", { message: "소켓 연결 성공" });
  socket.emit("join-room", { roomId });
}, []);

useEffect(() => {
  if (!socket) return;

  socket.on("add-chat", (data) => {
    console.log(data); // 렌더링 엄청 많이 됨..
    setChatContents([...chatContents, data]);
  });
}, [chatContents]);
```

socket을 전역으로 선언해서 컴포넌트가 마운트될 때 connecting을 시도했다.

동작이 안하는 것은 아니나 let을 굳이 안써도 되는 상황이었다.

무엇보다 chatContents를 dependency array에 넣어버리면서 무한 루프가 생성되었다.

```jsx
useEffect(() => {
  // socket init - connection
  const socket = io("http://52.79.79.67:3000/chat");

  socket.on("connect", () => {
    console.log(`room${roomId} connected`);
  });

  socket.on("join", (message) => console.log(message));

  socket.emit("join", { message: "소켓 연결 성공" });
  socket.emit("join-room", { roomId });

  socket.on("add-chat", (data) => {
    console.log(data);
    setChatContents((prev) => [...prev, data]);
  });
}, []);

useEffect(() => {
  makeSectionByDate(chatContents);
}, [chatContents]);
```

일단, socket 선언과 할당 및 connect 부분을 한 useEffect에 정리하였다.

그리고 모든 setState의 데이터 업데이트는 함수형 업데이트로 했다. prev => prev + 1과 같은

또한 setChatContents를 했으면 새로 들어온 대화 내용도 날짜로 section 구분을 해주어야 하니 chatContents가 바뀔 때마다 section 나누는 함수를 실행해준다.

그러면 무한 루프도 개선되었으며 socket.on 이벤트 리스닝도 잘 되어 화면에 잘 나타난다.

### 기타 할일

번역가 가입 폼

- 가능언어 선택 시 배열에 추가되도록
- 프로필 선택 시 이미지 바뀌도록
- 버튼 크기 변경

번역 의뢰 리스트

- 필터 기능
- 버튼 크기 변경
- 필터 모달 추가
- 선택 시 배열에 추가하여 태그로 나타내기

견적서 작성 폼

- 페이지 구성

내 번역

- 보낸 견적, 진행중, 완료 필터 기능

견적서 디테일

- 페이지 스타일 적용

채팅방 리스트

- 페이지 스타일 적용(마진 등) o
- chatlistcard에 날짜 가져올 데이터가 없음 -
- isRead 동작 확인 -

채팅방

- 클라이언트에서 번역가님, 번역가에서 유저님 나오는 것 확인 o
- 작업 완료 버튼 스타일 o
- 작업 완료 버튼 기능 추가 o
- 페이지에서 채팅 리스트 위로 올라가고 항상 최신 것에 포커스되도록
- 채팅 시 무한 콘솔 개선 o
- 시간 나타내기 o
- 클라이언트에서 상담하기 클릭 시 번역가 쪽에서 봇 챗 날리기

마이페이지

- profileInfo 카드 스타일 적용
- 리뷰 data fetching 확인

공통

- 스크롤 디자인 적용
- 토글 메뉴 state 밖으로 빼서 props로 받기
