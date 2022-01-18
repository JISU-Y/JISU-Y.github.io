---
layout: single
title: "[Project] 번역가 페이지 구현"
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

### Estimate Form

TranslationList에서 클릭된 Estimate의 정보를 가져와서 page에 보여주는 것 완료되었다.

하지만, 그 id를 가지고 견적서 POST 요청을 하는데 404 에러가 계속 발생하여 백엔드 개발자와 상의가 필요하다.

=> 해당 부분 api 명세서에 api 경로가 잘못 기재되어 있던 것으로 확인

```
api/estimate/list/:requestId -> api/estimate/list/detail/:requestId
```

api 경로 변경 후 정상 확인

### MyTranslationList page

```jsx
useEffect(() => {
  const fetchEstimates = async () => {
    const {
      data: { data },
    } = await apis.fetchMyList();
    setEstimates(data);
  };
  fetchEstimates();
}, []);

const handleClick = (estimate) => {
  navigate(`/translator/estimate/${estimate.id}`, {
    state: {
      estimate,
    },
  });
};
```

MyTranslationList page 컴포넌트 mount 시 api 요청하여 견적들 모두 가져오기

card를 클릭했을 때 그 estimate의 id를 라우터로 넣어주고, state로 estimate 객체를 전달한다.

(TranslatorEstimateDetail page에서 estimate 정보를 사용해야 하기 때문)

### TranslatorEstimateDetail page

```jsx
const {
  state: { estimate },
} = useLocation();
const [estimateDetail, setEstimateDetail] = useState(null);
const navigate = useNavigate();

useEffect(() => {
  const fetchEstimate = async () => {
    const {
      data: { data },
    } = await apis.getEstimate(estimate.id);
    setEstimateDetail(data);
  };
  fetchEstimate();
}, []);

const handleClick = () => {
  navigate(`/chat/${estimateDetail.roomId}`, {
    state: { roomId: estimateDetail.roomId },
  });
};
```

useLocation으로 MyTranslationList에서 전달한 estimate 객체를 가져온다.

컴포넌트 마운트시 estimate.id를 이용해서 api 요청을 한다.(견적서 디테일을 가져오는)
data를 받아 detail(confirmedDate, offerPrice)을 화면에 나타내준다.

상담하기 버튼의 onClick시 handleClick 동작시켜 라우터에는 chat/roomId로 해당 chatting 방으로 이동하도록 한다. 이동 시, roomId를 state로 전달한다.

### Chatting api 확인

```jsx
const {
  state: { roomId },
} = useLocation();

useEffect(() => {
  const createAndFetchChatroom = async () => {
    const data = await apis.getChatContents(roomId);
    console.log(data);
  };

  createAndFetchChatroom();
}, []);

const handleSubmit = async (e) => {
  e.preventDefault();
  console.log(e.target[0].value);
  await apis.sendChat(roomId, e.target[0].value);
};

return (
  <div>
    <ChatForm onSubmit={handleSubmit} />
  </div>
);
```

받아온 roomId로 chatting 해당하는 채팅방을 요청한다.

ChatForm 을 이용하여 submit 시 sendChat api로 채팅 내용을 서버로 요청한다.

채팅 전송되는 것 확인.

채팅방 불러오기 api 확인 필요하다.
