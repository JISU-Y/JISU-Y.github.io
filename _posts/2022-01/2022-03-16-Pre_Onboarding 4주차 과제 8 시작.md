---
layout: single
title: "[Course / TIL] Wanted Pre Onboarding FE Course 4주차 - 팀 과제 8 시작"
categories: Course
tag: [Course, TIL, Wanted, PreOnboarding]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Course

**Wanted Pre Onboarding FE Course**

## Study

### CORS

다른 출처로 네트워크 요청을 하게 되면 SOP 정책에 위반되어 CORS 에러가 발생한다.

#### SOP

SOP은 Same-Origin Policy의 줄임말로, 동일 출처 정책을 뜻합니다. MDN에선 다음과 같이 정의합니다.

동일 출처 정책(same-origin policy)은 어떤 출처에서 불러온 문서나 스크립트가 다른 출처에서 가져온 리소스와 상호작용하는 것을 제한하는 중요한 보안 방식입니다.

한 마디로 ‘같은 출처의 리소스만 공유가 가능하다’라는 정책인데요. 여기서 말하는 ‘출처(Origin)’는 다음과 같습니다.

<img src='https://s3.ap-northeast-2.amazonaws.com/urclass-images/WIMOe6KSBg5aHTPiJCDSw-1645189714110.png' alt='sop'>

프로토콜(http / https ..)와 호스트(주소), 포트(8080, 443, 3000)의 조합으로 되어 있습니다.

이 중 하나라도 다르면 동일 출처로 보지 않습니다.

#### SOP가 필요한 이유

SOP을 통해 해킹 등의 위협에서 보다 더 안전해질 수 있다는 것인데요. 왜 그런지 알아보기 위해서 동일 출처 정책이 없는 상황을 가정해봅시다.

네이버 같은 웹 페이지에 로그인해서 서비스를 이용하고 있다고 해봅시다. 서비스 이용중이 아니더라도 로그아웃을 깜빡했거나 자동 로그인 기능으로 인해 브라우저에 로그인 정보가 남아있을 수도 있을 것입니다.

그 상태에서 로그인 정보를 노리는 코드가 있는 다른 사이트에 방문하게 된다면? 해커는 로그인 정보를 이용해서 네이버에서 사용할 수 있는 모든 기능을 이용할 수 있게 됩니다. 나도 모르는 사이에 내 계정으로 블로그나 카페에 좋지 않은 글을 올리게 될 수도, 수 천 명에게 스팸 메일을 보낼 수도 있는 것입니다.

하지만, 네이버 api 를 사용하거나 프론트와 서버를 따로 개발할 때에도 출처가 달라지므로 다른 출처의 리소스를 사용하는 일이 많습니다.

따라서 CORS를 우회할 방법을 숙지하고 있어야 합니다.

#### CORS 우회

> 교차 출처 리소스 공유(Cross-Origin Resource Sharing, CORS)는 추가 HTTP 헤더를 사용하여, 한 출처에서 실행 중인 웹 애플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제입니다.

CORS 자체는 체제이고, SOP 에 위배되기 때문에 에러가 발생하는 것이다.

1. node 서버 설정

   헤더에 Access-Control-Allow-Origin에 출처를 명시하여 설정할 수 있다.

2. express 서버

   express 프레임워크를 사용하면 더 간단하게 설정이 가능하다.

3. 프록시 서버

   서버에서 설정할 수 없는 경우 클라이언트 단에서 프록시 서버를 사용하여 요청할 수 있다.

   프록시는 클라이언트 대신 요청하여 브라우저 sop 정책에 위배되지 않고 응답을 받아올 수 있다.

   cors-anywhere를 이용하여 우회 요청하는 경우가 많다.

### OAuth

> OAuth2.0은 인증을 위한 표준 프로토콜의 한 종류
> <br>보안 된 리소스에 액세스하기 위해 클라이언트에게 권한을 제공(Authorization)하는 프로세스를 단순화하는 프로토콜 중 한 방법입니다.

웹이나 앱에서 흔히 찾아볼 수 있는 소셜 로그인 인증 방식은 OAuth 2라는 기술을 바탕으로 구현됩니다.

전통적으로 직접 작성한 서버에서 인증을 처리해 주는 것과는 달리, OAuth는 인증을 중개해 주는 메커니즘입니다. 보안된 리소스에 액세스하기 위해 클라이언트에게 권한을 제공하는 프로세스를 단순화하는 프로토콜입니다.
즉, 이미 사용자 정보를 가지고 있는 웹 서비스(GitHub, google, facebook 등)에서 사용자의 인증을 대신해 주고, 접근 권한에 대한 토큰을 발급한 후, 이를 이용해 내 서버에서 인증이 가능해집니다.

OAuth를 사용하면 편리할 뿐만 아니라 보안상 이점도 있다.

```
Resource Owner :  액세스 중인 리소스의 유저입니다. 김코딩의 구글 계정을 이용하여 App에 로그인할 경우, 이때 Resource owner은 김코딩이 됩니다.
Client :  Resource owner를 대신하여 보호된 리소스에 액세스하는 응용프로그램입니다. 클라이언트는 서버, 데스크탑, 모바일 또는 기타 장치에서 호스팅 할 수 있습니다.
Resource server :  client의 요청을 수락하고 응답할 수 있는 서버입니다.
Authorization server :  Resource server가 액세스 토큰을 발급받는 서버입니다. 즉 클라이언트 및 리소스 소유자를 성공적으로 인증한 후 액세스 토큰을 발급하는 서버를 말합니다.
Authorization grant :  클라이언트가 액세스 토큰을 얻을 때 사용하는 자격 증명의 유형입니다.
Authorization code :  access token을 발급받기 전에 필요한 code입니다. client ID로 이 code를 받아온 후, client secret과 code를 이용해 Access token 을 받아옵니다.
Access token :  보호된 리소스에 액세스하는 데 사용되는 credentials입니다. Authorization code와 client secret을 이용해 받아온 이 Access token으로 이제 resource server에 접근을 할 수 있습니다.
Scope :  scope는 토큰의 권한을 정의합니다. 주어진 액세스 토큰을 사용하여 액세스할 수 있는 리소스의 범위입니다.
```

## 팀 프로젝트

### 구현

#### redux toolkit

**구현한 방법과 이유**

```jsx
const initialState = {
  data: [],
  loading: false,
  error: null,
};

export const searchDisease = createAsyncThunk(
  "data/searchData",
  async (word) => {
    const res = await fetch(`${BASE_URL}?name=${word}`);
    const data = await res.json();
    saveCache(word, data);
    return data;
  }
);

export const diseaseReducer = createSlice({
  name: "disease",
  initialState,
  reducers: {
    clearData: (state) => {
      state.data = [];
    },
    getLocalData: (state, action) => {
      const { keyword } = action.payload;
      const cache = JSON.parse(localStorage.getItem("searchResult"));
      state.data = cache[keyword].result;
    },
  },
  extraReducers: {
    [searchDisease.pending]: (state) => {
      state.loading = true;
    },
    [searchDisease.fulfilled]: (state, action) => {
      state.loading = false;
      state.data = action.payload;
      state.error = "";
    },
    [searchDisease.rejected]: (state, action) => {
      state.loading = false;
      state.data = [];
      state.error = action.payload;
    },
  },
});
```

api 요청을 하는 것이므로 middleware가 필요한다. toolkit을 사용하고자 하였으므로 thunk를 이용하여 비동기 처리를 해주었다.

그리고 extraReducers에서 Promise status에 따른 처리를 각각 해주었다.

pending 상태일 때는 loading만 true, 데이터가 정상 도착하여 fulfilled가 되었으면 payload를 data에 할당하고 loading을 false로 변경한다. reject가 되었을 때는 error 내용을 저장하고, 통신도 끝난 상태이므로 loading 또한 false로 변경한다.

**어려웠던 점**

redux toolkit을 처음 이용

redux의 패턴은 알고 있지만, 축약된 느낌이라 메서드를 사용하는 데에 조금 생소한 느낌이었다.

#### 메서드 정의

- configureStore

  store 생성

  ```jsx
  import { configureStore } from "@reduxjs/toolkit";
  import diseaseReducer from "./diseaseReducer";

  export const store = configureStore({
    reducer: {
      data: diseaseReducer,
    },
  });
  ```

- createSlice

  createAction, createReducer를 따로 사용하여 redux를 구현할 수도 있지만, createSlice 메서드를 이용하면 위 함수가 내부적으로 사용되기 때문에 따로 사용하지 않아도 된다.

  **extraReducers**

  extraReducers는 createSlice가 생성한 액션 타입 외 다른 액션 타입에 응답할 수 있도록 한다. 외부 액션을 참조할 의도를 가지고 있다.

  따라서 미들웨어로 비동기 처리할 때 middleware에서 name에 타입을 지정해주었는데, 그 타입에 맞는 reducer가 slice안에는 없기 때문에, extraReducer안에 fulfilled, rejected, pending에 따라 타입을 지정하고 상태를 변화시킬 수 있게 된다.

## STUDY

### MSA

MSA란?

> 마이크로서비스 아키텍처에 대한 정확한 정의는 없다. 작고, 독립적으로 배포 가능한 각각의 기능을 수행하는 서비스로 구성된 프레임워크

#### Monolithic Architecture

MSA의 반대의 아키텍처를 말한다.

소프트웨어는 모든 구성요소가 한 프로젝트에 통합되어 있는 형태이다.

웹 개발에서는 서비스를 만들 때 모듈별로 개발을 하고 개발이 완료된 웹 어플리케이션을 하나의 결과물로 패키징하여 배포되는 형태가 Monolithic Architecture인 것이다.

그러나 수백명 이상의 개발자가 투입되는 프로젝트에서는 Monolithic Architecture는 한계를 보이게 된다.

**단점**

- 부분 장애가 전체 서비스의 장애로 확대

  개발자의 잘못된 코드 배포 혹은 갑작스런 트래픽 증가로 인해 성능에 문제가 생겼을 경우, 서비스 전체의 장애로 확대될 가능성이 있다.

- 부분적인 Scale-out의 어려움

  Scale-out이란 여러 server로 나누어 일을 처리하는 방식이다.
  <br> 사용되지 않는 다른 모든 서비스가 Scale-out되어야 하기 때문에 부분 Scale-out이 어렵다.

- 서비스의 변경이 어렵고, 수정 시 장애의 영향 파악도 힘들다.

  여러 컴포넌트가 하나의 서비스에 강하게 결합되어 있기 때문에 수정에 대한 영향도 파악하기 힘들다.

- 배포 시간이 오래 걸린다.

- 한 Framework와 언어에 종속적이다.

#### MSA의 특징

MSA는 api를 통해서만 상호작용할 수 있다. 즉, 마이크로 서비스는 서비스의 end-point를 api형태로 외부에 노출하고, 실질적인 세부 사항은 모두 추상화한다.

각각의 서비스는 모듈화가 되어있으며 이러한 모듈끼리는 RPC 혹은 message-driven api 등을 이용하여 통신한다.

#### MSA의 장점

팀 단위로 적절한 수준에서 기술 스택을 다르게 사용할 수 있다.

서비스별로 독립적으로 배포가 가능하므로 CD 또한 훨씬 가볍게 할 수 있다.

#### MSA의 단점

서비스가 모두 분산되어 있기 때문에 내부 시스템의 통신을 어떻게 가져가야 할 지 정해야 한다.

통합 테스트가 어렵다. 개발 환경과 프로덕션 환경을 동일하게 가져가는 것이 어렵다.

[참고]
https://velog.io/@tedigom/MSA-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-1-MSA%EC%9D%98-%EA%B8%B0%EB%B3%B8-%EA%B0%9C%EB%85%90-3sk28yrv0e

https://wooaoe.tistory.com/57

## 회고 (TIL)

**2022.03.16 Daily 회고**

✏오늘 한 일

- 팀 과제 회의 및 수행
  - redux toolkit
- study
  - cors
  - OAuth
  - redux toolkit

⁉느낀 점

마지막 팀 과제이다. 마지막까지 힘내서 끝내봐야겠다.

toolkit 폴더나 파일 생성이 확연히 준 것을 체감할 수 있는 라이브러리이다.
그런데, toolkit이 아직 머리 속에 잘 안들어온다. redux 패턴을 모르는 게 아닌데 왜?

🎃현재 나의 상태

요새 면접 때문에 제 정신이 아닌 상태

<hr>
