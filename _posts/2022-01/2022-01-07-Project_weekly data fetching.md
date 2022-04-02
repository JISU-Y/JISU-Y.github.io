---
layout: single
title: "[Project] React 개인 프로젝트 weekly data fetching"
categories: Project
tag: [Project, React, 개인 프로젝트, 토이 프로젝트, firestore]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**React 개인 프로젝트 weekly data fetching**

## React 개인 프로젝트

### weekly data fetching

#### firestore query

요일마다 가지고 있는 약/영양제를 다르게 나타내주어야 하므로 firestore의 query가 필요했다.

freqWeekdays에 요일을 담은 배열이 있으므로
그 배열안에 '금'이 있는 모든 document를 가져오게 하는 것이다.

```jsx
const q = query(collRef,
  where('freqWeekdays', 'array-contains-any', action.payload))
)
```

하지만 미들웨어로 saga를 사용하므로 call을 이용해서 query를 사용했다.

```jsx
const q = yield call(
  query,
  collRef,
  where('freqWeekdays', 'array-contains-any', action.payload),
)

const querySnapshots = yield call(getDocs, q)
const docData = querySnapshots.map((doc) => doc.data())
```

가져온 docRef를 getDocs로 모두 가져온 후 doc.data를 docData 배열에 넣도록 한다.

이렇게 되면 payload에 ['금']이 들어온 경우

freqWeekdays 배열에 '금'이 있는 document를 모두 가져온다.

문제는 나는 요일별로 하나씩 가져오면 안된다.

모든 요일마다 따로따로 다 가져와야 한다.

all을 이용해서 시도해보고 있는데, getDocs 이후 data 변환이 어렵다. 아예 잘 못 가져온 걸 수도 있지만..

```jsx
const dayOfWeek = {
  월: 1,
  화: 2,
  수: 3,
  목: 4,
  금: 5,
  토: 6,
  일: 7,
}

const queries = yield all(
  Object.keys(dayOfWeek).map((day) =>
    call(query, collRef, where('freqWeekdays', 'array-contains-any', [day])),
  ),
)
const querySnapshots = yield all(queries.map((q) => call(getDocs, q)))

const docData = querySnapshots.map((querySnapshot) =>
  querySnapshot.docs.map((doc) => doc.data()),
)
```

이렇게 하여 다 가져오긴 했지만, delay가 조금 생겼다.

### 할 것

- 주마다 먹어야 할 약/영양제 fetch 해오기 (일 마다/Card 생성일 기준)
- 그 다음에 오늘 먹어야 하는 것들만 보여주기 (아마 dayJS를 사용하는 것이 좋을 것 같다.)
- 시간 순서대로 Today에 나타내기
- 오늘 먹을 것 리스트 밀어서 먹는(완료하기) 기능 추가
- 구글 로그인 구현
