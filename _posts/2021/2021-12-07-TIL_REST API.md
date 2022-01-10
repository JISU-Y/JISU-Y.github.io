---
layout: single
title: "[TIL] Javascript REST API"
categories: web
tag: [TIL, Javascript, REST API, RESTful]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## Javascript

### REST API

#### REST API란?

> REST 아키텍처 스타일을 따르는/지키는 API

- API란?
  > 소프트웨어 사이에서 정보를 주고 받기 위해 정해둔 매뉴얼대로 요청, 전송, 응답 받을 수 있는 것을 API(Application Programming Interface)라고 한다.

예를 들어 Browser에서 제공하는 api는 setTimeout, XMLHttpRequest 등 Browser가 특정 동작을 하도록 하는 API를 제공해서 개발자는 그 api를 가져다 쓰면 동작을 만들어 낼 수 있는 것이다.

- REST란?
  > REST는 REpresentational State Transfer
  > 웹을 위한 아키텍처 스타일(제약조건의 집합)
  > HTTP의 장점을 최대한 활용할 수 있도록 고안된 아키텍처 스타일이다.
  > <br>HTTP 프로토콜을 의도에 맞게 디자인 하도록 유도하는 형식이다.

이 아키텍처의 기본 원칙을 잘 지킨 서비스를 Restful하다고 한다.

> HTTP를 기반으로 클라이언트가 서버의 리소스에 접근하는 방식을 규정한 아키텍처이고, REST API는 REST를 기반으로 서비스 API를 구현한 것을 의미한다.

#### **REST API의 구성**

REST API는 자원(resourse), 행위(verb), 표현(representations)의 3가지 요소로 구성된다.

REST는 자체 표현 구조(self-de-scriptiveness)로 구성되어 **REST API만으로도 HTTP 요청의 내용을 이해할 수 있도록 작성하는 형식**이다.

    자원 : 자원 : URI(엔드포인트)
    행위 : 자원에 대한 행위 : HTTP 요청 메서드
    표현 : 자원에 대한 행위의 구체적 내용 : 페이로드

#### **REST의 특징**

Server-Client(서버-클라이언트 구조)

Stateless(무상태)

Cacheable(캐시 처리 가능)

Layered System(계층화)

Uniform Interface(인터페이스 일관성)

#### **REST API 설계 원칙**

URI는 리소소를 표현하고 행위는 HTTP 요청 메서드를 통해 해야한다.

-> URI 리소스 표현
<br>URI는 리소스를 식별할 수 있어야 하며 동사보다는 명사를 사용한다.
<br>get, show, 등등 메서드 행위같은 동사의 사용 지양
GET /getTodos/1 (X)

    GET /todos/1 (O)

-> 리소스에 대한 행위는 HTTP 요청 메서드로 표현
<br>HTTP 요청 메서드는 클라이언트가 서버에게 요청의 종류와 목적(행위)를 알리는 방법이다.
<br>(GET POST PUT PATCH DELETE) 을 사용하여 CRUD 구현한다.

    DELETE /todos/delete/1 (X)

    DELETE /todos/1 (O)

[참고 및 출처] https://www.redhat.com/ko/topics/integration/whats-the-difference-between-soap-rest
https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html
