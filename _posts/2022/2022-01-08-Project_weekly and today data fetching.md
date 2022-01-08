---
layout: single
title: "[Project] React 개인 프로젝트 weekly and today data fetching"
categories: Project
tag: [Project, React, 개인 프로젝트, 토이 프로젝트, firestore]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**React 개인 프로젝트 weekly and today data fetching**

## React 개인 프로젝트

### 요일마다 먹어야 하는 약 보여주기

firestore에서 query를 이용해 각 요일마다 먹어야 하는 약/영양제를 담은 배열을 가지고 왔다.

이제 각 요일마다 각자의 약/영양제 리스트를 보여주어야 한다.

```jsx
const weeklyPills = useSelector((state) => state.pills.weeklyPills);

useEffect(() => {
  dispatch(fetchPillsWeek());
}, []);
```

각 요일마다의 약 리스트를 담고 있는 길이가 7인 배열을 dispatch하고, store의 데이터를 구독하여 weeklyPills에 담는다.

form 전달 시 weekday로 설정한 doc만 가져온다.

```
weeklyPills = [
  0: [{…}]
  1: [{…}]
  2: []
  3: [{…}]
  4: (2) [{…}, {…}]
  5: []
  6: [{…}]
]
```

이를 기반으로 map을 이용해서 바로 화면에 보여주면 된다.

### N일마다 설정한 경우 각 요일에 보여주기

weeklyPills는 query를 이용하여 바로 배열을 가져올 수 있지만, N일마다로 되어 있는 요소들은 더 계산을 해주어야 한다.

1. N일마다는 생성일 기준으로 정한다.
2. 생성 날짜와 오늘 날짜를 구한다.
3. 생성 날짜와 오늘 날짜 주의 월요일을 기준으로 먹는 날인지 계산한다.

#### firestore timestamp 추가하기

먼저, 생성 날짜를 기준으로 해야하므로 timestamp를 추가한다.

firestore는 id생성은 자동으로 되지만, 생성날짜는 자동으로 생성되지 않아 추가해주어야 한다.

```jsx
const docRef = yield call(addDoc, collection(db, 'pills'), {
  ...action.payload,
  created: Timestamp.fromDate(new Date()),
})

const docData = yield call(getDoc, docRef)

yield put({
  type: CREATE_PILL_SUCCESS,
  payload: { id: docRef.id, created: docData.data().created, ...action.payload },
})
```

firestore에서 제공하는 Timestamp를 통해서 date를 생성한다.

id와는 달리 document field에 있는 created를 가져와야 한다. (docRef에는 created 프로퍼티가 없다.)

따라서 getDoc(docRef)를 해준다.

#### 생성 날짜 기준 이번 주 먹어야 할 리스트 계산

N일마다로 지정한 약들만 filtering 진행한다.

그 약들 하나하나에 대해 며칠 간격인지 나눈다.

1일 일 경우 매일이므로 모든 요일에 넣으면 되고,
<br>2일 이상일 경우 생성날짜와 그 주 월요일 날짜를 계산하여 n일 간격으로 요일에 넣는다.

```jsx
useEffect(() => {
  const filteredPills = pills.filter((pill) => pill.freqDay !== 0);
  filteredPills.forEach((pill) => {
    if (pill.freqDay === "1" || pill.freqDay === 1) {
      setWeekdayPills((prev) => prev.map((el) => [...el, pill]));
    } else {
      // 생성 날짜로부터 오늘날짜 기준 월~일에 어느 때 먹어야 하는지 계산
      const today = new Date();
      const pillDayArr = pill.created
        ?.toDate()
        .toLocaleDateString("ko-KO")
        .split(". ");

      const [year, month, day] = [
        today.getFullYear(),
        today.getMonth() + 1,
        today.getDate(),
      ];
      const dayOffset = today.getDay() === 0 ? 6 : today.getDay() - 1; // 일요일 7로 바꿈

      const thenDate = new Date(...pillDayArr);
      const monDate = new Date(year, month, day - dayOffset); // 그 주의 월요일만 구함

      const btMs = monDate.getTime() - thenDate.getTime();
      const btDay = btMs / (1000 * 60 * 60 * 24);

      setWeekdayPills((prev) =>
        prev.map((el, index) =>
          (btDay + index) % Number(pill.freqDay) === 0 ? [...el, pill] : el
        )
      );
    }
  });
}, []);
```

이렇게 해서 db에 있는 약/영양제를 이번 주 기준으로 먹어야 할 리스트를 보여주는 것이 완성되었다.

문제점:

계산이 너무 길고 복잡하고, 성능에 좋아보이지는 않는다..

더 좋은 방법에 대해서는 좀 더 고민을 해봐야 할 것 같다.

또한, useEffect가 마운트 될 때 한번만 실행되기 때문에 처음 home에서는 weeklyPills가 빈 배열일 때에만 연산이 되기 때문에 처음에는 아무것도 보이지 않게 된다..

(다른 페이제 갔다 오면 보임)

방법을 찾아보는 중이다.

### today (timeline)

weekly로 보여주는 컴포넌트 아래에 timeline 오늘 먹어야 하는 약 리스트만 보여주어야 한다.

weeklyPills에서 오늘 요일(date 객체 getDay로 구함)의 항목(배열)만 가져오면 된다.

```jsx
const today = new Date();
const dayOffset = today.getDay() === 0 ? 6 : today.getDay() - 1; // 일요일 7로 바꿈

setDayPills(weekdayPills[dayOffset]);
```

오늘 먹어야 할 약/영양제 리스트도 모두 보여주었다.

이제 오늘 리스트를 먹어야 하는 시간 순으로 나타내어야 한다.

리스트 요소를 클릭해서 먹은 것으로 처리하는 기능 추가해야 한다.

### 할 것

- 시간 순서대로 Today에 나타내기
- 오늘 먹을 것 리스트 밀어서 먹는(완료하기) 기능 추가
- 구글 로그인 구현
