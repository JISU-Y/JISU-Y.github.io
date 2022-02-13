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

#### Grid

```jsx
export const PicksWrapper = styled.div`
  display: grid;
  grid-template-rows: repeat(6, 100px);
  grid-template-columns: repeat(4, 1fr);
  justify-items: stretch;
  grid-auto-flow: column;
  row-gap: 18px;
  column-gap: 18px;
`;

export const ImageCardWrapper =
  styled.div <
  { index: number } >
  `
  display: block;
  width: 100%;
  height: 100%;
  & > div {
    width: 100%;
    height: 100%;
  }
  img {
    width: 100%;
    height: 100%;
  }
  ${({ index }) => css`
    grid-row-start: ${index < 4 ? 1 : index % 2 === 0 ? 3 : 4};
    grid-column-start: ${(index % 4) + 1};
    grid-row-end: ${index > 3 ? 6 : index % 2 === 0 ? 3 : 4};
    grid-column-end: ${(index % 4) + 2};
  `}
`;
```

#### Tab Menu interaction 리팩토링

React에서 DOM을 직접 조작하는 것은 지양해야하기 때문에

최대한 CSS로 구현하는 것이 좋다.

CSS로만 구현하려면 일단 한가지 포기해야하는 것이 있다.

hover되는 요소의 index만 styled component로 넘겨주어서 계산을 해주어야 하므로

모든 요소의 width가 같아질 수밖에 없다.

원래 사이트는 요소마다 width가 다르다..

일단 성능이 최우선이므로 조금은 덜 눈에 띄는 width 크기만 감안하고 다시 개선했다.

```jsx
const [lineIndex, setLineIndex] = useState(0);

// return
<S.MenuList>
  {menu.map((tab, index) => (
    <S.Menu
      key={tab}
      onMouseEnter={() => setLineIndex(index)}
      onMouseLeave={() => setLineIndex(index)}
      index={lineIndex}
    >
      {tab}
    </S.Menu>
  ))}
  <S.IndicatorLine />
</S.MenuList>;
```

일단 직접 DOM에서 clientWidth를 가져오는 부분은 모두 삭제하였다.

그리고, hover 될 때마다 그 요소의 index를 styled component 에 props로 넘겨준다.

```jsx
export const Menu =
  styled.li <
  { index: number } >
  `
  width: 100%;
  height: 100%;
  cursor: pointer;
  font-size: 14px;
  line-height: 18px;
  padding: 6px;
  position: relative;
  text-align: center;
  &:first-child,
  &:last-child {
    margin: 0;
  }
  ${({ index }) => css`
    & ~ ${IndicatorLine} {
      width: 0;
      left: ${index * 12.5 + 12.5 / 2}%;
    }
    &:hover ~ ${IndicatorLine} {
      width: 12.5%;
      left: ${index * 12.5 + 12.5 / 2}%;
    }
  `}
`;
```

index를 가지고 위치를 계산한다.

12.5인 이유는 메뉴가 8개 이기 때문이고, 가운데서 펼쳐지는 효과를 구현해야 하기 때문에 12.5의 반을 더해준다.

#### ToolTip 템플릿 구현

ToolTip이 사용되는 곳이 많기 때문에 컴포넌트로 만들어야 했다.

하지만, ToolTip의 경우 안에 text만 들어가는 것이 아니라 list가 들어갈 수도 있고, icon이 들어갈 수도 있으므로 템플릿으로 만들어서 component 자체를 props로 받도록 구현했다.

```jsx
function ToolTipTemplate({
  element,
  bgColor,
  color,
  top,
  left,
  right,
  bottom,
}: Props) {
  return (
    <S.ToolTipContainer
      className="tooltip"
      bgColor={bgColor}
      color={color}
      top={top}
      left={left}
      right={right}
      bottom={bottom}
    >
      {element}
    </S.ToolTipContainer>
  );
}
```

element의 type은 모든 컴포넌트가 올 수 있으므로 ReactElement<any, any>로 주었다.

그 element를 ToolTipContainer 안에 넣어주고, 나머지 props들은 ToolTip css에 관한 속성을 받는다.

ToolTip의 css는 일단 안 보이도록 해야하므로 다음과 같이 설정해주었다.

```css
.tooltip {
  display: inline-block;
  position: absolute;
  font-size: 13px;
  padding: 12px;
  border-radius: 6px;
  box-shadow: 0 4px 20px rgba(34, 34, 34, 0.15);
  opacity: 0;
  transform: translateY(-2px);
  transition: all 0.2s ease-in-out;
  z-index: 5;
}
```

tooltip을 클래스네임으로 따로 준 이유는 밖에서 사용할 때 tooltipContainer의 css에 opacity 등을 적용해야 하기 때문이다.

ToolTip 사용할 때

```jsx
<S.ContentMoreSpan> {extra}</S.ContentMoreSpan>
<ToolTipTemplate
  bgColor="#fff"
  color="#000"
  element={
    <S.ToolTipUl>
      {aboutDescriptions.map(li => (
        <S.ToolTipLi key={li}>{li}</S.ToolTipLi>
      ))}
    </S.ToolTipUl>
  }
/>
```

```jsx
export const ContentMoreSpan = styled.span`
  cursor: help;
  border-bottom: 2px dashed black;
  &:hover ~ .tooltip {
    display: inline-block;
    opacity: 1;
    transform: translateY(0);
    transition-delay: 0.5s;
  }
`;
```

원하는 요소에 hover가 되었을 때 tooltip 클래스네임을 이용해서 opacity 1 등 추가적인 transition을 위한 속성들을 넣어주어 사용하도록 구현했다.

### 기타 할일

피드백 대로 코드 수정 o

Main 페이지 UI layout 완성하기 (grid, logo 및 이미지, 배경)

Hover 하면 after에서 그림자 숙 나오는거 컴포넌트 화 (너무 많이 쓰임)

<hr>
