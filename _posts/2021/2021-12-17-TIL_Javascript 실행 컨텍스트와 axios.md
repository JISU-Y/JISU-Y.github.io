---
layout: single
title: "[TIL] Javascript - 실행 컨텍스트와 axios"
categories: web
tag: [TIL, Javascript, 원시값, 객체]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## Javascript 개념

### 실행 컨텍스트

#### 소스 코드

1. **소스 코드란?**

   > ECMAScript 사양에서 구분되는 **4가지 타입**.
   > ECMAScript code, 실행 가능한 코드(excutable code)라고 하며 **실행 컨텍스트를 생성**한다.

   | 소스코드 타입            | 설명                                                                                  |
   | ------------------------ | ------------------------------------------------------------------------------------- |
   | 전역 코드(global code)   | 전역에 존재하는 소스코드. 전역에 정의된 함수, 클래스 등 내부 코드는 미포함.           |
   | 함수 코드(function code) | 함수 내부에 존재하는 소스코드. 함수 내부에 중첩된 함수, 클래스 등 내부 코드는 미포함. |
   | eval 코드 (eval code)    | 빌트인 전역 함수인 eval 함수에 인수로 전달되어 실행되는 소스코드.                     |
   | 모듈 코드 (module code)  | 모듈 내부에 존재하는 소스코드. 모듈 내부의 함수, 클래스 등 내부 코드는 미포함.        |

   소스코드의 타입에 따라 실행 컨텍스트를 생성하는 과정과 관리 내용이 달라진다.

2. **소스코드의 평가와 실행**

   자바스크립트 엔진은 소스코드를 "**소스코드의 평가**"와 "**소스코드의 실행**" 두 과정으로 나누어 처리한다.

   - 평가 과정
     실행 컨텍스트를 생성
     변수, 함수 등의 **선언문만 먼저 실행**하여 생성된 변수나 함수 식별자를 키로 두어 렉시컬 환경의 환경 레코드(실행 컨텍스트가 관리하는 스코프)에 등록한다.
   - 실행 과정
     평가 과정이 끝나면 선언문을 제외한 소스코드가 순차적으로 실행(**런타임 시작**)
     실행하면서 **필요한 정보**(변수, 함수의 참조)를 렉시컬 환경에서 검색하여 **취득**한다.
     소스코드의 **실행 결과는 다시 실행 컨텍스트**가 관리하는 스코프에 등록된다.
   - 평가와 실행 과정의 예
     ```jsx
     var x;
     x = 1;
     ```
     1. 평가 과정에서 변수 선언문 var x를 먼저 실행한다.
     2. 변수 식별자 x는 실행 컨텍스트가 관리하는 스코프에 등록되고, undefined로 초기화된다.
     3. 평가 과정이 끝난 후 실행 과정 시작된다.
     4. 시작하고 나서 변수 선언문 var x는 평가 과정에서 이미 실행되었으므로 다음 코드가 실행된다.
     5. 변수 할당문 x = 1이 실행된다.
     6. 변수 x에 값을 할당하기 전, x 변수가 선언된 변수인지 확인한다(실행 컨텍스트가 관리하는 스코프에 등록이 되어있는지).
     7. 등록이 되어 있다면 값을 할당한다.
     8. 할당 결과를 실행 컨텍스트에 등록하여 관리한다.

- 실행 컨텍스트

  > 소스코드를 실행하는 데에 필요한 환경을 제공, 코드의 실행 결과를 관리하는 영역/메커니즘
  > 모든 코드가 실행 컨텍스트를 통해 실행되고 관리된다.

  자바스크립트 동작 원리를 담고 있는 핵심 개념
  <br>→ 자바스크립트가 변수와 값을 관리하는 방식, 호이스팅 발생 이유, 클로저, 이벤트 루프와 비동기 처리 동작 방식을 이해할 수 있는 기본 개념

  1. 실행 컨텍스트의 역할

     코드를 실행하려면 스코프, 식별자, 코드 실행 순서 등의 관리가 필요하다.
     실행 컨텍스트는 **소스 코드를 실행하는 데 필요한 환경을 제공**하고 코드의 **실행 결과를 관리**하는 영역이다.

     식별자**(변수, 함수, 클래스 등)를 등록**하고 관리하는 **스코프와 코드 실행 순서 관리**를 구현한 내부 메커니즘으로, 모든 코드는 실행 컨텍스트를 통해 실행되고 관리된다.

     이 중 식별자와 스코프는 실행 컨텍스트의 **렉시컬 환경**으로 관리하고, 코드 실행 순서는 **실행 컨텍스트 스택**으로 관리한다.

  2. 실행 컨텍스트 스택

     ```jsx
     const x = 1;

     function foo() {
       const y = 2;

       function bar() {
         const z = 3;
         console.log(x + y + z);
       }
       bar();
     }

     foo();
     ```

     자바스크립트 엔진은 먼저 전역 코드를 평가하여 전역 실행 컨텍스트를 생성한다.
     진행하다가 함수가 호출되면 함수 코드를 평가하여 함수 실행 컨텍스트를 생성한다.

     이 때 **생성된 실행 컨텍스트는 스택 자료구조로 관리**되는데, 이를 실행 컨텍스트 스택이라고 한다.

     스택이 추가되고 제거되면서 코드가 실행된다.

     - 실행 과정 <br>

       _1) 전역 코드의 평가와 실행_

       전역 코드 평가
       <br>→ 전역 실행 컨텍스트 생성, 실행 컨텍스트 스택에 푸시
       <br>→ 전역 변수 x와 전역 함수 foo는 전역 실행 컨텍스트에 등록
       <br>→ 평가가 끝나 실행이 시작
       <br>→ 전역 변수 x 값 할당, 전역 함수 foo 호출

       _2) foo 함수 코드의 평가와 실행_

       <br>→ 전역 함수 foo가 호출
       <br>→ 전역 코드 실행은 일시 중단되며 코드의 제어권이 foo 함수 내부로 이동
       <br>→ foo 함수 내부 코드 평가
       <br>→ foo 함수 실행 컨텍스트를 생성, 실행 컨텍스트 스택에 푸시
       <br>→ 지역 변수 y와 중첩 함수 bar가 foo 함수 실행 컨텍스트에 등록
       <br>→ 평가가 끝나 실행이 시작
       <br>→ 지역 변수 y에 값이 할당되고, bar 함수 호출

       _3) bar 함수 코드의 평가와 실행_
       <br>→ 중첩 함수 bar가 호출
       <br>→ foo 함수 코드의 실행은 일시 중단되며 코드의 제어권이 bar 함수 내부로 이동
       <br>→ bar 함수 내부 코드 평가
       <br>→ 실행 컨텍스트를 생성, 실행 컨텍스트 스택에 푸시
       <br>→ 지역 변수 z와 bar 함수 실행 컨텍스트에 등록
       <br>→ 평가가 끝나 실행이 시작
       <br>→ z에 값 할당
       <br>→ console.log 메서드 호출(실행 컨텍스트 생성, 스택 푸시, 종료 모두 거침)
       <br>→ bar 함수는 종료

       _4) foo 함수 코드로 복귀_
       <br>→ bar 함수가 종료되면 코드의 제어권은 다시 foo 함수로 이동
       <br>→ bar 함수 실행 컨텍스트를 실행 컨텍스트 스택에서 제거
       <br>→ foo 함수 또한 실행할 코드가 없으면 종료

       _5) 전역 코드로 복귀_
       <br>→ foo 함수가 종료되면 코드의 제어권은 다시 전역 코드로 이동
       <br>→ foo 함수 실행 컨텍스트를 실행 컨텍스트 스택에서 제거
       → 전역 코드에서도 실행할 코드가 없으면 종료, 실행 컨텍스트 스택에서 제거

         <aside style='background:gold; padding:10px 20px;' >
         💡 실행 컨텍스트 <br>
       <strong>스택의 최상위에 존재하는 실행 컨텍스트는
         언제나 현재 실행 중</strong>인 코드의 실행 컨텍스트이다.
         
         가장 최상위 실행 컨텍스트를 <strong>실행 중인 실행 컨텍스트(running execution context)</strong>라고 한다.
         
         </aside>


  3. 렉시컬 환경

     > 렉시컬 환경(Lexical Environment)은 식별자(변수 등)와 식별자에 바인딩된 값,
     > 상위 스코프에 대한 참조를 기록하는 자료구조이다.

     렉시컬 환경은 스코프를 구분하여 식별자를 등록하고 관리하는 저장소 역할
     렉시컬 환경은 키와 값을 갖는 객체 형태의 스코프를 생성한다. (식별자를 키로 바인딩된 값을 값으로 설정하여 관리한다.)

     ```jsx
     var a;
     let num = 1;
     ```

     ```
     렉시컬 환경

     a : undefined
     num : 1
     ```

     렉시컬 환경의 구성

     - 환경 레코드 (Environment Record)
       스코프에 포함된 식별자를 등록하고 값을 관리하는 저장소
       소스코드 타입에 따라 관리하는 내용이 다르다.
     - 외부 렉시컬 환경에 대한 참조 (Outer Lexical Environment Reference)
       상위 스코프(외부 렉시컬 환경)를 가리킨다.
       해당 실행 컨텍스트를 생성한 소스코드를 포함하는 상위 코드의 렉시컬 환경을 말하며 이 참조를 통해 **스코프 체인을 구현**한다.
         <aside style='background:gold; padding:10px 20px;' >
         💡 스코프 체인? <br>
         자바스크립트 엔진은 식별자를 찾을 때 일단 자신이 속한 스코프에서 찾고,
         그 스코프에 식별자가 없으면 상위 스코프에서 다시 찾아 나간다.
         이를 <strong>스코프 체인</strong>이라고 하며 스코프가 중첩되어있는 모든 상황에서 발생한다.
         
         </aside>


     ```jsx
     let a = 1;

     function f(num) {
       return num + a;
     }

     f(5);
     ```

     f 함수 스코프/렉시컬 환경에서는 a가 없으므로 외부 렉시컬 환경 참조를 통해 전역 코드에 있는 a의 값을 참조할 수 있는 것이다.

- 실행 컨텍스트와 블록 레벨 스코프

<aside style='background:gold; padding:10px 20px;'>
    💡 var : 함수 코드 블록만 지역 스코프로 인정 (함수 레벨 스코프) <br>
  let, const : 모든 코드 블록(함수, if 문, for 문, while 문, try/catch 문 등)을 지역 스코프로 인정 (블록 레벨 스코프)
</aside>

- let, const 변수의 렉시컬 환경의 변화

      ```jsx
        let x = 1

        if(true) {
            let x = 10
            console.log(x)
        }

        console.log(x)
      ```

  이 전역 실행 컨텍스트에서 if 문의 코드 블록이 실행되면 if 문의 코드 블록을 위한 **블록 렉시컬 환경**(선언적 환경 레코드가 있는)을 생성하여 전역 렉시컬 환경을 대체한다.
  이때 생성된 블록 렉시컬 환경에서 외부 렉시컬 환경 참조는 이 렉시컬 환경을 생성했던 렉시컬 환경을 가리킨다.
  ![](https://images.velog.io/images/jisu129/post/7dd5a367-6038-4f20-9925-d435a9e76e10/%EC%8B%A4%ED%96%89%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8%EC%99%80%20%EB%B8%94%EB%A1%9D%EB%A0%88%EB%B2%A8%20%EC%8A%A4%EC%BD%94%ED%94%841.png)

if 문의 코드가 종료되면 전역 실행 컨텍스트는 다시 이전의 렉시컬 환경으로 되돌린다.

![](https://images.velog.io/images/jisu129/post/fe6a243e-4edc-4551-986d-f8fcd9ed47fa/%EC%8B%A4%ED%96%89%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8%EC%99%80%20%EB%B8%94%EB%A1%9D%EB%A0%88%EB%B2%A8%20%EC%8A%A4%EC%BD%94%ED%94%84.png)

[사진 참조]
https://velog.io/@niyu/23%EC%9E%A5-%EC%8B%A4%ED%96%89-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8

## React 관련

### axios 라이브러리

#### axios interceptor

내 프로젝트에 적용했던 기능인데, 너무 클론하다가 그냥 아 몰라 하고 지나간 내용이기도 하고, 나중 인터뷰에서 axios를 사용한 이유와 interceptor를 사용한 이유를 물어볼 확률이 높기 때문에 이참에 공부하려고 한다.!!

일단 axios는 정리해둔 적이 있다.

- axios 라이브러리
  javascript ajax 라이브러리로 크로스 브라우징 기능과 직관적인 사용, promise 기반 등 사용성이 좋아 특히나 React, Vue에서 많이 사용하는 비동기 통신 라이브러리이다.

- axios interciptor
  axios 라이브러리에 포함되어 있는 기능이다.

기본적으로 요청 전, 응답 전에 통신을 가로채서 무언가를 수행해줄 수 있는 기능이다.
보통 로그인 token을 가지고 authorization 구현 시에 많이 사용된다.
또한, 오류 발생 시 동작해야할 것들을 미리 정의해둘 수 있다.

\- 요청 시

```jsx
axios.interceptors.request.use(
  (config) => {
    // Request 보내기 전 동작
    return config;
  },
  (error) => {
    // Request 수행 중 오류 발생 시 동작
    return Promise.reject(error);
  }
);
```

\- 응답 시

```jsx
axios.interceptors.response.use(
  (res) => {
    return res;
  },
  (error) => {
    return Promise.reject(error);
  }
);
```

[참고 및 출처]
https://wiki.jjagu.com/?p=376
https://hydev.tistory.com/3

- 내가 짠 코드

```jsx
// backend에서 token을 verify할 수 있도록 해줌
API.interceptors.request.use((req) => {
  // login 된 상태라면
  if (localStorage.getItem("profile")) {
    req.headers.authorization = `Bearer ${
      JSON.parse(localStorage.getItem("profile")).token
    }`; // local Storage에서 login 된 user의 token을 가져옴
  }

  return req;
});
```

이 interceptor는 request가 발생할 때마다 중간에서 동작한다.
따라서, login token 처리를 하기 딱 좋은 것이다.

- 내 프로젝트의 로그인 로직 flow

1.  ![](https://images.velog.io/images/jisu129/post/aca00a0b-17be-4301-87a9-395b82894836/signin1.JPG)
    프론트 단에서 사용자의 id와 pw를 받는다(server에 전달용)

2.  ![](https://images.velog.io/images/jisu129/post/833fac6c-764e-4a78-959c-19f7c246b5a6/signin2.JPG)
    받은 id와 pw(formData)를 action dispatching 한다

3.  ![](https://images.velog.io/images/jisu129/post/45d5f2a3-c3ec-4a09-b53d-e423f0cf07a8/signin-action.JPG)
    redux thunk middleware에서 비동기 처리 해준다
    formData를 가지고 signin api를 통해 로그인 해달라는 전달.

데이터가 오면 reducer로
화면은 홈으로 변경

4.  ![](https://images.velog.io/images/jisu129/post/9192dc21-a6d7-4a94-849b-5c45b6efbd83/signin-api.JPG)
    interceptors를 이용해서 요청 전 profile에 값이 있는지 확인.
    있으면 인가를 바로 해준다.

이 로직에서 여러 에러 처리도 가능할 것. profile이 없을 때 로그인 재요청 alert 이라든지..

5.  ![](https://images.velog.io/images/jisu129/post/e1424d31-2bc0-421a-9f92-9d4a1ce6e799/signin-backend-create-token.JPG)
    백엔드 단에서 받은 id와 pw로 유저 중복 처리, 비밀번호 확인 처리하고,
    모두 성공하면 token을 생성한다. (1시간 짜리)
    그러고 response를 보낸다.

6.  ![](https://images.velog.io/images/jisu129/post/9c288195-9530-4ff3-a194-08db9099db23/signin-reducer.JPG)
    middleware에서 처리해준 비동기 처리에서 받아온 데이터(=token)를
    localStorage에 저장한다. (profile을 key로)

7.  ![](https://images.velog.io/images/jisu129/post/ff59f648-0543-417c-ac5f-ef37cd4f6af3/token%20expire%20check.JPG)
    로그인 된 경우 홈으로 이동하는데, 로그인 되었을 때 변경되는 컴포넌트가 Navbar이다.
    Navbar에서 토큰의 expire를 체크해준다.

token을 decoding 해주고(라이브러리 사용) expire 확인하여 만료되었으면 logout 실행

만료가 안된거라면 이 컴포넌트에서의 state(user state)를 profile에서 가져온 token으로 설정할 수 있다.

### Git

#### git 버전 되돌렸다가 돌아오기

- 이전 커밋으로 되돌리고 싶을 때
  git checkout head~1 (숫자는 얼마나 전 단계로 돌릴 것인지)
  혹은
  git log를 통해 원하는 버전/커밋을 찾은 후
  해시코드 앞의 4자리 복사하여
  git checkout [해시코드 4자리 이상]

- 되돌렸다가 다시 현재 버전/커밋으로 돌아올때
  git checkout [branch name]

- 아예 되돌리고 싶을 때
  git revert head~1
  git revert [해시코드 4자리 이상]

### React 관련

#### React 프로젝트 manifest.json

- manifest.json

CRA로 react app을 만들면 public 폴더에 여러 파일들이 존재한다.

그 중 manifest.json 파일은
앱에 대한 정보를 담고 있는 json 파일이다.
배경색, 앱의 이름, icon 등

=> PWA 로 만들기 위해서는 필수적으로 포함되어야 하는 요소 중 하나다.

logo는 사용하지 않으므로 삭제하려고 한다.
![](https://images.velog.io/images/jisu129/post/5c049a81-9596-4e30-963a-412e45b875e3/public%20manifest.JPG)

일단 favicon은 추후 교체할 예정이므로 삭제는 하지 않았다.

![](https://images.velog.io/images/jisu129/post/f667d31c-4a05-483c-b677-7148015f6fdb/public%20manifest%20logo%20removed.JPG)

항목별 설명

- short_name: 사용자 홈 화면에서 아이콘 이름으로 사용
- name: 웹앱 설치 배너에 사용
- icons: 홈 화면에 추가할때 사용할 이미지
- start_url: 웹앱 실행시 시작되는 URL 주소
- display: 디스플레이 유형(fullscreen, standalone, browser 중 설정)
- theme_color: 상단 툴바의 색상
- background_color: 스플래시 화면 배경 색상
- orientation: 특정 방향을 강제로 지정(landscape, portrait 중 설정)

[참고 및 출처]
https://altenull.github.io/2018/03/09/%EC%9B%B9%EC%95%B1-%EB%A7%A4%EB%8B%88%ED%8E%98%EC%8A%A4%ED%8A%B8-%EC%84%9C%EB%B9%84%EC%8A%A4%EC%9B%8C%EC%BB%A4-Web-App-Manifest-Service-Worker/
