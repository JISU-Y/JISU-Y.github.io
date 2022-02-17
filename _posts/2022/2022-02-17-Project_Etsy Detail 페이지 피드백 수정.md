---
layout: single
title: "[Project] Etsy Detail 페이지 피드백 수정"
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

### Detail 페이지

#### flex 중첩

#### setState Props 전달

#### style 속성 순서

```css
.selector {
    /* Positioning */
    position: absolute;
    top: ${top}px;
    left: ${left}px;
    bottom: ${bottom}px;
    right: ${right}px;

    /* Display & Box Model */
    display: inline-block;
    max-width: 300px;
    padding: 12px;
    border-radius: 6px;
    box-shadow: 0 4px 20px ${COLORS.shadowGray};

    /* Color */
    background-color: ${bgColor};
    color: ${color};

    /* Text */
    font-size: 13px;

    /* Other */
    opacity: 0;
    pointer-events: none;
    z-index: 5;
    transform: translateY(-2px);
    transition: all 0.2s ease-in-out;
}
```

리뷰어가 판단하기 쉽고 유지보수성을 높이기 위해서 css도 순서와 그룹으로 묶어서 속성을 정한다.

참고 [https://css-tricks.com/poll-results-how-do-you-order-your-css-properties/]

### 기타 할일

PR 피드백 수정

- grid flex로 수정 (잘 안됨)
- 안티패턴 제거 o
- css attribute들 순서 setting o

장바구니 페이지 UI

장바구니 기능

<hr>
