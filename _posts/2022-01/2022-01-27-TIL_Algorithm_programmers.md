---
layout: single
title: "[Algorithm] Programmers - 신고 결과 받기, 위장"
categories: Algorithm
tag: [Algorithm, javascript, programmers, hash, 구현]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Algorithm

**Programmers**

## 알고리즘 문제 풀이

### 신고 결과 받기

#### 문제

level 1

user list와 누가 누굴 신고했는지에 대한 배열과 정지되는 신고 횟수를 입력으로 받고,

user list의 순서대로 자신이 신고한 사람 중 정지된 사람의 수 배열로 반환하는 문제.

#### 풀이

먼저, 본인이 신고한 사람 중 정지된 사람을 세어야 하니 userID를 키로 갖는 object를 생성하고자 했다.

마찬가지로 신고당한 사람이 얼마나 신고 당했는지 알려면 신고당한 사람을 키로 갖는 object를 생성하고자 했다.

```jsx
// 여기서 email 받은 것 카운팅
const userList = Object.fromEntries(id_list.map((el) => [el, 0]));
// 여기서 자신을 신고한 사람을 배열로 넣음
const reportList = Object.fromEntries(id_list.map((el) => [el, []]));
```

userList에는 { muzi: 0, frodo: 0, apeach: 0, neo: 0 } 0으로 값을 초기화 하고 각 userId를 key로 갖는 object가 들어간다.

reportList에는 { muzi: [], frodo: [], apeach: [], neo: [] } 본인을 누가 신고했는지 신고한 사람들의 배열이 들어간다.

원래 똑같이 0으로 하려고 했지만, 수만 필요한 것이 아니고, 누가 신고했는지 알아야 정지당했을 때 신고한 사람에게 메일을 보낼(카운팅을 할) 수 있기 때문에 빈 배열로 초기화했다.

```jsx
// 사람마다 신고받은 횟수 카운팅
report.forEach((el) => {
  const reporter = el.split(" ")[0];
  const reportee = el.split(" ")[1];
  // 신고 받은 사람을 key, value로 신고한 사람을 배열로 넣어준다.
  // 그사람이 여러번 신고했어도 한번만 적용된다.
  reportList[reportee] = reportList[reportee].includes(reporter) //
    ? reportList[reportee]
    : [...reportList[reportee], reporter];
});
```

먼저 신고받은 사람이 누구한테 신고를 당했는지 구해준다.
<br>이때, 신고를 했던 사람은 또 신고하더라도 카운팅하면 안된다.

```jsx
Object.values(reportList).forEach((el) => {
  // 신고한 사람 배열의 길이가 k번 이상이 되면
  if (el.length >= k) {
    // 각 신고한 사람을 키로 가져가서 1을 증가 시켜준다.
    el.map((reporter) => userList[reporter]++);
  }
});
```

신고받은 사람들의 object에 값을 다 넣어주었으면 그것을 토대로 정지 당할 사람, 정지당했으면 신고한 사람에게 메일을 보낸다.(카운팅을 한다)

그 결과로 userList는 { muzi: 2, frodo: 1, apeach: 1, neo: 0 } 가 도출된다.

따라서 값들을 array 형태로 바꾸어서 return 해주면 된다.

#### 코드

```jsx
function solution(id_list, report, k) {
  const userList = Object.fromEntries(id_list.map((el) => [el, 0]));
  const reportList = Object.fromEntries(id_list.map((el) => [el, []]));

  report.forEach((el) => {
    const reporter = el.split(" ")[0];
    const reportee = el.split(" ")[1];
    reportList[reportee] = reportList[reportee].includes(reporter) //
      ? reportList[reportee]
      : [...reportList[reportee], reporter];
  });

  Object.values(reportList).forEach((el) => {
    if (el.length >= k) {
      el.map((reporter) => userList[reporter]++);
    }
  });

  return Object.values(userList);
}
```

### 위장

#### 문제

해시 level2

옷의 종류와 이름이 주어지고, 사람이 전날과 겹치지 않게 입을 수 있는 조합의 수를 return 하는 문제

#### 풀이

일단 답을 보게 되었다.

처음에는 1개 고르고, 2개 고르고, 3개... 고를 때 모두의 경우의 수를 더하려고 했었다.

그런데, 규칙을 발견하지 못하여서 코드로 옮기지를 못했다.

그런데 검색을 해보니 경우의 수를 구하기 보다 이렇게 구현을 하는 것이 좋은 것 같았다.

결론은 종류마다의 옷 개수에서 1씩을 더해준다음에 모두 곱해주면 된다.

왜냐하면, 모든 종류의 옷을 갖춰 입어야 하는 것이 아니기 때문이다.

결과가 안 입은 것까지 포함시키기 때문에 마지막에 -1만 해주면 된다.

#### 코드

```jsx
function solution(clothes) {
  const clothesObj = {};

  clothes.forEach((el) => {
    clothesObj[el[1]] = clothesObj[el[1]] ? clothesObj[el[1]] + 1 : 1;
  });

  let result = 1;

  for (const key in clothesObj) {
    result *= clothesObj[key] + 1;
  }

  return result - 1;
}
```
