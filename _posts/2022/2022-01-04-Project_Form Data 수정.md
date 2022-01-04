---
layout: single
title: "[Project] React 개인 프로젝트 Form Data 수정"
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

#### 복용 시간 및 복용량 추가

로직을 어떻게 해야 할지 감이 잘 오지 않는다.

일단 복용시간, 복용량 추가하는 것까지는 했다.

이제 각각 수정하는 것과 해당 속성을 db에 저장하는 것까지 해야 한다.

#### 복용 주기

n일 마다, O요일마다 나타내는 것 변경

### 할 것

- form data 변경 마무리 (중요)

- 그 다음에 오늘 먹어야 하는 것들만 보여주기 (아마 dayJS를 사용하는 것이 좋을 것 같다.)
- 시간 순서대로 Today에 나타내기
- 그 다음에 이번주에 먹을 것들 동적으로 보여주기 (먼저 form에서 며칠마다, 요일마다 입력 받는 것도 제대로 구현해야함)
- 오늘 먹을 것 리스트 밀어서 먹는(완료하기) 기능 추가

- 구글 로그인 구현
