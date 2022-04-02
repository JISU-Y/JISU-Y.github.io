---
layout: single
title: "[Project] React - MobX"
categories: Project
tag: [Project, React, MobX]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**React - MobX**

## React 팀 프로젝트 시 필요 개념

### MobX

#### MobX란?

함수형 반응형 프로그래밍을 적용하여 전역 상태 관리를 단순하고 확장 가능하게 만드는 상태관리 라이브러리.

클래스에 종속적이기 때문에 hook과 같이 사용할 수 없었으나,

6버전 이후 부터는 데코레이터를 사용하지 않고, hook을 이용해서 사용하는 것이 권장되고 있다.

provider/inject 또한 legacy context를 사용하지 않고 React의 Context를 이용하는 것으로 한다.

- observable state

      관찰되고 있는 데이터 값들이 저장되어 있는 곳.

  state 같은..

- compute values

  자동 값 계산.
  <br>관련 데이터가 수정이 되면 자동으로 지정한 연산을 하여 값을 정의할 수 있다.

getter를 이용하여 구현한다.

- Reaction (Side Effects)

  compute values와 비슷하지만 값 계산 대신 콘솔, 네트워크 요청, 등 부수효과를 만들어낸다.

- Actions

  observable state에 저장되어 있는 데이터들을 변화시키는 함수

#### store 생성

```jsx
export function createFriendStore() {
  return {
    friends: [
      { name: "bob", isFavorite: true, isSingle: true },
      { name: "kim", isFavorite: true, isSingle: false },
      { name: "chi", isFavorite: false, isSingle: false },
    ],
    makeFriend(name, isFavorite, isSingle) {
      const oldFriend = this.friends.find((friend) => friend.name === name);
      if (oldFriend) {
        oldFriend.isFavorite = isFavorite;
        oldFriend.isSingle = isSingle;
      } else {
        this.friends.push({ name, isFavorite, isSingle });
      }
    },
    get singleFriends() {
      return this.friends.filter((friend) => friend.isSingle);
    },
  };
}
```

데코레이터는 사용하지 않고 정의한다.

#### context

```jsx
import { createContext, useContext } from "react";
import { createFriendStore } from "../store/friendStore";
import { useLocalStore } from "mobx-react";

const storeContext = createContext(null);

// store provider 만들기
export const FriendStoreProvider = ({ children }) => {
  const store = useLocalStore(createFriendStore);

  return (
    <storeContext.Provider value={store}>{children}</storeContext.Provider>
  );
};

export const useFriendStore = () => {
  const store = useContext(storeContext);
  if (!store) {
    throw new Error("useStore must be used within a Storeprovider");
  }
  return store;
};
```

React의 hook을 이용하여 Provider 컴포넌트를 생성한다.

#### FriendsMaster Form (observer)

```jsx
import { useObserver } from "mobx-react";
import React from "react";
import { useFriendStore } from "../context/friendStoreContext";

const FriendsMaker = () => {
  const store = useFriendStore();
  const onSubmit = (e) => {
    e.preventDefault();
    store.makeFriend(
      e.target[0].value,
      e.target[1].checked,
      e.target[2].checked
    );
  };

  return useObserver(() => (
    <form onSubmit={onSubmit}>
      <p>Total friends: {store.friends.length}</p>
      <input type="text" id="name" />
      <input type="checkbox" id="favorite" />
      <input type="checkbox" id="single" />
      <button type="submit">제출</button>
    </form>
  ));
};

export default FriendsMaker;
```

useObserver 안에 render되는 컴포넌트를 콜백함수로 감싸서 넣어준다.

#### All Friends (observer)

store에 있는 friends 데이터(배열) fetching

```jsx
import { useObserver } from "mobx-react";
import React from "react";
import { useFriendStore } from "../context/friendStoreContext";

const AllFriends = () => {
  const store = useFriendStore();

  const allFriends = store.friends;

  return useObserver(() => (
    <>
      <h4>view all friends</h4>
      {allFriends.map((friend) => (
        <p key={friend.name}>{friend.name}</p>
      ))}
    </>
  ));
};

export default AllFriends;
```

값이 변경이 되면(form 에서 friend를 추가했다면) 자동적으로 화면에 변경사항이 렌더링된다.

#### MatchMaker (observer)

```jsx
import { useObserver } from "mobx-react";
import React from "react";
import { useFriendStore } from "../context/friendStoreContext";

const MatchMaker = () => {
  const store = useFriendStore();

  const singleAndFavoriteFriends = store.singleFriends.filter(
    (friend) => friend.isFavorite
  );

  return useObserver(() => (
    <div>
      {singleAndFavoriteFriends.map((el) => (
        <p key={el.name}>{el.name}</p>
      ))}
    </div>
  ));
};

export default MatchMaker;
```

store에 데이터를 가져올 때 처음은 잘 필터링되어서 나오지만, 자동적으로 갱신이 되지 않아 이 부분 조금 더 확인 필요!

[참고]
https://mobx-react.js.org/recipes-context
https://coffeeandcakeandnewjeong.tistory.com/46#
https://jforj.tistory.com/154
https://kyounghwan01.github.io/blog/React/mobx/async/#async-await-runinaction
