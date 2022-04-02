---
layout: single
title: "[Project] 번역가 페이지 구현 (2)"
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

### Chatroom

Chatting 방 페이지 구현

ChatBalloon 추가 (시간, 채팅 내용) 확인

이름과 프로필 이미지는 아직 없음 (백엔드에서 넘겨주어야 함)

추가로, 현재 Chat contents를 가져올 때, 데이터에 userId만 있다.

userId로 상대방과 나를 구분할 수는 있지만, 어떤 id가 내것인지 알 수 없는 상황이다.

따라서 userId 이외에 translator, client를 데이터로 받아서 storage에 저장되어 있는 auth 정보와 비교하여 말 풍선을 왼쪽에 배치할지, 오른쪽에 배치할지를 나타내야 한다.

translator라면 상대방 이미지는 없으므로 default img를 사용해야 할 것 같다.

=> 로그인 시 auth를 받고 있으니 그것을 storage에 그대로 저장해두고,

채팅 컨텐츠를 받을 때 auth 정보도 함께 포함해서 받기로 결정했다.

### ChatForm 초기화

chatform에서 내용을 입력하고 submit 할 때 value를 초기화 해주는 코드가 없어

계속 input value가 그대로 남아있는 현상이 있었다.

일단, input 태그에는 value가 있어서 그것을 초기화 해주어야 한다고 생각했기 때문에 TextInput 컴포넌트에서 value prop을 다시 추가했다.

그 다음 TextInput을 사용하는 ChatForm에서 value를 전달해주어야 하는데, 여기서 전달을 하면 submit시 value를 초기화 할 수 없게 된다.

따라서 ChatForm을 사용하는 Chat page에서 state를 생성(chatText)하여 ChatForm에 value로 넘겨준다. 이는 input에 값이 변경될 때마다 chatText가 e.target.value로 set된다. 또한, submit 이벤트가 발생하면 handleSubmit이 동작하고, 그 때 chatText를 서버로 보내고, 원래 있던 chatText는 다시 ''로 초기화 한다.

ChatForm은 그냥 Chat page와 TextInput 사이에서 onChange와 value prop을 전달해주는 역할만 수행한다.

```jsx
// Chat page
const [chatText, setChatText] = useState("");

const handleSubmit = async (e) => {
  e.preventDefault();
  if (e.target[0].value === "") return;
  console.log(e.target[0].value);
  await apis.sendChat(roomId, e.target[0].value);
  setChatText("");
};

const handleChange = (e) => setChatText(e.target.value);

return (
  <ChatForm onSubmit={handleSubmit} onChange={handleChange} value={chatText} />
);
```

```jsx
// ChatForm component
const ChatForm = ({ onSubmit, onChange, value }) => {
  return (
    <form onSubmit={onSubmit}>
      <TextInput
        placeholder="채팅 내용을 입력하세요."
        onChange={onChange}
        value={value}
      />
      <Button type="submit" shortBtn content="보내기" />
    </form>
  );
};
```

```jsx
// TextInput component
const TextInput = ({ type, id, name, placeholder, onChange, value }) => (
  <Input
    type={type}
    id={id}
    name={name}
    placeholder={placeholder}
    onChange={onChange}
    value={value}
  />
);
```

### TranslatorEstimateDetail page

견적서 디테일 페이지에서 상담하기 버튼을 클라이언트가 상담하기를 누르기 전까지는
비활성화 시켜야 한다.

그런데 클라이언트가 상담하기를 누르기 전에는 데이터가 오지 않고 404 not found라고 뜬다.

클라이언트가 상담하기를 누르지 않더라도 데이터는 받아서 roomId가 있는지 없는지를 판단해서 버튼 활성화 여부를 나타내야 할 것 같다.

=> 클라이언트가 상담하기를 시작하지 않았을 때, roomId가 0인 것을 이용해서 버튼의 활성화 여부를 나타낸다.

### MyTranslationList page

견적서 리스트를 내가 보낸 견적 중 status가 ready 인 것들만 오는 현상이 있다.

DB에 임의로 다른 값(processing, done)을 넣어 test 해보았지만, status가 ready인 견적서만 불러온다.

=> 백엔드 단에서 디폴트로 ready만 보내도록 되어 있어 수정 후 모든 status에 대해 데이터 가져옴

### 웹 소켓 Chatting 구현

#### Socket.io

socket.io는 websocket 기반으로 클라이언트와 서버 간의 양방향 통신을 간으하게 해주는 모듈이다.

- emit : 데이터 전송
- on : 데이터 수신

클라이언트에서 emit한 데이터를 서버에서는 on으로 받는다. 반대로도 마찬가지

[참고] https://soonysoon.tistory.com/65

추후 리팩토링 시 socket 통신을 useChat 같은 커스텀 훅으로 사용할 수 있을 것 같다.

[참고] https://ichi.pro/ko/react-hooks-mich-socket-iolo-silsigan-chaeting-aeb-bildeu-79550473039024

- Chat 컴포넌트 구성

```jsx
import io from "socket.io-client";

export let socket;

const Chat = () => {
  const {
    state: { roomId },
  } = useLocation();
  const [chatContents, setChatContents] = useState([]); // 모든 채팅 내용
  const [chatText, setChatText] = useState(""); // 채팅 하나
  const [auth, setAuth] = useState("");

  useEffect(() => {
    const createAndFetchChatroom = async () => {
      const {
        data: { data },
      } = await apis.getChatContents(roomId);
      console.log(data);
      setChatContents(data);
    };

    createAndFetchChatroom();
  }, []);

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

  return (
    <div>
      {chatContents.map((chatContent) => (
        <ChatBalloon
          key={chatContent.id}
          name={chatContent?.User?.username ?? "???"}
          profileUrl={DefaultProfile}
          date={chatContent.createdAt}
          chat={chatContent.chat}
          isSelf={chatContent?.User?.auth === auth} // 내 auth와 채팅의 auth가 같을 때 채팅 오른쪽에 배치
        />
      ))}
      <ChatForm
        onSubmit={handleSubmit}
        onChange={handleChange}
        value={chatText}
      />
    </div>
  );
};
```

1.  socket.io-client 패키지 설치

        yarn add socket.io-client

2.  socket connect

```jsx
let socket;

// useEffect 내부
socket = io("http://52.79.79.67:3000/chat");
socket.on("connect", () => {
  console.log(`room${roomId} connected`);
});
```

socket 변수를 컴포넌트 함수 밖에 선언하고, useEffect(마운트 될 때) connection을 한 번 수행하도록 한다.

3. 서버와 연결

```jsx
socket.on("join", (message) => console.log(message));

socket.emit("join", { message: "소켓 연결 성공" });
socket.emit("join-room", { roomId });
```

서버에 설정된 name(여기서는 join)대로 on 이벤트를 부르고, 콜백 함수로 message를 받아온다.
<br> message에는 서버에서 전송하는 string이 담겨있다.

emit 메서드는 클라이언트에서 서버로 전송하는 메서드이다.

이를 이용해서 join에 클라이언트에서 서버로 보낼 message를 보낸다.

또한, join-room에는 현재 생성된 roomId가 필요하므로 그것을 전송해준다.

4. Chat message 받아오기

```jsx
useEffect(() => {
  if (!socket) return;

  socket.on("add-chat", (data) => {
    console.log(data); // 렌더링 엄청 많이 됨..
    setChatContents([...chatContents, data]);
  });
}, [chatContents]);
```

early return은 처음 컴포넌트가 마운트 될 때 socket은 undefined이기 때문이다.

on 메서드를 이용하여 add-chat에서 data(chat의 여러 프로퍼티를 가지고 있는 객체)를 받아온다.

DB에 저장되어 있는 내용을 준다고 한다.

data 객체를 그대로 원래 있던 대화 내용에 뒤에 붙인다.

문제점: 현재 한번 채팅을 전송하면 렌더링이 엄청나게 많이 된다.

그리고, 지금은 채팅을 전송하면 렌더링 때문에 가장 위로 올라간다. 따라서, 밑을 고정하고 오래된 내용들이 위로 올라가는 방식으로 화면을 구성해야한다.

### 기타 할일

Chat에서 렌더링 최적화 필요

형래님 button cursor pointer 바꿀 수 있게끔 바꿔주셔야 합니다.
TopDownButton 네비게이션에 걸려서 위치를 바꾸거나 z-index 조정을 해야할 것 같습니다.

api는 리뷰하고, chat list만 남았다.

스타일 적용
