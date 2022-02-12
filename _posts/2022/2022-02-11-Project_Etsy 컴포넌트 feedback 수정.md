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

#### Tab Menu Interaction

이런 식으로 hover가 있는 tab menu 를 만드려고 한다.

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-02/11_01.gif)

먼저, css로만 못할 것 같다고 생각했다.

hover interaction은 css로 가능하지만, 각 목록 간에 넘어다닐 때는 left나 transform 값을 동적으로 변경시켜야할 것 같아서 JS가 있어야 될 것 같다고 생각했다.

1. hover interaction

   hover 시 가운데부터 퍼지는 효과

   ```css
   &::after {
     content: "";
     position: absolute;
     bottom: -2px;
     left: 50%;
     transform: translateX(-50%);
     width: 0;
     border-bottom: 2px solid gray;
     transition: all 0.2s ease-in;
   }
   &:hover::after {
     width: 100%;
   }
   ```

   absolute 로 position 맞추고, bottom은 상위 컨테이너 border 보고 맞추면 된다.

   hover 전에는 width가 0, hover 시 width 100%로 둔다.

   transition을 적용하는데

   left: 50%;
   transform: translateX(-50%);

   이렇게 가운데로 끌고와줘야 가운데서 펼쳐진다. (아니면 왼쪽에서 오른쪽으로 커짐)

2. 목록마다 line 이동

   어떻게 구현할지 감이 안왔다.

   근데 생각해보니 transform, 혹은 left를 이용해서 각 목록 인덱스마다 위치를 정해주면 될 것 같았다.(width를 이용해서 계산)

   ```jsx
    export const IndicatorLine = styled.div<{
      alreadyIn: boolean;
      listWidth: number;
      linePosition: number;
    }>`
      transition: all 0.2s ease-in-out;
      ${({ alreadyIn, listWidth, linePosition }) => css`
        width: ${alreadyIn ? listWidth : 0}px;
        margin: 0 36px;
        position: absolute;
        bottom: -2px;
        left: ${linePosition + listWidth / 2}px;
        transform: translateX(-50%);
        transition: all 0.2s ease-in-out;
        border-bottom: 2px solid black;
      }`}
    `;
   ```

   alreadyIn 이것은 목록에 hover되었을 시 토글되는 state이다.

   이것을 이용해서 width가 0이 되었다가 특정 값이 되었다가 하면서 transition이 적용될 것이다.

   listWidth는 hover된 요소의 width값을 나타낸다. 각 목록마다 width가 같으면 좋겠지만 그렇지 않기 때문에 그 요소의 width값을 전달해주어야 한다.

   linePosition은 막대가 시작될 위치를 말한다. n번째 목록에 hover가 되었다면 0번부터 n-1번까지의 총 길이를 계산해서 left 속성에 값으로 넣어주어 hover된 요소 바로 아래에 line이 생기도록 하는 것이다.

   ```jsx
    <MenuContainer
      onMouseEnter={() => setAlreadyIn(true)}
      onMouseLeave={() => setAlreadyIn(false)}
    >
      <MenuList>
        {menu.map((tab, index) => (
          <Menu
            key={tab}
            onMouseEnter={e => indicatePositionLine(e.currentTarget, index)}
          >
            {tab}
          </Menu>
        ))}
        </MenuList>
      <IndicatorLine
        alreadyIn={alreadyIn}
        listWidth={listWidth}
        linePosition={linePosition}
      />
    </MenuContainer>
    <BorderLine />
   ```

   hover 시 interaction 주는 곳이 MenuContainer(상위 컴포넌트)인 이유는 처음에 들어가고 나올 떄만 가운데에서 라인이 펼쳐지는 효과가 있기 때문이다.

   목록간 이동할 때에는 left를 기준으로 transition이 생기지 저런 hover 는 딱 한번만 발생해야하기 때문이다.

   이제 진짜 목록에 hover가 되면 그 hover된 e.target을 함수에 전달하고, 몇번째인지도 알려준다.

   그 함수는 다음과 같이 실행된다.

   ```jsx
    const [alreadyIn, setAlreadyIn] = useState(false);
    const [listWidth, setListWidth] = useState(0);
    const [linePosition, setLinePosition] = useState(0);

    const indicatePositionLine = (
      target: EventTarget & HTMLLIElement,
      index: number
    ) => {
      if (!target.parentNode?.childNodes) return;

      const startPosition = Array.from(
        target.parentNode?.childNodes as NodeListOf<HTMLLIElement>
      )
        .slice(0, index)
        .reduce(
          (acc: number, tab: HTMLLIElement): number => (acc += tab.clientWidth),
          0
        );
      setLinePosition(startPosition);
      setListWidth(target.clientWidth);
    };
   ```

   함수 결과적으로는 hover된 요소의 width와 요소가 어디부터 시작해야하는 지에 대한 위치(number 값)가 설정된다.

   먼저 요소의 width는 e.target의 clientWidth로 구하면 끝이다.

   어디서부터 시작할지가 문제인데,

   0번째 부터 n-1번째의 요소들의 width값을 다 더해주면 된다.

   이 때 NodeList가 필요하기 때문에 target의 parentNode에서 모든 자식 요소를 일단 가지고 온다.

   그 자식 (li)들을 가지고 배열로 만들어 주고 (노드 리스트는 유사배열),

   몇번까지만 돌릴 것인지 slice 해준다. (이 때 index가 필요!)

   그대로 clientWidth들을 축적해서 계산해주면 css에서 left로 들어갈 시작 위치를 알 수 있다.

   ```
    left: ${linePosition + listWidth / 2}px;
    transform: translateX(-50%);
   ```

   그래서 left에 linePosition(startPosition)을 기본 값에다가 listWidth(hover된 요소의 길이)의 반을 주고, 다시 그 요소의 반을 왼쪽으로 가져와야 하기 때문에 transform -50%을 해준다. (가운데서 라인 펼쳐지는 효과)

### 코드 컨벤션

#### 컴포넌트 폴더 구조

컴포넌트 폴더 구조는 깔끔하게

폴더 안에 tsx 파일이 들어가고, tsx 파일의 style을 지정할 style.ts 파일, 밖에서 해당 컴포넌트를 사용하게 될 때 접근하도록 해주는 index.ts 파일해서 총 3개의 파일로 만드는 습관을 들이는 것이 좋을 것 같다.

#### styled-components

styled components를 사용할 때에는 깊게 nesting 하지 말고,

요소(컴포넌트) 하나마다 목적을 알려주는, 잘게 쪼개는 것이 좋다.

가독성도 훨씬 좋고, 유지보수할 때 더 좋다.

div로 감싸기만 할 때 네이밍할 것 없으면 보통

AboutContainer -> AbountWrapper -> AboutBox

이 순으로 많이 작성한다.

#### map

```jsx
{
  [1, 2, 3, 4, 5].map((el) => (
    <StarIcon key={el} width={12} height={12} color="black" />
  ));
}
```

의미없는 반복을 하려고 할 때 배열을 생성해서 바로 map을 돌리는 것보다 아래와 같이 쓰는 것이 더 좋다.

```jsx
{
  Array.from([1, 2, 3, 4, 5], (el) => (
    <StarIcon key={el} width={12} height={12} color="black" />
  ));
}

// or

Array.from({length:5}, n => ...)
```

[1, 2, 3, 4, 5] 이 배열을 직접 map을 돌리는 것이 아니라 Array.from으로 shallow copy하여 그 안에서 로직을 처리해주는 것이다.

### 기타 할일

피드백 대로 코드 수정 o

Main 페이지 UI layout 완성하기 (grid, logo 및 이미지, 배경)

Main 페이지 중복되는 컴포넌트 템플릿화 (근데 이렇게 하는 게 맞는건가)

<hr>
