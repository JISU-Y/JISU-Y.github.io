---
layout: single
title: "[Project] Etsy 프로젝트 컴포넌트 feedback 수정"
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

### Main 페이지 PR 피드백 수정

#### Header, Footer

tsx 파일, ts style 파일, index 파일 3개를 하나의 폴더로 묶어서 개발하는 습관!

styled-compontent는 그 의도에 맞게 nesting을 많이 쓰지 않고,

각각의 컴폰너트가 어떤 스타일인지, 어떤 기능인지를 바로 알 수 있도록 세세하게 component로 선언해준다.

### 코드 컨벤션

```jsx
{
  Array.from([1, 2, 3, 4, 5], (el) => (
    <StarIcon key={el} width={12} height={12} color="black" />
  ));
}
```

### 기타 할일

피드백 대로 코드 수정

Main 페이지 UI layout 완성하기 (grid)

Main 페이지 중복되는 컴포넌트 템플릿화 (근데 이렇게 하는 게 맞는건가)

<hr>
