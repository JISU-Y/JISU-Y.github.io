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

useState를 부모 컴포넌트에서 선언하고 바로 setState를 그대로 자식 컴포넌트에 넘겨주는 것은 대표적인 안티패턴이다.

제일 큰 문제는 자식 컴포넌트에서 setState액션이 실행될때와 부모 컴포넌트에서 setState액션이 실행될때 충돌이 일어날 수 있기 때문이다.

따라서, 부모 컴포넌트, 자식 컴포넌트 각각 state를 관리하고, 그 state를 공유할 수 있도록 만들어 주어야 한다.

그러기 위해서는 부모 컴포넌트에서 setState 액션이 포함된 핸들러 함수를 만들고 그의 인자를 state type으로 지정해서 이 핸들러 함수를 Props로 자식 컴포넌트에 전달,

자식 컴포넌트에서는 핸들러 함수에 자식 컴포넌트 state를 담아서 보내주면 된다.

- 부모 컴포넌트

```jsx
const { data, menu } = useUniqueList();

const handleCurrentTab = (tab: number) => setCurrentTab(tab);

return (
  <TabMenu list={menu} currentTab={currentTab} handleTab={handleCurrentTab} />
);
```

handleCurrentTab을 정의해서 (setState 액션이 포함된) Props로 전달하고, 부모 컴포넌트의 값을 공유할 수 있도록 currentTab을 전달한다.

- 자식 컴포넌트

```jsx
const [tab, setTab] = useState(currentTab ?? 0);

const onTabClicked = (tabIndex: number) => {
  setTab(tabIndex);
  handleTab?.(tabIndex);
};

return (
  <S.MenuContainer>
    {list?.map((menu, index) => (
      <S.TabButton
        key={menu}
        onClick={() => onTabClicked(index)}
        list={list}
        currentTab={tab}
        fontSize={fontSize}
      >
        {menu}
        {tagNumber && (
          <S.TagNumber>{tagNumber[index].toLocaleString()}</S.TagNumber>
        )}
      </S.TabButton>
    ))}
  </S.MenuContainer>
);
```

다음과 같이 tab state 또한 자식 컴포넌트에서 따로 관리하면서 받은 핸들러를 onClick에 넣어 인자를 담아서 전달하면 자식과 부모의 state가 안전하게 공유될 수 있다.

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
