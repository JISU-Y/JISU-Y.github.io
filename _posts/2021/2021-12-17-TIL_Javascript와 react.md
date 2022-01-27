---
layout: single
title: "[TIL] Javascript, React - 전역 변수 문제점, 클래스 컴포넌트"
categories: Javascript
tag: [TIL, Javascript, React, 전역 변수, 클래스 컴포넌트]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## Javascript 개념

### 전역 변수의 문제점

#### 변수의 생명 주기

- 지역 변수의 생명 주기
  > 지역 변수는 함수가 호출되면 생성되고 함수가 종료되면 소멸되는 라이프 사이클을 가진다. (보통 지역 변수의 생명 주기 === 함수의 생명 주기)

변수는 자신이 등록된 스코프가 소멸될 때까지 유효한데, 스코프나 변수를 누구도 참조하지 않을 때 가비지 콜렉터에 의해 메모리 풀에 반환된다.
누가 참조하고 있다면 해제되지 않고 남아있다.

- 전역 변수의 생명 주기
  > var 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 된다.
      전역 변수의 생명 주기 === 전역 객체의 생명 주기

브라우저 환경에서는 window가 전역 객체인데, 전역 객체 window는 웹 페이지를 닫기 전까지 유효하다.
따라서, var 또한 웹페이지를 닫을 때 까지 유효하다.

#### 전역 변수의 문제점

- 암묵적 결합(implicit coupling)
  > 전역 변수는 코드 어디서든 참조하고 할당하는 것을 허용하겠다는 것.

유효 범위가 큰 변수는 코드의 가독성을 해치고, 위험성도 높아진다.

- 긴 생명 주기
  생명 주기가 길기 때문에, 메모리 리소스도 오랜 기간 소비된다.
  따라서 var로 선언한 변수는 중복 선언을 허용하기 때문에 의도치 않은 재할당이 발생할 수 있다.

- 스코프 체인 상 종점에 존재
  전역 변수의 검색 속도가 느리다. (큰 차이는 없음)
  스코프 체인 상 가장 상위에 있기 때문에, 참조하려면 가장 마지막에 검색된다.

- 네임 스페이스 오염
  자바스크립트는 파일이 분리되어 있다고 해도 하나의 전역 스코프를 공유한다.
  따라서, 전역 변수를 사용할 경우 다른 파일에 영향으로 예상치 못한 결과를 가져올 수 있다.

#### 전역 변수 사용 억제 방법

<aside>
💡 변수의 스코프는 좁을수록 좋다.
</aside>

- 즉시 실행 함수
  함수 정의와 동시에 호출되는 **즉시 실행 함수는 단 한번만 호출**된다.
  모든 코드를 즉시 실행 함수로 감싸면 모든 변수가 **즉시 실행 함수의 지역 변수**가 된다.
  전역 변수를 생성하지 않아 라이브러리에서 많이 사용된다.

- 네임스페이스 객체
  전역에 네임스페이스 역할을 담당할 객체를 생성하여 전역 변수처럼 사용할 변수를 이 **네임스페이스 객체의 프로퍼티로 추가**하는 식으로 사용한다.
  식별자 충돌은 방지할 수 있으나 객체 자체가 전역 변수에 할당되므로 아주 좋지는 않다.
- 모듈 패턴
  클래스를 모방하여 관련이 있는 변수와 함수를 모아 즉시 실행 함수로 감싸 하나의 모듈을 만드는 것이다.
  클로저를 기반으로 동작하며 전역 변수의 억제는 물론 캡슐화까지 구현할 수 있게 된다.

<aside>
💡 캡슐화: 객체의 상태를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는 것.
→ 특정 프로퍼티나 메서드를 감출 목적으로도 사용하여 정보 은닉(information hiding)이라고 한다.
</aside>

```jsx
var Counter = (function () {
  // private 변수
  var num = 0;

  // 외부로 공개할 데이터나 메서드를 프로퍼티로 추가한 객체를 반환한다.
  return {
    increase() {
      return ++num;
    },
    decrease() {
      return --num;
    },
  };
})();

// private 변수는 외부로 노출되지 않는다. (정보 은닉)
console.log(Counter.num); // undefined

console.log(Counter.increase()); // 1
console.log(Counter.increase()); // 2
console.log(Counter.decrease()); // 1
console.log(Counter.decrease()); // 0
```

- ES6 모듈

  ES6 모듈은 파일 자체의 **독자적인 모듈 스코프를 제공**한다.
  <br>이 모듈을 사용하면 **전역 변수를 사용할 수 없다**.
  <br>script 태그의 type=”module” 어트리뷰트를 추가하면 자바스크립트 파일은 모듈로서 동작한다.
  <br>크로스 브라우징이 아직 안되기 때문에 ES6보다는 Webpack 등의 모듈 번들러를 사용한다.

## React

### React 레슨

#### ShouldComponentUpdate

1.  문제 발견
    ![](https://images.velog.io/images/jisu129/post/3d0365ec-4bde-4aa5-952f-fb9452dbcb3f/1%EC%B4%88%20%EB%A7%88%EB%8B%A4%20render.JPG)
    기본 상태에서는 1초 마다 렌더링이 된다.
    time을 불러오는 함수 getCurrentTime 함수가 DidUpdate 메서드 안에 있으므로 1초마다 렌더 -> DidUpdate가 반복된다.

        그런데, pause를 누르거나 modal의 close 버튼을 누르면 render가 계속해서 늘어난다.

2.  원인 파악
    ![](https://images.velog.io/images/jisu129/post/f1ed2406-ae04-44ab-bd2b-8901700e3254/%EB%B2%84%ED%8A%BC%20%EB%88%84%EB%A5%BC%20%EB%95%8C%20%EB%A7%88%EB%8B%A4%20%EB%A0%8C%EB%8D%94%EA%B0%80%20%EB%88%84%EC%A0%81%EB%90%98%EC%96%B4%20%EB%B0%9C%EC%83%9D.JPG)
    처음에는 Modal이 떠서 그런가... 생각했다.
    근데 그게 아니라,
    Modal을 띄우기 위한 shoulShowModal state가 변경이 되면서 리렌더링이 일어나고, DidUpdate 메서드가 다시 불려져서 1초 사이에 또 시간을 가져오도록 되어 있었기 때문에 렌더링이 pause/modal close 를 누를 때 마다 기하급수적으로 증가하는 것이었다.

        그러니까, 원래는 1초마다 렌더링되고 Update 메서드가 불려지면 되는 것인데
        거기서 button이 눌려져서 state가 변경이 되면서 1초 사이에 렌더링을 또 진행해버렸던 것이다. 1초 사이에 시간을 받아오는 것이기 때문에 눈에는 업데이트가 되는 지 몰랐던 것이다.

        그래서 함수형 component에서 useEffect 처럼 특정 state가 변화할 때만 렌더링되게 하는 그런 메서드가 없을까 찾아본 결과 메서드를 알게 되었다.!

- ShouldComponentUpdate 메서드
  : props 혹은 state를 변경 시, boolean 값을 반환하여 rerendering 여부를 결정하는 메서드

3.  문제 해결
    ![](https://images.velog.io/images/jisu129/post/3bede011-818d-4b05-8495-da9cf41381cb/shouldComponentUpdate.JPG)
    ShouldComponentUpdate 메서드를 이용해서 shouldShowModal이 바뀔 때는 렌더링 하지 못하도록 하고 싶었다.

        이 메서드를 어떻게 쓰는 고 찾아봤더니
        nextProps와 nextState를 인수로 현재 this.props 혹은 this.state와 비교하여 렌더링을 할지 말지 결정하면 되는 것이었다.

        그래서 이 컴포넌트에서는 props가 없으므로 nextState만 받아서 그 shouldShowModal과
        this.State의 shouldShowModal이 같지 않을 때는 false가 나와서 렌더링되지 않도록 했더니 해결되었다!
        (이 두개가 같을 때는 시간이 돌아가야 하기 때문에 렌더링이 되어야 한다!)
