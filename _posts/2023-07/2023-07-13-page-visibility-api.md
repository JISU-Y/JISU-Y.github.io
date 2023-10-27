---
layout: single
title: "[Browser] Page Visibility API"
categories: Web
tag: [Web, Browser, Browser api]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Page Visibility API (페이지 가시성 API)

> 요소가 보여지고 있는지, 숨겨져 있는지를 알 수 있도록 이벤트를 제공해주는 api

### 특징

- 현재 페이지의 visibility 상태도 알 수 있다.

  → 페이지가 보이지 않을 때 불필요한 요청 및 리소스 낭비를 줄이고 성능을 향상시키기 위해 사용

- 사용자가 다른 탬 혹은 윈도우 최소화를 하는 등의 동작을 하게 되면 api는 visibilitychange 이벤트를 발생시킨다.

  → 페이지에서 비디오를 보여주고 있었을 때 만약 페이지를 최소화했을 때,  
  그 때의 동영상 위치를 기억하거나 혹은 오디오만 재생되게하고 비디오 프레임은 보여주지 않는 등 처리 가능.

<aside style='background-color : transparent; color: black; opacity: 0.8; border: 1px solid black; padding: 10px 20px'>
❓ 특정 페이지에서 iframe 이 없어졌다가 사라졌다가 하면 iframe 내에 visibility는 어떻게 될까?

- iframe은 부모 document와 동일한 visibility state를 가지고 있다.
- iframe display none을 한다고 해서 visibility event가 발생하거나 하지는 않는다.
</aside>

### 예전에는..

이전에 이 api가 없었을 때는 `blur`, `focus` 이벤트를 이용해서 비슷하게 구현을 했었다.

하지만 이는 진짜로 사용자에게 보여지는지 여부를 알 수 는 없다.

→ 사용자가 다른 탭을 선택했다고 해서 그게 hidden이 된 상태는 아니라는 것. (`onblur`, `onfocus`는 달라질테지만)

창/탭을 여러 개 띄워놓고 그 중 하나를 선택했을 때,  
focus는 잃겠지만 사용자가 보는 화면에서는 사라지지 않는다.

- **`onBlur`** 이벤트는 해당 페이지나 요소가 포커스를 잃을 때 발생하며, 사용자가 다른 탭이나 다른 창으로 이동하는 경우 발생.
- 반대로 **`onFocus`** 이벤트는 페이지나 요소가 포커스를 얻을 때 발생하며, 사용자가 해당 탭으로 돌아올 때 발생.

## interface

```
Document.hidden → boolean
Document.visibilityState → string (visible | hidden)
```

- visible: at least partially visible (최소한 어느 정도라도 보여지고 있다.)  
  → **창이 최소화되지 않은 상태**이고, 가장 상위 탭임.
- hidden: 사용자에게 아예 보여지지 않는 상태.  
  → **창이 최소화되어 보여지지 않는 상태**거나, 기기 **스크린이 off** 된 상태.

## events

`visibilitychange` event

현재 탭의 컨텐츠가 보여지거나 숨겨질 때 발생되는 이벤트

## 실습

### Page Visibility api 사용

```jsx
import { useState, useEffect } from "react";

export default function App() {
  const [time, setTime] = useState(0);
  const [isRunning, setIsRunning] = useState(false);

  const startStopwatch = () => {
    setIsRunning(true);
  };

  const stopStopwatch = () => {
    setIsRunning(false);
  };

  const resetStopwatch = () => {
    setTime(0);
    setIsRunning(false);
  };

  const tick = () => {
    setTime((prevTime) => prevTime + 1);
  };

  useEffect(() => {
    let intervalId;

    if (isRunning) {
      intervalId = setInterval(tick, 1000);
    } else {
      clearInterval(intervalId);
    }

    return () => clearInterval(intervalId);
  }, [isRunning]);

  useEffect(() => {
    window.addEventListener("visibilitychange", () => {
      const isPageHidden = document.hidden;
      // console.log("visibility changed", document.hidden);

      if (isPageHidden) {
        stopStopwatch();
      } else {
        startStopwatch();
      }
    });
  }, []);

  return (
    <div>
      <div>{time}초</div>
      <button onClick={startStopwatch}>시작</button>
      <button onClick={stopStopwatch}>정지</button>
      <button onClick={resetStopwatch}>리셋</button>
    </div>
  );
}
```

### onBlur / onFocus 였다면

```jsx
import { useState, useEffect } from "react";

export default function App() {
  const [time, setTime] = useState(0);
  const [isRunning, setIsRunning] = useState(false);

  const startStopwatch = () => {
    setIsRunning(true);
  };

  const stopStopwatch = () => {
    setIsRunning(false);
  };

  const resetStopwatch = () => {
    setTime(0);
    setIsRunning(false);
  };

  const tick = () => {
    setTime((prevTime) => prevTime + 1);
  };

  useEffect(() => {
    let intervalId;

    if (isRunning) {
      intervalId = setInterval(tick, 1000);
    } else {
      clearInterval(intervalId);
    }

    return () => clearInterval(intervalId);
  }, [isRunning]);

  const [isPageVisible, setIsPageVisible] = useState(true);

  const handleBlur = () => {
    setIsPageVisible(false);
    stopStopwatch();
  };

  const handleFocus = () => {
    setIsPageVisible(true);
    startStopwatch();
  };

  useEffect(() => {
    window.addEventListener("blur", handleBlur);
    window.addEventListener("focus", handleFocus);

    return () => {
      window.removeEventListener("blur", handleBlur);
      window.removeEventListener("focus", handleFocus);
    };
  }, []);

  return (
    <div>
      <div>{time}초</div>
      <button onClick={startStopwatch}>시작</button>
      <button onClick={stopStopwatch}>정지</button>
      <button onClick={resetStopwatch}>리셋</button>
    </div>
  );
}
```

### 비교

페이지를 보고 있으면 타이머가 start 되고, 안 보고 있으면 타이머가 멈추는 프로그램.

Page Visibiility API 를 사용했을 때는 다른 페이지나 프로그램을 선택해도 사용자에게 보여지고 있으면 계속 타이머가 진행되는 것을 확인할 수 있다.

이에 반해 onBlur/onFocus로 구현했을 경우에는 페이지가 보여지고 있음에도 다른 페이지나 프로그램을 선택하면 타이머가 멈추게 된다.

| Page Visibility API                                                                          | onBlur/onFocus                                                                                          |
| -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-07/13-page-visibility-api.gif) | ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-07/13-page-visibility-onblur-onfocus.gif) |

### 활용도

충분히 활용도는 높을 것 같다.

- 동영상 서비스에서 탭을 변경했을 때 처리
- 리워드 서비스 같은 곳에서 특정 페이지에 머무르는 등 사용자 행동 트래킹 및 리워딩
- 마케팅 툴 연동에도 사용 가능 (사용자 행동 트래킹)

### 참고 링크

[page visibility api](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API)

<hr>
