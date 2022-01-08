---
layout: single
title: "[Project] 팀 프로젝트 폴더 디렉토리 및 component 구성"
categories: Project
tag: [Project, React, 팀 프로젝트, 폴더 디렉토리, component]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**팀 프로젝트 component 구성**

## 번역톡

### 폴더 디렉토리 및 component 구성

#### 컴포넌트 구조 짜기

디자이너 포함 팀원끼리 로직 플로우를 회의하고, 와이어 프레임을 완성했다.

일단 atomic design을 기반으로 프로젝트 디렉토리 구조를 정할 것이었기 때문에 컴포넌트를 atom 단계까지 분해해보는 과정을 가졌다.

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/01_1.jpg)

일단 common component를 확실히 했고, template도 두 가지로 생각할 수 있었다.

page는 약 15 페이지 정도가 될 예정이다.

확실히 페이지를 하나하나 분해해서 컴포넌트를 쪼개어서 보면 재사용될 컴포넌트를 정확히 할 수 있었다.

- Button, Tag, StatusMessage
  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/08_1.jpg)

- Input
  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/08_2.jpg)

- Header, Footer
  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/08_3.jpg)

- Navigation
  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/08_4.jpg)

- Menu
  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/08_5.jpg)

- Info
  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/08_6.jpg)

- Card
  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/08_7.jpg)

- Detail, Indicator
  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/08_8.jpg)

- Chat
  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/08_9.jpg)

#### 디렉토리 구조

```
┌─ public
│
├─ src
│    ├─ assets
│    │    ├─ icons
│    │    ├─ font
│    │    └─ images
│    ├─ components
│    │    ├─ Card
│    │    ├─ Button
│    │    ├─ Input
│    │    ├─ Alert
│    │    ├─ Tag
│    │    ├─ Navbar
│    │    ├─ Detail
│    │    ├─ ProfileInfo
│    │    ├─ Menu
│    │    ├─ Chat
│    │    ├─ Header
│    │    └─ Footer
│    ├─ pages
│    ├─ constants
│    ├─ styles
│    ├─ utils
│    └─ mobx
├─ .gitignore
├─ .prettierrc
├─ .eslintrc
├─ yarn.lock
├─ package.json
├─ webpack.config.js
├─ .babelrc
└─ README.md
```

컴포넌트를 나누다 보니 common에 사용되는 컴포넌트가 대부분이기도 하고, 각각의 컴포넌트가 기능을 가지고 있어 component 안에는 atoms, molecules, organisms로 구분짓지 않고 Input 기능, Button 기능 등 으로 폴더 구조를 가져가기로 했다.

### 할 것

- 아직 구현이 협의되지 않은 디테일한 부분이 많아 팀 회의 후 결정해야 한다.

- 아직 구현되지 않은 페이지가 있어 디자이너 작업 후 추가적으로 컴포넌트를 구성해야 한다.

- 와이어 프레임에서 어느 정도 디자인 시안을 해두어서 일단 최종 디자인 나오기 전 부터 작업을 시작할 것.
