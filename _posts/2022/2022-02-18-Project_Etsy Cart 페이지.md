---
layout: single
title: "[Project] Etsy Cart 페이지 및 장바구니 기능 구현"
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

### Cart 페이지

Cart 페이지 마크업 완성

### 장바구니 기능 구현

#### 구현 내용들

- Detail 페이지에서 옵션 선택 후 add to cart 클릭 시 Cart 페이지 이동
- 선택한 옵션 및 Detail 페이지의 아이템 정보들을 Cart에 카드로 나타내기
- Cart 페이지에서 아이템 타이틀 선택 시 다시 디테일 페이지로 이동
- 아이템 수량 선택 시 저장 및 금액 반영
- 아이템 카드의 remove 버튼 클릭 시 Cart 항목에서 삭제
- Cart 항목 추가 시 Header의 장바구니 버튼에 숫자 카운팅

#### radio 버튼 커스텀

- html (jsx 요소)

```jsx
<S.RadioBox>
  <S.Radio
    type="radio"
    id="Checkout"
    name="payment"
    value="Checkout"
    checked={payment === "Checkout"}
    onChange={handlePayment}
  />
  <S.RadioCheckMark htmlFor="Checkout" />
  <S.RadioLabel>Checkout</S.RadioLabel>
</S.RadioBox>
```

- css

```jsx
export const RadioBox = styled.div`
  /* Positioning */
  position: relative;

  /* Display & Box Model */
  display: block;
  margin-bottom: 12px;

  /* Font */
  font-size: 22px;
  font-weight: bold;

  /* Other */
  cursor: pointer;
`;

export const RadioCheckMark = styled.label`
  /* Positioning */
  position: absolute;
  top: 0;
  left: 0;

  /* Display & Box Model */
  width: 25px;
  height: 25px;
  border: 2px solid ${COLORS.subFont};
  border-radius: 50%;

  /* Color */
  background-color: ${COLORS.white};

  /* Other */
  cursor: pointer;
  &::after {
    content: "";
    /* Positioning */
    position: absolute;
    top: 7px;
    left: 7px;

    /* Display & Box Model */
    display: block;
    width: 11px;
    height: 11px;
    border-radius: 50%;

    /* Color */
    background-color: ${COLORS.white};

    /* Other */
    z-index: 2;
  }
`;

export const Radio = styled.input`
  /* Positioning */
  position: absolute;

  /* Display & Box Model */
  height: 0;
  width: 0;

  /* Other */
  opacity: 0;
  cursor: pointer;

  &:checked ~ ${RadioCheckMark} {
    border: none;
    background-color: ${COLORS.darkGray};
  }
`;

export const RadioLabel = styled.span`
  /* Display & Box Model */
  margin-left: 30px;
`;
```

간단히 구조는

```
div(RadioBox) > label(RadioCheckMark), input(Radio), span(RadioLabel)
```

이러한 구조이다. div는 라디오 한 항목을 나타내고,

그 하위에 실제 value를 받는 input이 있다.(이 input은 안보이게 할 것)

그리고 그 사라지는 input의 스타일링과 click event를 대신할 label이 있어야 하겠고, 항목의 제목이 필요하므로 span 또한 필요하다.

그래서 label(커스텀 디자인 한 동그라미)을 클릭하면 input의 radio button이 클릭되게끔 하여 동작하게 한다.

### context api 사용

redux만 사용해봤지 다른 전역 관리 라이브러리나 api를 사용해본 적은 없는데, context api를 사용하기로 했어서 best practice를 조금 찾아보았다.

일단 context api는 크게 3가지로 분류된다.

Context(store 같은), Provider, Consumer

1. Context 생성
   가장 먼저 생성해야 할 것은 Context이다.

   ```jsx
   export const CartContext = createContext<{
     cartItems: CartItemProps[];
   }>({ cartItems: [] });
   ```

   createContext라는 api로 context를 생성한다. 이 context에는 Provider라는 컴포넌트가 있다.

2. Provider 생성 및 배치

   Provider는 가장 상위 컴포넌트에 배치하고, children으로 모든 데이터를 공유하고 싶은 컴포넌트들을 받는다.

   그 Provider를 template 형태로 만들어서 export 해주고 index나 app.tsx에서 쓸 준비를 한다.

   ```jsx
   import React, { createContext, useState } from 'react';
   import { CartItemProps } from '../types';

   export const CartContext = createContext<{
     cartItems: CartItemProps[];
   }>({ cartItems: [] });

   interface Props {
     children: JSX.Element | JSX.Element[];
   }

   const CartContextProvider = ({ children }: Props): JSX.Element => {
     const [cartItems, setCartItems] = useState<CartItemProps[]>([]);

     return (
       <CartContext.Provider
         value={{ cartItems }}
       >
         {children}
       </CartContext.Provider>
     );
   };

   export default CartContextProvider;
   ```

3. Provider 사용

   ```jsx
   <React.StrictMode>
     <CartContextProvider>
       <SWRConfig value={{ fetcher }}>
         <Router>
           <App />
         </Router>
       </SWRConfig>
     </CartContextProvider>
   </React.StrictMode>,
   ```

   이렇게 모든 하위 컴포넌트가 Provider의 children이 되도록 한다.

4. 전역 데이터의 관리

   전역 context에서 state를 관리해서 그 state를 구독하는 컴포넌트가 꺼내서 열람할 수 있도록 하는 것이다.

   state를 공유할 수도 있지만, 정의한 함수 또한 어디서든 쓸 수 있도록 할 수 있다.

   ```jsx
   const CartContextProvider = ({ children }: Props): JSX.Element => {
     const [cartItems, setCartItems] = useState<CartItemProps[]>([]);

     const addItemtoCart = (newItem: CartItemProps) =>
       setCartItems(prev => [...prev, newItem]);

     const removeItemFromCart = (id: number) =>
       setCartItems(prev => prev.filter(el => el.id !== id));

     const changeQuantities = (id: number, quantity: number) => {
       const newCartItems = cartItems.map(item =>
         item.id === id ? { ...item, quantity } : item
       );
       setCartItems(newCartItems);
     };

     return (
       <CartContext.Provider
         value={{ cartItems }}
       >
         {children}
       </CartContext.Provider>
     );
   };
   ```

   그래서 cartItems (cart에 저장한 항목들의 배열)을 어디서나 쓸 수 있도록 하고,

   어느 곳에서는 데이터의 변경을 일으켜야 하기 때문에 context에서 전역 state를 변경해주는 함수를 작성한 다음 그 함수를 쓰도록 한다.

   추가, 변경, 삭제가 있을 것이므로 함수가 3개 존재할 것이다.

5. 구독 컴포넌트에서 데이터 사용 및 변경

   일단 useContext를 이용해서 item을 가져온다.

   ```jsx
   function PaymentSection() {
     const { cartItems, someFunc } = useContext(CartContext);

     // do something
     someFunc();

     return <S.Container>{cartItems.someKey}</S.Container>;
   }
   ```

   carItems에 있는 항목들을 가공 또는 계산해서 나타내준다.

### 기타 할일

PR 피드백 수정

- grid flex로 수정 (잘 안됨)

장바구니 페이지 UI o

장바구니 기능 o

<hr>
