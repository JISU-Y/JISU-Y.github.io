---
layout: single
title: "[Course / TIL] Wanted Pre Onboarding FE Course 4주차 - 팀 과제 7 시작"
categories: Course
tag: [Course, TIL, Wanted, PreOnboarding]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Course

**Wanted Pre Onboarding FE Course**

## 팀 프로젝트

### 더보기 컴포넌트

#### fetch api

fetch는 promise로 보통 사용하지만, 더 깔끔한 코드를 위해 async, await로 구현하고자 했다.

```jsx
// promise
useEffect(() => {
  const fetchData = () => {
    fetch("url", {
      cache: "default",
      headers: {
        "Cache-Control": "max-age=" + 3000,
        "TEST-AUTH": "wantedpreonboarding",
      },
    })
      .then((res) => res.json())
      .then((data) => setContents(data))
      .catch((error) => console.error(error));
  };
  fetchData();
}, [sector]);
```

그런데, 갑자기 어떻게 하는지 까먹었다...

async await도 promise 기반이기 때문에 구현을 못할리는 없어서 찾아보게 되었다.

결론은 response에 json화 시킬 때도 await를 붙여주면 된다!

```jsx
// async await
useEffect(() => {
  const fetchData = async () => {
    try {
      const res = await fetch("url", {
        cache: "default",
        headers: {
          "Cache-Control": "max-age=" + 3000,
          "TEST-AUTH": "wantedpreonboarding",
        },
      });
      const data = await res.json();
      setContents(data.content);
    } catch (error) {
      console.error(error);
    }
  };
  fetchData();
}, [sector]);
```

## 회고 (TIL)

**2022.03.14 Daily 회고**

✏오늘 한 일

- 팀 과제 회의 및 수행
  - 더보기 컴포넌트 구현
- 모의 면접 대비
  - 브라우저 및 네트워크

⁉느낀 점

네트워크는 너무 뭉뚱그려서만 아는 것 같다. 외운다고 될까? 따로 책을 사서 공부해야겠다.

정신이 없다. 여기저기 면접도 있고, 부족하다 보니 떨릴 수 밖에 없다. 여기저기 많이라도 봐보자.

🎃현재 나의 상태

얼마 안남았다고 생각하니 벌써부터 좋다.

<hr>
