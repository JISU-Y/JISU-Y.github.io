---
layout: single
title: "[Course / TIL] Wanted Pre Onboarding FE Course 1주차 - 팀 과제 2"
categories: Course
tag: [Course, TIL, Wanted, PreOnboarding, bundling]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Course

**Wanted Pre Onboarding FE Course**

## study

## 팀 프로젝트

### 회의

#### 기술 스택

- tailwind
- React
- 라이브러리
  - react-sortable (Drag & Drop) ⇒ 미정

#### 기능 명세 확인

#### 컴포넌트 나누기

- `<Input />` - `placeholder`
- `<StackedList />` - `width`, `height` 전달 받아 인라인 스타일로 적용
- `<ListItem />` - `children` 전달받기
- `<Button />` - 스타일만 필요
- `<Toggle />` - `on`, `setOn`, `onCallback`, `offCallback`
- `<Radio />` - 라디오 이쁜거 있으면 제공, 없으면 쌩으로 쓰기
- `<Popup />` - 소메뉴

#### 컨벤션 정하기

복잡하면 오히려 생산성이 떨어지고 지키기 힘들어질 것 같다고 판단했다.

따라서, 간결하게 템플릿을 만들었다.

**커밋 메시지 규칙**

```jsx
Feat: 작업 내용
```

| 유형            | 설명                                |
| --------------- | ----------------------------------- |
| Feat            | 새로운 기능 추가                    |
| Add             | 파일추가(이미지, 아이콘, 폰트...)   |
| Fix             | 버그 수정                           |
| Docs            | 문서 수정                           |
| Style           | 스타일(CSS, 퍼블, UI적인 요소) 수정 |
| Comment         | 주석 추가, 수정, 삭제               |
| Chore           | 빌드업 업무, 패키지 매니저 수정     |
| Refactor        | 수정                                |
| Rename          | 변수명, 함수명, 파일명, 폴더명 변경 |
| Remove          | 변수, 함수, 기능, 파일, 폴더 삭제   |
| !BREAKINGCHANGE | 큰 api 변경, 큰 구조적인 변경       |
| !HOTFIX         | 급한 치명적인 버그 수정             |
| Deploy          | 배포                                |

**Lint 규칙**

- prettier 사용

**PR 형식**

- PR 제목

```jsx
Feat: 작업 내용
Refactor: 작업 내용
```

- PR 본문

```jsx
### 반영 브랜치
feature/toggle -> dev

### 작업 사항 상세
로그인 시, 구글 소셜 로그인 기능을 추가했습니다.
```

### 아이템 항목 이동 구현 (컨테이너 간 이동)

멘붕의 현장이었지만, 팀원 분의 도움으로 끝까지 구현할 수 있었다.

#### 아이템 이동

1. state 선언 및 관리

state는 app(가장 최상위)에서 관리한다.

먼저, items(모든 아이템)을 디폴트로 leftItems에 넣어놓는다.
rightItems는 빈 배열로 둔다.

클릭되었을 때 선택된 아이템들의 배열도 만들어 놓는다.

중요한 점은 left 와 right를 나누는 것이 중요하다.

=> 왼쪽 컨테이너에 있는 아이템을 클릭해놓고, 오른쪽 컨테이너에 있는 아이템을 클릭했을 때 두 군데 모두 선택이 되어서는 안되기 때문에 나누어 둔다.

```jsx
const items = emojiMenus;

const initialSeleted = {
  left: [],
  right: [],
};

const [leftItems, setLeftItems] = useState(items);
const [rightItems, setRightItems] = useState([]);
const [selectedItems, setSelectedItems] = useState(initialSeleted);
```

2. 한 개씩만 이동

기본적으로 하나를 선택해서 이동 버튼을 클릭하면 다른 쪽으로 옮겨가도록 한다.

이 때, 왼쪽에서 선택하는 함수와 오른쪽에서 선택하는 함수를 구분해준다.

```jsx
// moveItems.js
const handleLeftSelect = (e, id) => {
  setSelectedItems((prev) => ({
    left: prev.left.map(({ id }) => id).includes(id) // 이미 있던 아이템이라면
      ? prev.left.filter(({ id: itemId }) => itemId !== id) // selected에서 삭제
      : leftItems.filter(({ id: itemId }) => itemId === id), // 아니면 그냥 selected에 추가
    right: [], // 오른쪽을 비워줌으로써 왼쪽/오른쪽 컨테이너에서 중복으로 클릭 안되도록
  }));
};

// App.js
<Selector
  list={leftItems}
  selectedItems={selectedItems.left}
  handleSelect={handleLeftSelect}
/>;
```

이제 리스트 컨테이너 컴포넌트에서 아이템이 클릭될 때마다 해당 아이템의 id를 보내준다.

e는 추후 ctrl, shift 키 때문에 둠

```jsx
<StackedList>
  {list?.map((item) => {
    return (
      <ListItem
        key={item.id}
        id={item.id}
        onClick={(e) => handleSelect(e, item.id)}
        className={`block hover:bg-gray-100 select-none
        // 모든 아이템을 돌다가 선택된 아이템과 같은 id가 있으면 css 적용
        ${
          selectedItems.map(({ id }) => id).includes(item.id)
            ? "bg-gray-100"
            : "bg-white"
        }
        `}
      >
        <div className="p-3 cursor-pointer">
          <span>{item.emoji}</span>
          <span>{item.name}</span>
        </div>
      </ListItem>
    );
  })}
</StackedList>
```

오른쪽 버튼 클릭 시, 왼쪽에 있던 아이템들에서 선택된 아이템들 빼준다. (id 이용)

오른쪽에서는 선택된 아이템들을 추가해준다.

끝나면 선택된 아이템 모두 날리기

```jsx
const moveToRight = ({ all = false }) => {
  // 선택된 아이템들의 id 배열
  const selectedItemIds = all
    ? leftItems.map(({ id }) => id)
    : selectedItems.left.map(({ id }) => id);

  // leftItems에서 선택된 아이템 제거
  setLeftItems((prev) =>
    prev.filter((item) => !selectedItemIds.includes(item.id))
  );
  // rightItems에 선택된 아이템 추가
  setRightItems((prev) =>
    all ? [...prev, ...leftItems] : [...prev, ...selectedItems.left]
  );
  // 초기화
  setSelectedItems(initialSeleted);
};
```

이렇게 하면 하나씩 이동은 쉽게 구현할 수 있다. (왼쪽, 오른쪽 변수만 다르고 로직 모두 동일)

키포인트는 선택된 아이템 배열을 left와 right로 나누는 것이 중요하다. 그래야 왼쪽 컨테이너 오른쪽 컨테이너에서 선택 상태가 중첩되지 않을 수 있다.

#### ctrl, shift 다중 선택 및 이동

아이템 클릭 시 받아온 이벤트에서

e.ctrlKey, e.shiftKey로 키보드 이벤트를 확인할 수 있다 (boolean으로 나옴)

```jsx
// shift 클릭
// 선택된 아이템들, 왼쪽/오른쪽 아이템들, 지금 클릭된 id받아서 범위 내의 아이템들 반환
const shiftClick = (selectedItems, originItems, id) => {
  // start 초기값이 0이기 때문에 처음부터 shift 누르고 아이템 선택하면 맨 처음 아이템부터 모두 선택
  let [start, end] = [0, 0];

  // start는 selectedItems에 가장 처음에 있던 아이템의 id로 index를 구함
  if (selectedItems[0]) {
    const startId = originItems.filter(
      ({ id }) => id === selectedItems[0].id
    )[0].id;
    // start 아이템의 인덱스 위치
    start = originItems.map(({ id }) => id === startId).indexOf(true);
  }
  // end는 지금 클릭된 id로 index를 구함
  end = originItems.map(({ id: itemId }) => itemId === id).indexOf(true);

  // start 부터 end 까지 선택된 것으로 설정
  const selects = originItems.filter(
    (item, index) => index >= start && index <= end
  );

  return selects;
};
```

```jsx
const handleLeftSelect = (e, id) => {
  // shift가 눌린채로 이벤트가 발생했다면
  if (e.shiftKey) {
    // shift 클릭 관리하는 함수로 넘어감
    // 클릭 범위의 아이템들을 반환해줌
    const selects = shiftClick(selectedItems.left, leftItems, id);

    setSelectedItems((prev) => ({
      left: prev.left.map(({ id }) => id).includes(id)
        ? prev.left.filter(({ id: itemId }) => itemId !== id)
        : selects, // start~end의 아이템
      right: [],
    }));
  } else {
    // ctrl 다중 & 하나씩 옮기기
    setSelectedItems((prev) => ({
      left: prev.left.map(({ id }) => id).includes(id) // 이미 있던 아이템이라면
        ? prev.left.filter(({ id: itemId }) => itemId !== id) // selected에서 삭제
        : e.ctrlKey || e.metaKey // ctrl, cmd 키 확인
        ? [
            ...prev.left,
            ...leftItems.filter(({ id: itemId }) => itemId === id), // ctrl/cmd 키 누른거면 축적
          ]
        : leftItems.filter(({ id: itemId }) => itemId === id), // 아니면 그냥 selected에 추가
      right: [],
    }));
  }
};
```

처음에 너무 막막하고, 멘붕이었다. 정말로 자괴감이 들었다.

하지만, 머리를 식히고 다시 정신차리고 보니 또 어느샌가 되긴 했다.

차근차근, divide & conquer가 매우매우 중요하다고 생각한다.

## 회고 (TIL)

**2022.02.24 Daily 회고**

✏오늘 한 일

- 팀 과제 전 회의
- 팀 과제 수행
  - 아이템 옮기기 기능
  - 아이템 다중 선택 기능

⁉느낀 점

다른 과제를 시작한다. 저번 과제에서 부족한 점이었던 컨벤션을 미리 회의를 통해서 정해두어서 앞으로도 부족한 점을 회고를 통해 개선해나가면 좋겠다.

실력에 비해 어려운 부분을 맡은 것 같다. 하지만 오늘 안에 끝낼 생각이다. 팀원분들이 있기 때문에 걱정이 크게 되지는 않는다.

제대로 하고 있는 것이 맞는 건지에 대해 팀원분들께 여쭤보고 진행하니 진행 방향도 잘 잡히는 것 같아서 좋다.

🎃현재 나의 상태

오늘 안에 끝낼 생각이기 때문에 정신차려야 할 것 같다.

구현 자체도 어려운 기능이 아직도 많다는 것이 슬프지만 어차피 평생 가도 모든 것을 통달하는 것은 아니기에 위안을 조금 삼아본다.

<hr>
