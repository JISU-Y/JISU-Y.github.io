---
layout: single
title: "[Course / TIL] Wanted Pre Onboarding FE Course 2주차 - 팀 과제 3 진행"
categories: Course
tag: [Course, TIL, Wanted, PreOnboarding, React, 면접 대비]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Course

**Wanted Pre Onboarding FE Course**

## 팀 과제 구현

### 과제 마무리

#### memory lick 개선

datepicker modal에서 ToastMsg를 구현하느라 setTimeout을 사용하였는데, cleanup 을 해주지 않아서 다음과 같은 워닝이 계속 발생했다.

```
Warning: Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in a useEffect cleanup function.
```

원인은 modal에서 사용하는 setTimeout을 구독 취소하지 않아서이다.

그냥 cleanup 함수에 clearTimeout만 넣어준다고 해결되지 않는다.

```jsx
const [showMsg, setShowMsg] = useState(false);

useEffect(() => {
  const timer = setTimeout(() => setShowMsg(false), 3000);
  return () => {
    clearTimeout(timer);
  };
}, [showMsg]);
```

다음과 같이 timer라는 변수에 넣어주고 clearTimeout으로 해주어야 해결된다.

#### Toast message 구현

위의 state와 useEffect 에 이어서 3초 동안 Toast message가 나오는데, animation을 주어서 메시지가 올라왔다가 사라지도록 구현할 것이다.

구현하는 이유는 달력이 켜졌을 때 시작 날짜와 종료날짜를 모두 누르도록 하고 싶었기 때문이다.

이 부분은 실제 서비스와 동작이 조금 다른데, 다르기 때문에 조금이라도 UX를 향상시키기 위해 시작점이 눌렸을 때 종료지점도 클릭하라는 Toast msg를 띄워줄 것이다.

- 렌더
  ```jsx
  {
    showMsg && (
      <ToastMessage show={showMsg}>
        기간 시작점을 선택하셨습니다.
        <br />
        종료일을 선택해주세요.
      </ToastMessage>
    );
  }
  ```
- 스타일

  ```jsx
  const ToastMessage = styled.span`
    position: absolute;
    bottom: 45px;
    left: 50%;

    width: fit-content;
    padding: 8px 16px;
    border-radius: 5px;

    background-color: ${backgroundColor.primary};
    color: white;
    font-size: ${fontSize.base};

    transform: translate(-50%, 0);
    pointer-events: none;
    z-index: 0;
    ${({ show }) => css`
      animation: 3s ${show ? "showToast" : "none"};
    `}

    @keyframes showToast {
      0% {
        transform: translate(-50%, 45px);
      }
      30%,
      70% {
        transform: translate(-50%, 0);
      }
      100% {
        transform: translate(-50%, 45px);
      }
    }
  `;
  ```

  올라오고 내려가는 것은 빠르게 하려고 30%~70%일 때 떠 있도록 했다.

## 회고 (TIL)

**2022.03.02 Daily 회고**

✏오늘 한 일

- 팀 과제 수행 및 제출
  - 달력 기간 선택 기능 리팩토링
  - 토스트 메시지 기능 추가
- React study

⁉느낀 점

라이브러리를 사용해서 기능은 유사하게 되었지만, 완전히 동일하지는 않다. 그래서 추가적으로 UX를 위해 토스트 메시지를 추가했다. 다음엔 좀 더 깔끔한 구현을 할 수 있을까.

매일 팀원에게 배우는 것이 많다! 팀원을 너무 잘 만났고, 역시 팀 프로젝트를 하는 것이 좋은 것 같다.

과제를 3일 동안 했으면 좋겠다. 2일은 너무 중압감이 든다~

🎃현재 나의 상태

다음은 어떤 기능을 구현하게 될지 걱정 반 두려움 반

<hr>
