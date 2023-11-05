---
layout: single
title: "[CS] Network - WebSocket"
categories: CS
tag: [cs, network, WebSocket, http]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# WebSocket

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 socket을 사용하는 방법 보다는 WebSocket이 나오게 된 히스토리를 중점으로 알아보려고 한다.

</aside>

## WebSocket이란?

### WebSocket protocol

> 프로토콜 종류 중 하나이다.
> 클라이언트와 서버 간 **양방향 통신**이며 **full-duplex 프로토콜**이다.

<aside style='background-color : transparent; color: black; opacity: 0.8; border: 1px solid black; padding: 10px 20px'>
☝ Full duplex 프로토콜?

어플리케이션이 데이터 전송과 수신을 동시에 할 수 있는 프로토콜.  
이로 인해 따로 응답없어도 정보를 전달할 수 있고, 요청하지 않아도 응답을 받을 수 있다.

</aside>

**기본 특징**

- 양방향 통신이 가능하여 업데이트된 데이터를 빠르게 나타낼 수 있다.
- `ws://`, `wss://` 로 시작한다.
- HTTP와 달리 stateful 하다.
  한 쪽 통신이 끊기기 전에는 계속 통신이 유지된다.

### WebSocket API

> 웹 소켓은 브라우저와 서버 간 양방향 통신을 가능하게 해주는 기술.

> 브라우저에서 WebSocket 프로토콜 연결을 생성 및 관리해주는 API를 제공하는 객체로 구현한다.

- 브라우저에서 제공하는 기능.
- Web Worker 스레드에서 동작하기 때문에, 메인 스레드 동작을 블락하거나 영향을 주지 않는다!  
  web worker란 메인 스레드와는 별개의 스레드.
- 사용자가 메시지를 서버에 보낼 수 있고, 응답을 이벤트 드리븐 방식으로 받을 수 있게 된다. (응답을 따로 요청하지 않아도)

[출처 - MDN:WebSockets_API](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)

⇒ 이를 기반으로 래핑된 라이브러리가 **`Socket.IO`** 인 것이다.

## Socket이 나오게 된 계기

HTTP 프로토콜은 클라이언트가 요청해야만 응답을 줌.  
요청을 하지 않았는데 응답을 하는 건 없음.

그래서 채팅, 알림, 주식 등등 기능들은 어떻게 하나?  
클라이언트와 서버 간 실시간 양방향 커뮤니케이션은 이전부터 계속 발전해왔다.

그렇다면 websocket이라는 스탠다드 기술이 등장하기 전 어떻게 이를 구현했었을까?

이전에 사용하던 것들

- http polling
- long polling
- streaming
- 등등

### http polling 방식

<aside style='background-color : transparent; color: black; opacity: 0.8; border: 1px solid black; padding: 10px 20px'>
☝ polling(short polling, long polling) 방식

클라이언트가 서버에 일정한 간격으로 정보를 poll(체킹)하는 것.  
즉, 요청을 클라이언트에서 직접 일으킨다.  
(보통 `/poll` 엔드포인트로 요청을 주기적으로 보냄.)

</aside>

### short polling 방식

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-09/07-HTTP_Polling.avif)

short polling은 클라이언트가 일정 주기로 서버에 데이터를 요청한다.  
서버가 가지고 있는 데이터가 업데이트 되어 있든 아니든 상관없다.

단점

- 비효율적이고 리소스를 많이 사용한다. (계속 주기적으로 요청을 하기 때문에)
- 주기 사이에 데이터가 업데이트 되는 경우 stale한 데이터가 디스플레이된다.

### long polling 방식

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-09/07-HTTP_Long_Polling.avif)

기존 polling 방식의 비효율성을 개선한 방식.

클라이언트가 서버에 요청을 보내면 서버는 응답을 보류하고 **데이터가 생성되면 그 때 응답 전송**.

트래픽을 좀 줄일 수 있음.

요청 또한 매번 주기적으로 보내는 것이 아니라 새 요청을 열고, 서버가 새 데이터를 보낼 때 까지 연결을 유지한다.

업데이트가 따로 없는 요청이라면 연결이 timeout 된다.

이후 다시 요청, 즉 새로운 커넥션을 만들어 다음 업데이트를 기다리게 된다.

보통 `100` ~ `300` seconds 정도로 설정한다고 함.

단점

- 다발적인 요청/응답이 있는 경우 polling과 마찬가지로 부하 발생
- HTTP 통신이므로 req, res 헤더가 불필요하게 크다.
  - WebSocket header는 최소 2바이트 부터라고 함.  
    (최소한의 정보만 담고 있기 때문)

### streaming

스트리밍은 HTTP 연결을 계속 유지.

서버 값이 업데이트 되면 그 때 그 때 응답 전송.

- 연결 유지해야 하기 때문에 연결에 대한 유효성 관리 필요
- 마찬가지로 HTTP 통신이므로 req, res 헤더가 불필요하게 크다.

### WebSockets

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-09/07-data-send-and-receive.png)

단일 TCP/IP 커넥션을 통한 HTTP 기반으로 동작하는 채널에서 실시간 양방향 통신 가능.

먼저 handshake - HTTP TCP 통신과 동일하게 동작.  
(3way-handshake, 4way-handshake)

handshake 완료 후 ws 프로토콜로 전환이 성공하면 채널에서 메시지 단위로 주고 받을 수 있게 된다.

### WebRTC

WEB real time communication 라고 하는 데 추가적으로 알아보면 좋을 것 같음.

## Socket.io

얘를 왜 사용해야 하나?

WebSocket web api도 현재 최신 모던 브라우저에서 모두 제공되고 있기는 하지만,

사용할 수 없는 환경(옛날 버전 브라우저 등)에서 fallback 처리가 되어 있다.

공식 문서에 따르면,

- HTTP long-polling
- WebSocket

이 두 방식으로 구현되어 있다고 함.

☝️ **근데!** 커넥션 수립은 long-polling 방식을 디폴트로 사용한다고 함.

이유는

```
By default, the client establishes the connection
with the HTTP long-polling transport.

But, why?
While WebSocket is clearly the best way
to establish a bidirectional communication,
experience has shown that it is not always possible
to establish a WebSocket connection,
due to corporate proxies, personal firewall,
antivirus software...
From the user perspective,
an unsuccessful WebSocket connection can translate
in up to at least 10 seconds of waiting
for the realtime application to begin exchanging data.
This perceptively hurts user experience.
```

WebSocket 방식은 모종의 이유들(안티바이러스 백신, firewall 등등 사용자 환경)에 의해 수립이 항상 가능해지는 건 아니라고 함.

### 실제 요청

1. handshake

   - session id를 발급 받음
   - 후속 요청에 sid에 session id를 계속 사용.

   ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-09/07-websocket-handshake.png)

2. send data (HTTP long-polling)

   ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-09/07-websocket-http-long-polling-send-data.png)

3. receive data (HTTP long-polling)

   ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-09/07-websocket-http-long-polling-receive-data.png)

4. upgrade (WebSocket)

   ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-09/07-websocket-socket-upgrade.png)

5. receive data (HTTP long-polling)

   WebSocket 커넥션이 정상적으로 연결되었으면 polling 종료됨.

   ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-09/07-websocket-http-long-polling-close.png)

### 참고 링크

https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers

https://pusher.com/blog/what-came-before-websockets/#ajax

https://www.geeksforgeeks.org/what-is-web-socket-and-how-it-is-different-from-the-http/

[https://doozi0316.tistory.com/entry/WebSocket이란-개념과-동작-과정-socketio-Polling-Streaming](https://doozi0316.tistory.com/entry/WebSocket%EC%9D%B4%EB%9E%80-%EA%B0%9C%EB%85%90%EA%B3%BC-%EB%8F%99%EC%9E%91-%EA%B3%BC%EC%A0%95-socketio-Polling-Streaming)

[https://doozi0316.tistory.com/entry/HTTPHTTPS란-TCP-UDP-HandShake-개념-정리](https://doozi0316.tistory.com/entry/HTTPHTTPS%EB%9E%80-TCP-UDP-HandShake-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC)

https://socket.io/docs/v3/how-it-works/

<hr>
