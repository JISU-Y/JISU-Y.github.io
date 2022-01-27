---
layout: single
title: "[TIL] React - setState / CSS - vertical align center"
categories: React
tag: [TIL, React, useState, CSS]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## React

### setState

#### 비동기 setState

어제 알아본 setState 비동기를 예상치 못하게 만나서 해결하게 되었다!

```jsx
const [postTodo, setPostTodo] = useState({
  todoText: props.isTodoEdit ? props.editTodo.todoText : "",
  todoDone: false,
  tempId: null,
});
```

처음 postTodo useState를 이용하여 state 선언을 할 때, props.editTodo.todoText를 set 해주었는데, postTodo를 사용할 때에는 props.editTodo.todoText 값이 할당되어 있지 않아서 왜 그런가 했었다.

setState는 비동기이기 때문에 값이 set이 될 때 나머지 코드도 이미 다 실행이 되기 때문에 render시에 업데이트 된 값(props.editTodo.todoText)을 볼 수 없었던 것이다.

그래서 나는 useEffect를 사용해서 state set을 해주기로 하였다.

useEffect를 이용해서 render가 되었을 때, props.editTodo의 값이 바뀌면 set을 하도록 한 것이다.

```jsx
useEffect(() => {
  setPostTodo({
    todoText: props.isTodoEdit ? props.editTodo.todoText : "",
    todoDone: false,
    tempId: null,
  });
}, [props.editTodo]);
```

그러나, 이 코드 또한 문제점이 있다.
<br>useEffect의 dependency에 추가가 권고되는 변수들이 props.editTodo 이 외에도 props.isTodoEdit가 있는데, 이를 추가하면 too much render 가 된다..

#### functional update

그래서 알아보니 추가 dependency를 추가하지 않고, 함수형 업데이트를 사용해서 추가 변수들을 쓰지 않아도 되었다.

```jsx
useEffect(() => {
  // dependency 추가하지 않고, functional update 사용
  setPostTodo((todo) => {
    return { ...todo, todoText: props.editTodo.todoText };
  });
}, [props.editTodo]);
```

이처럼 setState를 전 상태를 전달해서 그를 업데이트 하는 식으로 함수형으로 set을 하면 추가 dependency가 필요하지 않게 된다.

가독성 또한 좋으며 이전 state의 값을 이용하는 것이므로 좀 더 안전할 수 있겠다.

### CSS 관련

#### 수직 가운데 정렬

세로 가운데 정렬, 수직 가운데 정렬은 css를 사용하면 항상 어떻게 해야할지 바로바로 생각이 안나는 것 같다.

그래서 찾아보기도 했고, 찾아본 방법과 내가 나름 생각해낸 방법을 적어두어야 겠다.

    1. line-height  이용
    2. padding 이용
    3. display table 이용
    4. pseudo selector 이용

1. line-height 쓰기

   가장 간단한 방법이고 직관적인 방법이라고 생각한다.

   나는 거의 line-height를 즐겨쓰게 되는 것 같다

   하지만, 이것은 정적인 웹에서만 해당이 된다는 것.

   만약 가운데에 두고 싶은 text가 개행이 되거나 요소의 height가 달라지면 이 방법도 통하지 않게 된다.

   line-height를 특정 px로 고정시켜서 사용하는 것이기 때문에 이 설정 px과 달라지면 정중앙으로 오지 않는다.<br>(text의 높이를 건드리는 것이므로 완전 text가 완전 넘어가버린다.)

2. padding 이용

   그래서 text의 양이 일정하지 않다면, height값을 빼고 padding을 넣으면 가운데로 보낼 수 있다.

3. display table 이용

   찾아보면서 배운 것인데, 부모 요소에 display에 table을 지정하고, 가운데로 보내고 싶은 자식 요소들의 display를 table-cell로 설정하여 수직 가운데 정렬이 가능해진다.

   [참고 및 출처] https://oursmalljoy.com/css-%EA%B8%80%EC%9E%90-%EC%88%98%EC%A7%81-%EA%B0%80%EC%9A%B4%EB%8D%B0-%EC%A0%95%EB%A0%AC-text-vertical-align/

4. pseudo selector 이용

   나는 이렇게 구현해보았다.

   사실 위의 상황과 많이 다를 수는 있으나 이런 방법으로도 구현할 수 있을 것 같다.<br>가상 선택자를 이용해서 absolute로 위치를 지정하고 content에 text를 쓰는 방법을 사용했다.

   ```css
   .row {
     display: flex;
     justify-content: space-between;
     align-items: center;
     margin: 4px auto;
     color: #fff;
     background: linear-gradient(
       90deg,
       rgba(255, 118, 20, 1) 0%,
       rgba(255, 84, 17, 1) 100%
     );
     padding: 16px;
     border-radius: 5px;
     width: 90%;
   }
   .complete {
     text-decoration: line-through;
     position: relative;
   }
   .complete::after {
     content: "COMPLETE!";
     background-color: transparent;
     position: absolute;
     top: 50%;
     left: 50%;
     transform: translate(-50%, -50%);
     letter-spacing: 10px;
     font-size: 17px;
     font-weight: bold;
     color: black;
     opacity: 1;
     pointer-events: none;
   }
   .complete::before {
     content: "";
     background-color: rgba(255, 255, 255, 0.5);
     position: absolute;
     top: 0;
     left: 0;
     width: 100%;
     height: 100%;
     pointer-events: none;
   }
   ```

   그러니까, 가운데로 보낼 요소를 psuedo selector로 생성하는 것이다.

   그 안의 content에 넣고 싶은 text를 넣고, pseudo selector를 absolute로 두어서 옮기면 된다!

   ```css
   element {
     top: 50%;
     left: 50%;
     transform: translate(-50%, -50%);
   }
   ```

   이렇게 가운데로 보내면 된다. (수직 뿐만 아니라 좌우도 가운데)

   나는 배경 색은 따로 가져가고 싶었기 때문에 pseudo selector를 하나 더 추가할 수 밖에 없었다.

   따라서 이러한 방법을 통해서 내가 수직 가운데로 가기로 원하는 요소 (text)를 높이에 상관없이 가운데 정렬할 수 있었다.
