---
layout: single
title: "[Project] React 개인 프로젝트 Form Data 수정 2"
categories: Project
tag: [Project, React, 개인 프로젝트, 토이 프로젝트, firestore]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-12/01_1.png)

# Project

**React 개인 프로젝트 Form Data 수정**

## React 개인 프로젝트

### Form Data 수정

#### 복용 시간 및 복용량 수정

복용 시간 및 복용량을 여러 개로 둘 경우,

Home에서는 복용 시간 마다 똑같은 약/영양제를 나누어야 하고,

PillSetting에서는 똑같은 약/영양제는 그냥 하나의 컴포넌트로 나타내야 한다.

이 때문에, 로직이 복잡해지고 firebase serverless 백엔드로는 깔끔한 로직을 구현할 수 있을 것 같지 않다.

따라서, 일단은 시간과 복용량은 하나만 받는 것으로 하고, 추후에 기본 기능이 안정화되었을 때 추가해보는 것으로 하기로 했다.

#### 요일 sorting

요일을 선택 시 순서에 상관 없이 선택하면 뒤죽박죽 순서대로 나타난다.

가독성이 떨어지고, 정리된 것 같지 않아서 아무렇게나 선택해도 sorting을 하기로 했다.

먼저 기존에 사용하던 요일 배열을 지우고, 각 요일 string 값을 키로 가지는 객체에 각각에다가 차례대로 수를 값으로 할당해주었다.

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

[...freqWeekdays, e.target.value].sort((a, b) => dayOfWeek[a] - dayOfWeek[b])
```

그리고 나서 배열에 대해서 sorting을 실시하고 그 안에 sorting 기준은 객체에 배열 요소(요일 string)을 넣어줌으로써 숫자 순서대로 비교할 수 있도록 한다.

이렇게 하면 아무렇게나 요일을 선택해도 월~금 순서로 정렬이 된다.

#### create fail 현상

테스트를 안해보고, 계속 Modal form data를 수정하다가 create 하니 fail이 발생하였다.

title이 undefind라서 iterate할 수 없다는 에러인데, db에 저장은 된다. (?)

해결: 결론은 내가 멍청했다.

firestore에 addDoc api를 사용해서 객체를 전달하면 docRef(Add a new document with a generated id / 즉, id를 만들어서 새롭게 생성한 document를 반환해준다.)

따라서, 그 document의 id를 이용해서 id에 action.payload(formdata)를 합친 객체를 다시 payload로 전달한다.

```jsx
const docRef = yield call(addDoc, collection(db, 'pills'), action.payload)

yield put({ type: CREATE_PILL_SUCCESS, payload: { id: docRef.id, ...action.payload } })
```

TDD의 중요성을 또 한번 느끼게 되는 것 같다. 손으로 테스팅 해야하는 경우도 많고, 언제부터 안되었는지를 알았다면 디버깅 시간을 훨씬 줄였을 것이라고 생각한다.

(modal만 계속 수정해서 modal에 문제 있는 줄 알았지만, 원래부터 안되었던 것이다 ㅠㅠ)

#### weekly pill 시작

요일별 먹어야할 약/영양제 나타내는 부분.

현재 firestore query를 이용해서 action dispatching 할 때 payload로 요일 배열을 전달한 후에 그것에 맞는 document를 가져오도록한다.

잘 되지 않는다..

### 할 것

- 주마다 먹어야 할 약/영양제 fetch 해오기
- 그 다음에 오늘 먹어야 하는 것들만 보여주기 (아마 dayJS를 사용하는 것이 좋을 것 같다.)
- 시간 순서대로 Today에 나타내기
- 그 다음에 이번주에 먹을 것들 동적으로 보여주기 (먼저 form에서 며칠마다, 요일마다 입력 받는 것도 제대로 구현해야함)
- 오늘 먹을 것 리스트 밀어서 먹는(완료하기) 기능 추가

- 구글 로그인 구현
