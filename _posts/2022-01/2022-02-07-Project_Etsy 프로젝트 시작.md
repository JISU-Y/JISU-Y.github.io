---
layout: single
title: "[Project] Etsy 프로젝트 시작"
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

### 공용 컴포넌트 생성

#### Card 컴포넌트

#### CircleCard 컴포넌트

#### ImageCard 컴포넌트

#### TabMenu 컴포넌트

#### Favorite 컴포넌트

```jsx
function Favorite({ size }: FavoriteProps) {
  const [width, height] = size === "small" ? [18, 18] : [24, 24];

  return (
    <Heart {...{ size }}>
      <HeartIcon width={width} height={height} color="black" />
    </Heart>
  );
}
```

width, height 정하는 부분 저렇게 해도 되는 것인지.. 물어보기

### FC<Component\>와 function component

#### FC<Component\>

```jsx
const SomeComponent: React.FC<Props> = ({ prop }) => {
  return null;
};
```

#### function component

```jsx
const SomeComponent = ({ prop }: Props) { return null }
```

둘 다 보통 혼용하여 사용하지만, FC로 컴포넌트를 나타내줄 때에는 안에 함수를 사용하지 않거나 간단한 함수일 때 사용. FC로 하게 되면 children을 명시해주지 않아도 접근할 수 있다.

function component를 주로 사용하게 되는데, function으로 하는 이유가 안에 있는 함수들은 const로 선언해주어야 가독성이 좋기 때문에 보통 function component로 선언하고 안에 로직들을 const로 처리한다.

### SVG Component

보통 svg는 컴포넌트로 변경하여 사용한다.

또한, svg의 쓸데없는 부분들을 제거하여 용량을 다운사이징해서 사용한다.

```jsx
interface Props {
  width: string | number;
  height: string | number;
  color: string;
}

const HeartIcon: React.FC<Props> = ({
  width = 18,
  height = 18,
  color = "black",
}) => {
  return (
    <svg
      xmlns="http://www.w3.org/2000/svg"
      width={width}
      height={height}
      stroke={color}
      viewBox="0 0 24 24"
      strokeWidth="0.5"
      aria-hidden="true"
    >
      <path d="M12 ..중략.. 5Z" />
    </svg>
  );
};
```

### styled-components props 및 css

#### props

styled-components에서 props를 받을 때, 5개 이상이 아니라면 리뷰어나 가독성을 위해

props를 다 가져오지 않고 사용하는 prop만 직접 type을 써준다.

이렇게 하면 리뷰어가 이 type이 무엇인지 바로 알 수 있게 된다.

#### css

props로 css를 동적으로 적용할 때, 원래

```jsx
width: ${props => (props.size === 'small' ? '30px' : '42px')};
height: ${props => (props.size === 'small' ? '30px' : '42px')};
```

이렇게 따로따로 props를 불러왔지만, 저것이 js를 한번 씩 실행하는 구문이므로

아래와 같이 일괄적으로 props를 받아서 한꺼번에 css를 적용해주는 것을 많이 사용한다.

```jsx
const Heart =
  styled.div <
  { size: "small" | "large" } >
  `
  ${({ size }) => css`
    width: ${size === "small" ? "30px" : "42px"};
    height: ${size === "small" ? "30px" : "42px"};
  `};
  background-color: white;
  /* 중략 */
  svg {
    margin: auto;
  }
`;
```

### 기타 할일

피드백 수정 -

Main 페이지에서 컴포넌트 사용해서 단으로 나타내기

Detail 페이지에서는 컴포넌트 사용해서 Swiper로 나타내기

Main 페이지 UI layout 완성하기
