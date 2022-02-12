---
layout: single
title: "[Project] Etsy Main 페이지 UI layout 완성"
categories: Project
tag: [Project, React, 개인 프로젝트, Typescript, 클론]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**개인 프로젝트**

## React 개인 프로젝트 (Typescript)

### Main 페이지 UI layout 완성

#### Header, Footer

#### Grid

#### Tab Menu interaction 리팩토링

React에서 DOM을 직접 조작하는 것은 지양해야하기 때문에

최대한 CSS로 구현하는 것이 좋다.

CSS로만 구현하려면 일단 한가지 포기해야하는 것이 있다.

hover되는 요소의 index만 styled component로 넘겨주어서 계산을 해주어야 하므로

모든 요소의 width가 같아질 수밖에 없다.

원래 사이트는 요소마다 width가 다르다..

일단 성능이 최우선이므로 조금은 덜 눈에 띄는 width 크기만 감안하고 다시 개선해보려고 했다.

// 내용 추가 필

### 기타 할일

피드백 대로 코드 수정 o

Main 페이지 UI layout 완성하기 (grid, logo 및 이미지, 배경)

Main 페이지 중복되는 컴포넌트 템플릿화 (근데 이렇게 하는 게 맞는건가)

아직 헷갈리는게 PR 지금 두개인데, push를 첫번째 걸로 했는데

그러면 두번째 PR은 어떻게 되는거..?

Hover 하면 after에서 그림자 숙 나오는거 컴포넌트 화 (너무 많이 쓰임)

<hr>
