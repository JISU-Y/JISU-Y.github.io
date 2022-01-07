---
layout: single
title: "[Project] 팀 프로젝트 component 구성"
categories: Project
tag: [Project, React, 팀 프로젝트, 디렉토리, component]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**팀 프로젝트 component 구성**

## 번역톡

### 디렉토리 구조 및 컴포넌트 구성

#### 컴포넌트 구조 짜기

디자이너 포함 팀원끼리 로직 플로우를 회의하고, 와이어 프레임을 완성했다.

일단 atomic design을 기반으로 프로젝트 디렉토리 구조를 정할 것이었기 때문에 컴포넌트를 atom 단계까지 분해해보는 과정을 가졌다.

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/01_1.jpg)

일단 common component를 확실히 했고, template도 두 가지로 생각할 수 있었다.

page는 약 15 페이지 정도가 될 예정이다.

확실히 페이지를 하나하나 분해해서 컴포넌트를 쪼개어서 보면 재사용될 컴포넌트를 정확히 할 수 있었다.

#### 디렉토리 구조

```
┌─ public
│
├─ src
│    ├─ components
│    │    ├─ common
│    │    ├─ users
│    │    ├─ translator
│    │    ├─ chatting
│    │    └─ templates
│    ├─ pages
│    ├─ constants
│    ├─ utils
│    └─ mobx
├─ .gitignore
├─ .prettierrc
├─ .eslintrc
├─ webpack.config.js
└─ .babelrc
```

상세 디렉토리 구조는 아직 정하지 않았고, 큰 틀은 이 정도가 될 것 같다.

### 할 것

- 아직 구현이 협의되지 않은 디테일한 부분이 많아 팀 회의 후 결정해야 한다.

- 아직 구현되지 않은 페이지가 있어 디자이너 작업 후 추가적으로 컴포넌트를 구성해야 한다.

- 와이어 프레임에서 어느 정도 디자인 시안을 해두어서 일단 최종 디자인 나오기 전 부터 작업을 시작할 것.
