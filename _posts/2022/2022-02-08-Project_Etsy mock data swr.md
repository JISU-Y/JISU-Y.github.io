---
layout: single
title: "[Project] Etsy 프로젝트 Mock data SWR로 fetching하기"
categories: Project
tag: [Project, React, 개인 프로젝트, Typescript, 클론, swr, mock data]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**개인 프로젝트**

## React 개인 프로젝트 (Typescript)

### Mock data 생성

server가 없을 때 프론트엔드에서 데이터를 가지고 나타내야 하는 상황이라면 (테스트) Mock data가 필요하다.

Postman의 mock server를 사용할 수 있지만, 이번에 Mock data를 public 폴더에 생성하여 fetch하여 사용하기로 했다.

경로는 다음과 같다.

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-02/08_1.png)

public 폴더 안에 data라는 폴더를 만들고 그 안에 json 형식의 파일들을 생성한다.

사실 DB나 api 세팅을 먼저 하진 않았기 때문에 프론트엔드 구현에 초점을 맞추었다.

어떻게 하면 효율적으로, 혹은 진짜 server에서 줄 수 있을까에 대한 고민은 하지 않았다.

이후 이 data를 사용하려고 하면

```
http://localhost:3000/data/NAME.json
```

의 주소로 호출하면 된다.

#### SWR

SWR은 데이터 fetching 라이브러리이다.

상세 설명을 하기 보다 어떻게 구현했는지에 대해 알아보고, 상세 설명은 시간을 내어서 공부를 해야겠다고 생각했다.

대충은 redux보다 훨씬 간결하고, 서버와 로컬 데이터를 매칭시킬 수 있다는 장점이 있는 것을 알지만, 정확하게 설명하려면 더 많은 공부가 필요할 것 같다.

기본 data fetching 구현은 아주 아주 쉽다.

```jsx
const { data, error } = useSWR("searchBubbles.json", (url) =>
  getSearchBubbles(url)
);

if (error) return <div>failed to load</div>;
if (!circleData) return <div>...loading</div>;

// 여기서 data 사용
```

useSWR에서 첫번째 인수는 경로를 의미하고 그 첫번째 인수가 useSWR 두번째 인수의 인수로 들어간다.

두번째 인수는 fetching 함수인데, 네이티브 fetch로 구현해도 되고, axios를 사용해도 된다.

현재 나는 axios를 사용하고 있어 utis로 따로 뻈다

**질문 사항**

swr 이렇게 쓰는 것이 맞는 것인지..??

data depth가 너무 길어지는데 이거 destructuring 어떻게 하는지?

swr error 처리 안해줘도 되는 건지?

swr 여러 요청을 할 때 반복되는 것이 많아지는데 어떻게 해야 하는지?

### 기타 할일

Main 페이지에서 컴포넌트 사용해서 단으로 나타내기
-> Mock data 생성 및 swr fetching 구현 o

Detail 페이지에서는 컴포넌트 사용해서 Swiper로 나타내기

Main 페이지 UI layout 완성하기

<hr>

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-02/08_2.png)

아직은 개판이다.

그리고 아직 props interface 사용이 익숙치 않다...

큰일 났다.. 어렵다!!

typescript 신경써야하고, 구현에도 신경써야하고, 코드 리뷰가 있기 때문에 리뷰어에 대해서도 신경써서 하다보니 더 어렵게 느껴지는 것 같다.

일단 하는 수 밖에 없다.

내일은 swiper와 Main UI layout 전반적인 정리는 무조건 끝내자
