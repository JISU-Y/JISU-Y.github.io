---
layout: single
title: "[Course / TIL] Wanted Pre Onboarding FE Course 3주차 - 팀 과제 5 제출"
categories: Course
tag: [Course, TIL, Wanted, PreOnboarding, 웹 접근성, a11y]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Course

**Wanted Pre Onboarding FE Course**

## 팀 프로젝트

### 구현

#### 웹 접근성 구현

웹 접근성을 고려하여 모달 제작

**웹 접근성이란?**

웹 접근성(web accessibility/a11y)은 장애/비장애인이 모두 웹사이트를 이용할 수 있도록 하는 방식이다.

스크린 리더기를 통해 시각장애인은 웹 사이트를 읽게 되는 데 이 때 웹 접근성을 고려해서 img 태그에 alt를 의미 있게 작성하는 등 처리해줄 수 있다.

비단 시간 장애인뿐만 아니라 모든 장애인들 그리고 비장애인들(노인, 작은 화면 사용자, 마우스 없는 사용자 등등) 또한 아울러서 고려한다.

**구현 사항**

```
tab / Shift tab으로 클릭 가능 요소들 focus
Enter시 클릭 동작
Esc로 모달 끄기
모달 켜지면 모달 내에서만 tab이 돌아다니도록 하기
모달 끄면 마지막으로 focus 되어 있던 요소 그대로 focus
```

- tab / Shift tab으로 클릭 가능 요소들 focus

  특정 태그들은 기본적으로 tab 시 focus가 된다. (a, button, input, textarea, select 등등)

  그러나 div, span 같은 태그만으로 내용을 알 수 없는 태그들은 기본적으로 tab focus에서 제외된다.

  따라서 div 요소를 tab에 포함시키고 싶을 때는 요소 어트리뷰트에 tabIndex를 0으로 준다.

  - tabIndex 속성

    - tab focus 순서를 임의로 조정
    - a, area, button, input, object, select, textarea 태그에 적용 가능
    - -32767 ~ 32767 사이 값 입력 가능

  - tabindex = "1"

    - 문서 안에서 가장 먼저 초점을 받게 한다.
    - 자연스런 Markup 순서를 거스른다.

  - tabindex = "0"
    - div, span 등에 focus를 줄 수 있다.
    - tabindex=0 은 Markup 순서대로 인식한다.

  * tabindex = "-1"
    - focus가 가능한 요소도 focus되지 않도록 만들어 준다.

  [출처] https://heewon26.tistory.com/208

- Enter시 클릭 동작

  focus 된 이후 동작

  button이나 a 태그는 focus 이후 enter를 누를 시 자동으로 click 이벤트와 같이 동작을 하지만, 나머지 태그들은 tabIndex를 통해 focus가 되도록 설정하더라도 enter를 누르면 저절로 click 이벤트가 발생하지는 않는다.

  그럼 어떻게 해야할까?

  나는 이렇게 했다.

  1. 요소에 onKeyPress에 이벤트 핸들러 함수 추가하기

  ```jsx
  const handleEnter = (e: React.KeyboardEvent<HTMLLIElement>) => {
    if (e.key === "Enter") {
      selectItem();
    }
  };

  return (
    <ListContainer onClick={selectItem} onKeyPress={(e) => handleEnter(e)}>
      <Name>{휴양림_명칭}</Name>
      <Address>{휴양림_주소}</Address>
      <Contact>{전화번호}</Contact>
      {data.memo && <Memo>{memo}</Memo>}
    </ListContainer>
  );
  ```

  div 태그에 javascript 없이 enter가 클릭 이벤트로 동작하도록 하는 속성은 없다고 한다.

  벌써부터 selectItem 이 함수를 두 번이나 써주어야 하고 함수도 한 개 늘어났다. 매우 보기 좋지 않은 듯 하다.

  2. button으로 감싸기

  ```jsx
  <ListContainer onClick={selectItem}>
    <button role={"listitem"}>
      <Name>{휴양림_명칭}</Name>
      <Address>{휴양림_주소}</Address>
      <Contact>{전화번호}</Contact>
      {data.memo && <Memo>{memo}</Memo>}
    </button>
  </ListContainer>
  ```

  클릭이 되고 싶은 요소 하위에 요소들을 button 태그로 감싸고, role 어트리뷰트를 주어 focus와 enter가 디폴트로 동작하도록 한다.

  이렇게 하면 매우 깔끔하게 focus와 enter 처리를 해줄 수 있다.

  **여기까지의 동작**

  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-03/08_01.gif)

- Esc로 모달 끄기

  ```jsx
  const escapeClose = useCallback(
    (e) => {
      if (e.key === "Escape") {
        setShowModal(false);
      }
    },
    [setShowModal]
  );

  useEffect(() => {
    // 생략
    document.documentElement.addEventListener("keydown", escapeClose);
    return () => {
      document.documentElement.removeEventListener("keydown", escapeClose);
    };
  }, [show, escapeClose]);
  ```

  document 자체에서 keydown event를 listen하여 키보드가 눌렸을 때 escapeClose가 동작하도록 한다.

  escapeClose 함수에는 키보드가 Escape일 때만 동작하도록 한다.

- 모달 켜지면 모달 내에서만 tab이 돌아다니도록 하기

  모달이 켜졌음에도 focus가 페이지에서 돌아다닌다면 매우 불편할 것이다.

  예를 들어, 리스트 페이지에서 모달을 띄운다고 하면 모달에 focus가 가려면 리스트에 있는 모든 요소들을 다 focus 지나야 하기 때문에 매우 불편하다.

  따라서 모달이 켜지자마자 모달 내에 있는 요소 중 특정 요소에 바로 focus가 되도록 하고, 모달이 떠 있을 때는 tab이 바깥으로 나가지 않도록 처리해야 한다.

  **모달 켜진 후 input 요소에 focus**

  내가 구현하려고 한 모달에는 input, button?, button 즉 2개 혹은 3개의 요소가 있었고 input에는 무조건 value를 적어야 하기 때문에 모달이 켜질 경우 input으로 바로 focus를 주면 사용자 입장에서 훨씬 편할 것이라고 생각했다.

  ```jsx
  useEffect(() => {
    if (show) {
      inputMemoRef.current?.focus();
    }
  }, [show]);
  ```

  **모달 내에서만 tab이 돌아다니도록**

  팀원의 도움을 받아서 구현했다.

  모달에서 보이지 않는 div를 맨 앞, 맨 뒤에 두어서 맨 뒤 div에 focus가 오면 실제로 focus를 준 처음 요소로 보내고, 맨 앞 div에 focus가 오면 실제 focus가 되어야 하는 가장 마지막 요소로 보내는 방식으로 구현했다.

  ```jsx
  const focusLastEl = useCallback(
    (e) => {
      if (e.target === firstFocusTrap.current) {
        if (lastFocusableEl) {
          (lastFocusableEl as HTMLElement)?.focus();
        }
      }
    },
    [firstFocusTrap, lastFocusableEl],
  );

  const focusFirstEl = useCallback(
    (e) => {
      if (e.target === lastFocusTrap.current) {
        inputMemoRef.current?.focus();
      }
    },
    [lastFocusTrap],
  );

  useEffect(() => {
    const focusHead = firstFocusTrap.current;
    const focusFoot = lastFocusTrap.current;
    if (focusHead) {
      focusHead.addEventListener('focusin', focusLastEl);
    }
    if (focusFoot) {
      focusFoot.addEventListener('focusin', focusFirstEl);
    }
    return () => {
      if (focusFoot) {
        focusFoot.removeEventListener('focusin', focusLastEl);
      }
      if (focusHead) {
        focusHead.removeEventListener('focusin', focusFirstEl);
      }
    };
  }, [show, focusFirstEl, focusLastEl]);
  ```

  useEffect 내 콜백 함수를 보면, 앞, 뒤의 fake div에 focusin event를 listening하다가 event가 발생하면 각 함수를 동작하도록 했다.

  따라서

  ```jsx
  const focusHead = firstFocusTrap.current;
  focusHead.addEventListener("focusin", focusLastEl);
  ```

  모달의 가장 첫번째 fake div(firstFocusTrap)가 focusin이 되면 모달에서 가장 마지막 요소(fake div 제외)에 focus가 되도록 한다.

  반대의 경우도 마찬가지이다.

  추가로, 마지막 요소를 구하는 방법은 다음과 같다.

  ```jsx
  const setLastFocus = useCallback(() => {
    if (!contentRef.current) return;

    const focusableEls = Array.from(
      contentRef.current.querySelectorAll(
        'a[href], button, input, textarea, select, details, [tabindex]:not([tabindex="-1"])'
      )
    ).filter(
      (el) => !el.hasAttribute("disabled") && !el.getAttribute("aria-hidden")
    );

    const lastEl = focusableEls[focusableEls.length - 1];

    if (lastEl) {
      setLastFocusableEl(lastEl);
    }
  }, [contentRef]);
  ```

  focusableEls로 모달의 요소 중 focus가 가능한 요소들을 뽑아낸다. (여기서도 fake div 제외)

  그 중에서 가장 뒤에 있는 노드를 취해 lastFocusableEl에 set 해준다.

  이 함수는 모달이 켜졌을 때 동작하게 하면 된다.

  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-03/08_02.gif)

- 모달 끄면 마지막으로 focus 되어 있던 요소 그대로 focus

  모달에서 할 일을 마치고 껐을 때는 tab이 어디로 가야할까?

  처음으로 돌아가면 내가 마지막으로 썼던 요소를 찾아 또 스크롤을 해야하는 경우가 생기기 때문에 모달 처리가 끝나서 모달이 사라지게 되면 원래 페이지에서 마지막으로 focus 되어 있던 요소에 focus를 다시 옮겨 주어야 한다.

  ```jsx
  const [prevActiveEl, setPrevActiveEl] = useState<Element | null>();

  useEffect(() => {
    if (show && document.activeElement?.tagName === 'BUTTON') {
      setPrevActiveEl(document.activeElement);
      setNextOfPrevActiveEl(document.activeElement.nextElementSibling);
      inputMemoRef.current?.focus();
      setLastFocus();
    }

    /*
    생략
    */
  }, [show, escapeClose, setLastFocus, setScrollLock]);

  const closeModalAndFocusPrev = useCallback(() => {
    setShowModal(false);
    if (prevActiveEl) {
      (prevActiveEl as HTMLElement)?.focus();
    } else {
      (nextOfPrevActiveEl as HTMLElement)?.focus();
    }
  }, [setShowModal, prevActiveEl, nextOfPrevActiveEl]);
  ```

  기본적으로 show가 true가 되면(모달이 켜지면) 모달의 input 요소에 focus가 되도록 한다. 하지만 이전에 원래 페이지에서 focus였던 요소를 따로 저장해두어야 한다.

  ```jsx
  setPrevActiveEl(document.activeElement);
  ```

  prevActiveEl를 이용해서 모달이 꺼질 때, prevActiveEl에 focus가 되도록 하면 된다.

  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-03/08_03.gif)

#### input 렌더링 최적화

input에 무언가를 적으면 다른 컴포넌트들도 렌더링이 된다.

이를 개선하기 위해 memo를 사용했다.

```jsx
const MemoButton = React.memo(MainButton);
const MemoList = React.memo(MainList);
```

## 회고 (TIL)

**2022.03.08 Daily 회고**

✏오늘 한 일

- 팀 과제 수행 및 제출
  - 모달 웹 접근성 개선

⁉느낀 점

웹 접근성 고려하는 것도 매우 어렵다는 것을 알았다. 이것도 일이다.

aria 속성은 좀 더 공부를 해야할 것 같다.

팀원이 있어서 항상 다행.

🎃현재 나의 상태

피곤하지만 할 수 있다고 최면 거는 중

<hr>
