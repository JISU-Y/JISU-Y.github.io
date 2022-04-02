---
layout: single
title: "[Project] Etsy Detail 페이지 완성"
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

#### personalization textarea 부분

### 컴포넌트 Refactoring

Component hover interaction이 동일한 부분이 많이 있었다.

따라서 hover효과가 적용된 button을 컴포넌트로 만들어서 재사용하고자 했다.

1. Background가 없는 상태에서 hover 시 작았다가 커지는 효과의 버튼

   먼저 Sign in, 장바구니, Footer location에 쓰이는 버튼의 Hover interaction

   ```jsx
   function BgAnimatedButton({
     buttonLabel,
     useToggle,
     shouldShow,
     setShouldShow,
     textColor,
     bgColor,
   }: Props) {
     return (
       <Button
         textColor={textColor}
         bgColor={bgColor}
         onClick={() => (setShouldShow ? setShouldShow((prev) => !prev) : null)}
       >
         {buttonLabel}
         {useToggle && (
           <SVGWrapper rotated={shouldShow ?? false}>
             <Bracket width={25} height={25} color={COLORS.mainFont} />
           </SVGWrapper>
         )}
       </Button>
     );
   }
   ```

   기본적으로 buttonLabel이 들어간다. 보통 string으로 받으면 되겠지만, 장바구니 svg를 사용해야 하기 때문에

   buttonLabel의 type을 다음과 같이 정했다.

   ```jsx
   interface Props {
     buttonLabel: string | React.SVGProps<SVGSVGElement>;
     useToggle?: boolean;
     shouldShow?: boolean;
     setShouldShow?: React.Dispatch<React.SetStateAction<boolean>>;
     textColor?: string;
     bgColor?: string;
   }
   ```

   스타일은 기본적으로 background와 border는 없는 상태이며, after가 background를 대신하여 scale 될 것이다.

   after를 background를 해야지만 안에 있는 font에 영향을 주지 않으면서 크게 할 수 있다.

   ```jsx
   const Button = styled.button<{
     textColor?: string;
     bgColor?: string;
   }>`
     ${({ textColor, bgColor }) => css`
       font-size: 13px;
       font-weight: 500;
       background: none;
       border: none;
       width: fit-content;
       padding: 12px 15px;
       position: relative;
       display: flex;
       align-items: center;
       white-space: nowrap;
       color: ${textColor ?? COLORS.mainFont};
       cursor: pointer;
       &::after {
         content: '';
         width: 100%;
         height: 100%;
         position: absolute;
         top: 0;
         left: 0;
         border-radius: 30px;
         transform: scale(0.8); // 먼저 작게 설정
         opacity: 0; // 일단 보이지 않게
         background: ${bgColor ? bgColor : COLORS.hoverGray};
         transition: ${TRANSITION.quick};
       }
       &:hover::after {
         transform: scale(1); // 좀 크게 변경
         opacity: 1; // 보이게 설정
       }
     `}
   `;
   ```

2. Background나 border가 있는 상태에서 hover 시 그 상태에서 더 커지는 효과의 버튼

   위의 컴포넌트와 따로 나눈 이유는 BgAnimatedButton와 다르게 scale이 1 -> 1.02로 변하기 때문이고, hover 하지 않을 상태에서도 background나 border가 보여야 하기 때문에 다른 컴포넌트로 분리하였다.

   동일하게 Button안에 buttonLabel을 string으로 받는 jsx요소가 있으며 스타일은 다음과 같다.

   ```jsx
   const Button = styled.button<{
     textColor?: string;
     bgColor?: string;
     borderAttr?: string;
     widthFit?: boolean;
   }>`
     ${({ textColor, bgColor, borderAttr, widthFit }) => css`
       color: ${textColor};
       width: ${widthFit ? 'fit-content' : '100%'};
       min-width: 48px;
       min-height: 48px;
       border: none;
       outline: none;
       font-size: 16px;
       font-weight: bold;
       padding: 12px 18px;
       text-align: center;
       background-color: transparent;
       position: relative;
       cursor: pointer;
       &::after {
         content: '';
         position: absolute;
         top: 0;
         left: 0;
         width: 100%;
         height: 100%;
         box-sizing: border-box;
         background-color: ${bgColor ?? COLORS.mainFont};
         border: ${borderAttr ?? 'none'};
         color: ${textColor ?? COLORS.mainFont};
         transform: scale(1);
         transition: ${TRANSITION.normal};
         border-radius: 24px;
         z-index: ${borderAttr ? zIndex.base : zIndex.hide};
       }
       &:hover::after {
         transform: scale(1.02);
         background-color: ${bgColor ?? COLORS.darkGray};
       }
     `}
   `;
   ```

   hover 하지 않았을 때에도 background와 border가 보여져야하지만, hover 시 scale되었을 때 컨텐츠가 같이 커지지 않도록 모든 배경에 대한 스타일은 after에서 해준다.

### 기타 할일

PR 피드백 수정

- grid flex로 수정
- 안티패턴 제거
- css attribute들 순서 setting

장바구니 기능

<hr>
