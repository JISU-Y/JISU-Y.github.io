---
layout: single
title: "[Browser] Web worker와 Service worker"
categories: Browser
tag: [Browser, Web worker, Service worker]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Web worker와 Service worker

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 web worker와 service worker는 도대체 무엇일까?
</aside>

## Web worker

> **Web Workers** makes it possible to run a script operation in a background thread separate from the main execution thread of a web application.
>
> ---
>
> [출처: mdn]

⇒ `Web Worker`는 웹 앱의 main thread와는 별개로 background thread에서 스크립트가 실행될 수 있도록 해주는 도구.

### 왜 필요하지?

싱글 스레드 언어인 자바스크립트로 된 웹 앱에서 백그라운드 스레드에서 동작을 지원하면서 **메인 스레드(보통은 UI)가 블락킹되거나 느려지지 않도록** 할 수 있다.

### 특징 및 종류

**worker 스레드 생성**

- Worker라는 생성자를 이용해서 인스턴스 생성.
- JS 파일 내에 worker thread에서 동작하게 될 코드를 작성하면 된다.
- 이벤트 드리븐 방식으로 데이터를 주고 받는다.
- 거의 모든 JS 기능을 사용할 수 있다.

- 하지만! DOM 제어는 불가능.

**웹 워커 종류**

- **Dedicated worker**: 전용 워커  
  하나의 JS 스레드에만 사용되는 유일한 워커.
- **Shared worker**: 공유 워커  
  같은 웹(도메인) 내에만 있다면 여러 개 스크립트에서도 공유됨 (window, iframe 등등)  
  active port로 통신함.
- **Service worker**: 서비스 워커  
  웹 앱과 브라우저 (혹은 네트워크) 간 프록시 서버 역할.  
  오프라인 작용, 네트워크 요청 인터셉트 등을 실행할 수 있음.

### Web Worker의 사용

**메인 스레드와 워커 스레드간 소통**

`postMessage` 메서드를 써서 데이터를 전송하고,

받을 때는 `onmessage` 이벤트 핸들러에서 사용할 수 있다.  
(`onmessage` 이벤트 핸들러의 인자 event에 data 속성이 존재)

- 유의점
  - 데이터는 복사되어 넘겨주는 것이지 공유하는 것이 아니다.
  - 같은 오리진일 때는 다른 탭을 계속 생성하더라도 worker는 새롭게 만들어진다.
  - 메인 스레드간 소통 이 외에도 워커 스레드에서 네트워크 통신도 가능!

**main thread**

```tsx
// main.ts

// worker thread 생성
const myWorker = new Worker("worker.ts");

const first = document.getElementById(".first-input");

first.onchange(() => {
  myWorker.postMessage(first.value); // worker에 message(data) 보내기
});

myWorker.onmessage((e) => {
  console.log(`Main thread recieved data from worker: ${e.data}`); // e.data 에서 데이터 확인 가능
});
```

`postMessage`에 태워 보내는 데이터는 어떤 타입이든 상관 없다.

**web worker thread**

```tsx
// worker.ts

onmessage((e: Event) => {
  // onmessage 인자에 넘어오는 event 객체에 data 어트리뷰트에서 꺼내서 사용
  const workerResult = e.data; // first.value (input 값)

  postMessage(`Worker thread recieved data: ${workerResult}`);
});
```

웹 앱, 즉 **메인 스레드의 글로벌 객체는 window**이다.

하지만 web worker 스레드는 이와 별개이므로 여기서 window 없이 바로 특정 속성이나 메서드를 참조하면 에러가 발생한다. (window라고 명시 해주어야 한다.)

그래도 window의 websocket이나 indexedDB를 포함하여 여러 메서드나 속성을 사용할 수 있다. (단, DOM manipulation이 안된다.)

worker에 갔다오는 연산은 main 스레드를 블락킹하지 않으므로 UI는 멈추거나 느려지지 않으면서 무거운 연산이 가능해진다.

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
🤔 react에서는 이를 해결하기 위해 동시성 개념을 넣어서 useTransition인가 암튼 그거 사용해서 UI는 끊기지 않으면서 연산할 수 있도록 해주는 게 있는데 어떤 게 더 좋지? 이게 무조건 좋은 건가?

⇒ 일단, react에서 동시성 모드(concurrent mode)는 병렬로 진행되는 것이 아닌 메인 스레드에서 동작하고 급한 task 부터 처리하는 방식이다.  
얻고자 하는 이득은 비슷해보이나, 동작은 다르다고 할 수 있을 것 같다.  
이 부분은 동시성을 추가적으로 공부하면서 알아보아야 할 것 같다.

</aside>

**에러 핸들링**

onerror 이벤트로 에러를 listening.

```tsx
// worker.ts

onerror((error: ErrorEvent) => {
  const workerErrorResult = e.message;

  postMessage(`Worker recieved error: ${workerErrorResult}`);
});
```

ErrorEvent는 기본 interface

- message: 에러 메시지
- filename: 에러 발생 파일 이름
- fileno: 에러 발생 line number

**연결 끊기**

web worker는 main 스레드와 연결을 끊을 수 있다.

```tsx
// main.ts

myWorker.terminate();
```

## Shared Worker

> dedicated worker와는 다르게 same origin이면 window, iframes 등이 달라도 여러 스레드에 접근이 가능한 워커이다.

**생성**

```tsx
const myWorker = new SharedWorker("worker.js");
```

```tsx
squareNumber.onchange = () => {
  myWorker.port.postMessage([squareNumber.value, squareNumber.value]);
  console.log("Message posted to worker");
};
```

```tsx
onconnect = (e) => {
  const port = e.ports[0];

  port.onmessage = (e) => {
    const workerResult = `Result: ${e.data[0] * e.data[1]}`;
    port.postMessage(workerResult);
  };
};
```

다른 탭들을 port로 구분

## Service worker

> **Service workers** essentially act as proxy servers that sit between web applications, the browser, and the network (when available).
>
> ---
>
> [출처: mdn]

- 웹 앱 ↔ 브라우저 (혹은 네트워크) 사이 프록시 서버 역할.
- 특정 상황 (보통은 네트워크 없을 때) 에서 앱이 어떻게 동작할지를 컨트롤할 수 있도록 여러 기능을 사용할 수 있다.
  - 보통 오프라인 작용, 네트워크 요청 인터셉트 등에 사용.
- web worker와 동일하게 메인 스레드와는 별개  
  이것 또한 DOM access는 불가능. 그렇기 때문에 메인 스레드를 막지 않는다.
- 동기 코드들은 서비스 워커 내에서 사용 불가능  
  동기적인 XHR, Web Storage api들은 이 서비스 워커 내에서 사용할 수 없다.

- **대표 use case**
  - Background data synchronization.
  - Responding to resource requests from other origins.
  - Receiving centralized updates to expensive-to-calculate data such as geolocation or gyroscope, so multiple pages can make use of one set of data.
  - Client-side compiling and dependency management of CoffeeScript, less, CJS/AMD modules, etc. for development purposes.
  - Hooks for background services.
  - Custom templating based on certain URL patterns.
  - Performance enhancements, for example pre-fetching resources that the user is likely to need in the near future, such as the next few pictures in a photo album.
  - API mocking.

### Service Worker 사용

서비스 워커를 사용하려면

`[ServiceWorkerContainer.register()](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerContainer/register)` 로 등록한다.

그럼 서비스 워커가 다운로드 됨. 이후 설치 및 activate 됨.

```tsx
// main.js

const checkSWAvailable = () => {
  const isServiceWorkerSupported = "serviceWorker" in navigator;
  const isNotificationSupported = "Notification" in window;

  if (!isServiceWorkerSupported) {
    throw new Error("No Service Worker support!");
  }
  if (!isNotificationSupported) {
    throw new Error("No Notification API Support!");
  }

  return {
    isServiceWorkerSupported,
    isNotificationSupported,
  };
};

if (checkSWAvailable().isServiceWorkerSupported) {
  //브라우저에 Service Worker를 등록
  navigator.serviceWorker
    .register("service-worker.js")
    .then((registration) => {
      console.log("[ServiceWorker] 등록 성공: ", registration);
    })
    .catch((err) => {
      console.log("[ServiceWorker] 등록 실패: ", err);
    });
}

if (checkSWAvailable().isNotificationSupported) {
  // 알림 허용 / 차단 체크 요청
  Notification.requestPermission().then((result) => {
    // 'granted' | 'default' | 'denied'
    if (result === "granted") {
      console.log("[Notification] 허용: ", result);
    } else {
      console.log("[Notification] 차단: ", result);
    }
  });
}
```

service worker running → application에서 확인 가능

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-05/06-service-worker-start.jpg)

웹 페이지/사이트와 수명주기가 완전히 별개이므로  
요청하지 않으면 없는 것이고, 웹페이지가 닫혀도 비활성화 되지 않는다.

```tsx
// service-worker.js

// install event
window.self.addEventListener("install", (e) => {
  console.log("[Service Worker] Installed", e);
});

// active event
window.self.addEventListener("activate", (e) => {
  // This will be called only once when the service worker is activated.
  console.log("[Service Worker] activated", e);
});

// Push Message 수신 이벤트
window.self.addEventListener("push", (event) => {
  console.log("[ServiceWorker] 푸시알림 수신: ", event); //Push 정보 조회

  const title = "Push Codelab";
  const options = {
    body: "Yay it works.",
    icon: "images/icon.png",
    badge: "images/badge.png",
  };

  event.waitUntil(window.self.registration.showNotification(title, options));
});

// 사용자가 Notification을 클릭했을 때
window.self.addEventListener("notificationclick", (event) => {
  console.log("[ServiceWorker] 푸시알림 클릭: ", event);
  event.notification.close();
  event.waitUntil(
    clients.matchAll({ type: "window" }).then(function (clientList) {
      //실행된 브라우저가 있으면 Focus
      for (let i = 0; i < clientList.length; i++) {
        const client = clientList[i];
        if (client.url === "/" && "focus" in client) return client.focus();
      } //실행된 브라우저가 없으면 Open
      if (clients.openWindow) {
        return clients.openWindow(process.env.BASE_URL);
      }
    })
  );
});
```

이런 식으로 event handler를 통해서 사용한다.

---

여담: service worker from other origins 보면 등록된 서비스 워커들을 모두 볼 수 있음..  
엄청 많은 거 보니 꽤 많이 사용하는 건가 싶었음.

### 참고 링크

[web worker - mdn](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers)

[service worker - mdn](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)

[service worker - push 알림 구현](https://nsinc.tistory.com/218)

[service worker - cache 구현](https://so-so.dev/web/service-worker/)

[service worker - web push notification](https://medium.com/@a7ul/beginners-guide-to-web-push-notifications-using-service-workers-cb3474a17679)

[useWorker in react](https://javascript.plainenglish.io/multi-threaded-react-app-using-useworker-e67a593c9b51)

<hr>
