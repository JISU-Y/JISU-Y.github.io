---
layout: single
title: "[React] React - useEffect와 비동기 처리"
categories: React
tag: [React, hooks]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# React - useEffect와 비동기 처리

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 그냥 쓰던 useEffect, 자세히는 이와 관련된 비동기 처리에 대해서 좀 더 알아보기로 한다.
</aside>

## useEffect

> `useEffect` is a React Hook that lets you synchronize a component with an external system.  
> 외부 시스템과 리액트 컴포넌트를 동기화 시켜주는 리액트 훅.

[출처]: [리액트 공식 문서](https://beta.reactjs.org/reference/react/useEffect#useeffect)

외부 시스템과 동기화라는 말이 무엇인지 정확히 모르겠지만, (추후 설명)  
먼저 react 로직의 종류를 살펴보자.

### React 컴포넌트 내부의 두 가지 로직의 종류

- **렌더링 코드**: props와 state를 가지고 디스플레이하고, 변형하고, JSX 반환해준다.
  → pure function이어야 함. 변형하기로 한 로직은 같은 값을 넣었을 때 무조건 같은 값을 반환해주어야 함.
- **이벤트 핸들러**: 컴포넌트 내 그냥 상태를 변경하는 것 말고도 다른 동작을 하는 함수. input field 업데이트, HTTP POST 요청, 라우팅 등.
  → 유저 액션(버튼 클릭, 입력)에 의해 실행되고, side effects가 일어남.

**그런데 이런 상황은 어떨까?**

```
홈 화면이 뜨면 노래 리스트를 나열해야 한다.
```

- 렌더링 코드일까? ❌
  노래 리스트 데이터를 가져오는 것은 순수 함수가 아니다!
- 이벤트 핸들러일까? ❌
  side effect이기 때문에 이벤트 핸들링으로 나타내주어야 하나?
  그건 또 아니다! 노래 리스트 가져와 달라고 클릭 버튼을 만들 수는 없는 노릇이다…

그럼 어떤 hook을 사용해야 이런 상황들을 처리할 수 있을까?

### 이 때 사용하는 것이 useEffect

특정 이벤트를 통해서 side effect를 처리하는 것이 아닌  
**렌더링에 따라 side effect를 지정**하고 싶을 때 사용한다.

(⇒ 홈 화면이 렌더링되면 노래 리스트(비동기 처리/Side effect)를 요청하도록 지정)

```jsx
import { useEffect } from "react";

export default function MusicListComponent() {
  useEffect(() => {
    const fetchMusicList = get("노래가져오는url");

    fetchMusicList();
  }, []);

  return (
    <div>
      {musicList.map((music) => (
        <div>{music.title}</div>
      ))}
    </div>
  );
}
```

### 그래서 외부 시스템 동기화란 무슨 말일까?

**외부 시스템이란?**

> 리액트 상태와 관련없는 혹은 컨트롤할 수 없는 리액트 밖의 모듈/컴포넌트/api들이다.

**외부 시스템과 동기화 해야 하는 상황**

- 브라우저 api를 이용하고 싶을 때
- 네트워크 처리할 때
- 리액트가 아닌 컴포넌트를 컨트롤하고 싶을 때
- 서버 커넥션을 셋업할 때
- 컴포넌트 뷰마다 분석 로그/이벤트를 전송할 때

### 기본적인 사용법

**useEffect 선언**

```javascript
useEffect(() => {
  // Your code here...
});
```

기본적으로 effect는 렌더 후 동작한다.

**Effect dependencies 지정**

매번 렌더링마다 effect가 동작하도록 하는 것이 싫다면,  
deps array에 상태를 지정하여 그 상태가 변경이 될 때만 동작하도록 할 수 있다.

```javascript
useEffect(() => {
  // Your code here...
}, [state]);
```

**필요 시 cleanup 함수 추가**

특정 동작들(connect, observer..)은 멈추거나 리셋해야 할 수도 있다.

이 때 첫 번째 인자로 전달했던 callback function에 함수를 return하여 cleanup function 동작을 추가할 수 있다.  
이는 언마운트 시 동작한다.

```javascript
useEffect(() => {
  chatServer.connect();
  // Your code here...

  return () => {
    chatServer.disconnect();
  };
}, []);
```

## useEffect를 통한 비동기 처리

위에서 설명한 것과 같이 data fetching을 위해 useEffect를 사용할 수 있다.

```jsx
import { useEffect, useState } from "react";

import "./styles.css";

const animals = ["cat", "dog", "rabbit", "turtle", "otter"];

const fetchAnimals = async () => {
  const animalResponse = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(animals);
    }, 250);
  });

  return animalResponse;
};

export default function App() {
  const [animals, setAnimals] = useState(null);

  useEffect(() => {
    // Promise를 반환하는 fetchAnimals 함수를 이용해서 animals state를 set한다.
    fetchAnimals().then((res) => {
      setAnimals(res);
    });
  }, []);

  return (
    <div className="App">
      {animals?.map((animal) => (
        <div key={animal}>{animal}</div>
      ))}
    </div>
  );
}
```

그러나 공식 문서에는 그렇게 추천하지 않는다. 🤷

### useEffect 안에서 비동기 처리하는 것이 별로 좋지 않은 이유

1. 서버에서 동작하지 않는다.

   ⇒ 서버에서 렌더된 HTML은 데이터가 없는 상태라서 데이터를 로드하려면 클라이언트가 모든 JS를 다운로드하고 웹 앱을 렌더해야 한다. 매우 비효율적이다.  
   즉, 클라이언트에서 렌더가 모두 끝나야 그제서야 동작하므로 느리다.

2. Network waterfalls가 발생할 가능성이 높다.

   ⇒ 네트워크 워터폴이란 부모 컴포넌트에서 데이터 fetching 후 렌더가 끝나야 다음 자식 컴포넌트에서 데이터를 fetching하기 시작하는 방식으로 느리다.

3. 데이터를 preload하거나 캐싱하지 않는다.

   ⇒ 컴포넌트가 언마운트되었다가 다시 마운트되면 다시 데이터 fetching이 일어난다.

4. 별로 자연스럽지 않다. (비동기를 위해 써야할 코드가 많아진다.)

   ⇒ useEffect로 처리하면 race condition을 신경써야하는데, 이를 해결하려면 여러가지 방법을 써서 보일러 플레이트만 늘어난다.

    <aside style='background-color : skyblue; opacity: 0.8; padding: 10px 20px'>
    ☝️ race condition
        <br/>
    경쟁 상태로 둘 이상의 입력 또는 조작의 타이밍이나 순서 등이 결과 값에 영향을 줄 수 있는 상태를 말한다.
        <br/>
    데이터 요청을 순서대로 했다고 요청한 순서대로 응답이 오는 것이 아니기 때문에 race condition이 생긴다.
    <br/>
    ⇒ 따로 알아볼 만큼 양이 꽤 되고, 공부를 해야 할 것 같아서 이번에는 다루지 않겠다..
    </aside>

### 그래서 대안은?

- framework 사용 시 제공하는 data fetching 시스템을 사용한다.

  모던 리액트 프레임워크에서는 위와 같은 좋지 않은 문제들을 개선해 적용되어 있다.  
  ex) NextJS

- 클라이언트 사이드 캐시를 만들거나 사용한다. (라이브러리를 사용해라)

  여러 문제들이 해결되어 있는 클라이언트 사이드 캐시를 제공하는 라이브러리를 사용한다.  
  ex) React Query, swr 등

  혹은 본인이 useEffect를 사용하면서도 중복 요청 제거, 캐싱, network waterfall 개선을 위해 로직들을 추가하면 된다.  
  race condition 같은 경우는 boolean flag를 사용하는 경우도 있고
  network waterfall 같은 경우는 데이터를 preload하여 개선하는 경우도 있다.

### useEffect에 async 콜백을 전달할 수 없는 이유

useEffect의 인자로 callback function과 dependencies를 optional로 줄 수 있다.  
여기서 비동기 처리 시 callback function으로 async function을 주면 안되는 것인가?

```jsx
import { useEffect, useState } from "react";

import "./styles.css";

const animals = ["cat", "dog", "rabbit", "turtle", "otter"];

const fetchAnimals = async () => {
  const animalResponse = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(animals);
    }, 250);
  });

  return animalResponse;
};

export default function App() {
  const [animals, setAnimals] = useState(null);

  useEffect(async () => {
    const animalData = await fetchAnimals(); // warning 발생!
  }, []);

  return (
    <div className="App">
      {animals?.map((animal) => (
        <div key={animal}>{animal}</div>
      ))}
    </div>
  );
}
```

Warning이 뜬다.

```jsx
Warning: useEffect must not return anything besides a function, which is used for clean-up.

It looks like you wrote useEffect(async () => ...) or returned a Promise. Instead, write the async function inside your effect and call it immediately:

useEffect(() => {
  async function fetchData() {
    // You can await here
    const response = await MyAPI.getData(someId);
    // ...
  }
  fetchData();
}, [someId]); // Or [] if effect doesn't need props or state
```

warning안에서 친절히 설명과 해결 방법까지 설명해주고 있다.

**안되는 이유**

useEffect의 콜백은 **아무것도 반환하지 않거나 clean-up을 위한 function만 반환**할 수 있다.

그런데, `async` 키워드가 붙은 function은 무조건 `Promise` 객체를 반환하도록 되어 있다.

따라서 `Promise`를 반환하게 되는 async function을 callback으로 전달하면 warning이 발생하게 되는 것이다.

### 참고 아티클

[https://beta.reactjs.org/reference/react/useEffect#useeffect](https://beta.reactjs.org/reference/react/useEffect#useeffect)

[https://beta.reactjs.org/learn/synchronizing-with-effects](https://beta.reactjs.org/learn/synchronizing-with-effects)

[https://beta.reactjs.org/reference/react/useEffect#fetching-data-with-effects](https://beta.reactjs.org/reference/react/useEffect#fetching-data-with-effects)

[https://devtrium.com/posts/async-functions-useeffect](https://devtrium.com/posts/async-functions-useeffect)

이해하기 꽤 어려운 부분이 많았어서 추가적으로 공부해야 할 것 같은 아티클이 되었다..

- race condition
- useEffect는 서버에서 돌아가지 않는다.
- preload
- NextJS의 data fetching mechanism

알아봐야 할 주제에 추가해두어야지.

<hr>
