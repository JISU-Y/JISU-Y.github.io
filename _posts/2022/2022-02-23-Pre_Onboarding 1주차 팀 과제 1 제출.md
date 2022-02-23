---
layout: single
title: "[Course / TIL] Wanted Pre Onboarding FE Course 1주차 - study: bundling, 팀 과제"
categories: Course
tag: [Course, TIL, Wanted, PreOnboarding, bundling, 면접 대비]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Course

**Wanted Pre Onboarding FE Course**

## study

## 팀 프로젝트

### detail 페이지

#### 컴포넌트

- header: 뒤로가기, 페이지 제목, 끄기 버튼(기능X)

- 게시글 컴포넌트 배열로 나타나는 거

- 게시글 컴포넌트
  - 게시글 헤더 : 아이디, 게시 날짜, 신고하기
  - 이미지
  - description
    - 좋아요 버튼(기능O), 카운트, 공유 버튼(기능O), 하트(기능X)
    - 리뷰 별(기능X)
    - 옵션
    - 내용
    - 태그
    - 배송
  - 상품 리스트
  - 댓글 리스트
  - 댓글 form

#### Git 충돌

처음 master와 dev 브랜치를 생성해두고 dev에서 branch를 따서 작업하려고 했으나

몇분은 master에서 딴 브랜치에서 작업하다가 master로 머지했고, 몇 분은 dev에서 따서 작업했다.

이러다 보니 master와 dev가 완전히 다른 커밋 로그를 갖게 되었고,

dev에서 딴 브랜치를 dev에 머지하고, dev에서 master로 머지하려고 했을 때 진행되지 않았다.

그래서 dev를 pull을 받고, master도 pull을 받은 후 dev 브랜치에서 rebase master를 해서 커밋을 연결시키고자 했다.

rebase master를 하고 충돌을 해결한 후 rebase --continue로 끝내려고 했으나

terminal에 vim 에디터가 뜨면서 진행되지 않았다.

찾아보니 일단 git commit 이름은 바꾸고, 만약 순서를 바꿀 커밋이 있거나 삭제할 커밋이 있다면 설정한 후에

Esc 누른 후 : wq를 하면 저장 후 종료가 된다.

여기서는 커밋 자체를 수정할 필요는 없고 머지만 하면 되었기에 일단 :w(저장) :q(종료) 를 통해 vim editor를 종료하였고, 정상적으로 merge(rebase)가 된 것을 확인할 수 있었다.

이제 master와 dev가 통합되어 dev에서 master로 머지가 가능해졌다.

[참고] https://wormwlrm.github.io/2020/09/03/Git-rebase-with-interactive-option.html

#### 소셜 공유

페이스북, 트위터, 카카오톡 공유 기능을 구현해야한다.

페이스북/트위터 와 카카오톡 공유 기능은 다르게 구현한다!

**페이스북 / 트위터 공유**

라이브러리를 간단하게 이용할 수 있다.

react-share라는 라이브러리인데, 업데이트가 되지는 않는다고 한다.

- 라이브러리를 선택한 이유는

      1. 페이스북, 트위터 등등 많은 공유 기능을 지원하고 있어 매우 편리
      2. 이미지도 포함하고 있고, 사이즈, border-radius등 커스텀이 되어 번거롭지 않다.
      3. 공유 기능이 메인이 아니었기 때문에 생산성을 위해 라이브러리를 사용했다.

  위와 같다.

사용하니 카카오톡과 비교하여 5개 가량 구현 시간이 차이 나는 듯 하다. (카카오톡을 애초에 처음 해봤기에)

- 사용법

```jsx
// 사용하고자 하는 공유 버튼 및 icon import
import { FacebookShareButton, FacebookIcon, TwitterIcon, TwitterShareButton } from "react-share"

// 바로 사용
return (
  <FacebookShareButton url={currentUrl} onClick={(e) => e.stopPropagation()}>
    <FacebookIcon size={80} borderRadius={10} />
  </FacebookShareButton>
  <TwitterShareButton url={currentUrl} onClick={(e) => e.stopPropagation()}>
    <TwitterIcon size={80} borderRadius={10} />
  </TwitterShareButton>
)
```

공유하고자 하는 url을 url 어트리뷰트에 넣고, icon의 size, borderRadius 등 여러 스타일을 적용해준다.

onClick에는 모달 Container가 클릭되었을 때 modal이 off되도록 구현되어 있는데, 버튼을 눌러도 이벤트 버블링 때문에 바로 modal이 off되는 현상이 있었다.

따라서 버튼이 눌렸을 때는 모달이 꺼지는 것을 방지하기 위해 stopPropagation 메서드를 사용했다.

**카카오톡 공유**

카카오톡 공유는 페이스북/트위터 보다는 조금 복잡하다.

1. kakao developers 내 애플리케이션 추가

추가하면 여러 키가 주어지는데, **Javascript 키**를 가지고 이용하면 된다.

2. script including

카카오에서 제공하는 카카오 script를 포함시켜야 한다.

React에서는 여러 방법이 있을 수 있다.

    1. 애초에 HTML에 포함시키기
    2. Helmet 컴포넌트(라이브러리) 사용
    3. 필요한 컴포넌트가 마운트되었을 때 포함시키기

html 파일을 건드리고 싶지 않았고, 자주 쓰지도 않는 script를 미리 포함시키는 것보다 사용하고자 할 때만 포함시키는 것이 좋을 것 같아 3번으로 진행하려 했다.

```jsx
useEffect(() => {
  const script = document.createElement("script");
  script.src = "https://developers.kakao.com/sdk/js/kakao.js";
  script.async = true;

  document.body.appendChild(script);

  return () => {
    document.body.removeChild(script);
  };
}, []);
```

script를 사용하게 될 컴포넌트의 상위에서 하여야 하위 컴포넌트들 여러 군데에서 사용할 수 있게된다. 따라서 Share(내가 카카오톡 버튼 놓은 곳) 컴포넌트보다 상위 컴포넌트(ProductCard)에서 실행하게 했다.

하지만!

그런데 이렇게 하니 script가 아이템의 개수 만큼 script가 생성되었다.

그래서 script를 public에 두는 것으로 했다...

sdk script

```html
<script src="https://developers.kakao.com/sdk/js/kakao.js"></script>
```

3. 버튼 사용

다른 공유와는 다르게 메타 태그처럼 미리 나타나게 하는 정보들이 필요해 꽤 장황한 객체를 추가해야한다.

```jsx
// 카카오 공유 버튼 생성
const createKakaoButton = (e) => {
  e.stopPropagation();
  if (window.Kakao) {
    const kakao = window.Kakao;

    if (!kakao.isInitialized()) {
      kakao.init("34cbf0f18f5987e6e9641ad7f4bc6106"); // env 생성 필요
    }

    kakao.Link.sendDefault({
      objectType: "feed",
      content: {
        title: "balaan",
        description: `${review.title}`,
        imageUrl: `${review.image}`,
        link: {
          mobileWebUrl: currentUrl,
          webUrl: currentUrl,
        },
      },
      buttons: [
        {
          title: "리뷰 보기",
          link: {
            mobileWebUrl: currentUrl,
            webUrl: currentUrl,
          },
        },
      ],
    });
  }
};

return (
  <KakaoButton onClick={createKakaoButton}>
    <KakaoImg
      src="https://static.balaan.co.kr/mobile/img/share/btn_share_kt.png"
      alt="kakaotalk"
    />
  </KakaoButton>
);
```

보통 createDefaultButton을 많이 사용하는 듯 하다.

하지만 여기서는 한 아이템마다 각각의 링크를 보내야하기 때문에, onClick이 될 때 공유가 되도록 하고 싶었다.

따라서 sendDefault 메서드를 사용하여 카카오 세팅을 했고, onClick에 담았다.

[참고]
https://ellismin.com/2020/09/share-kakao/
https://webruden.tistory.com/354

### 회의

폴더 구조 협의 - components에 특정 페이지만 쓰는 컴포넌트 common 컴포넌트 따로 안나누어도 되는지 => 하면서 나눔

이슈 안 적어도 되는지 => 안 적음

폴더 네이밍 -> 파일은 파스칼, 변수는 카멜로 되어있지만 폴더명은 나와있지 않아서 통일된 것인지 => 폴더명 카멜

좋아요 기능 redux 구현하는 것인지 => 로컬 스토리지 (개인이 눌렀는지 안눌렀는지만 판단)

나머지 이미지같은 것들은 public에 mock data로 넣어두면 되는지 => 알아서

머지 5시

처음 접속 시 데이터 아무것도 없음 (initial data를 깔아줘야 할지 아니면 없는 상태로 할지) => initial dummy data 만듦

components 아래 바로 파일 있으면 공용, 아니면 각 페이지 폴더 아래에

#### 완성

첫 과제가 무사히(?) 잘 끝났다.

팀원분들 믿음이 확 간다! 모두 구현을 너무 잘 하셔서 서로 오지랖 피우지 않고 각자 구현할 사항 구현하고 만들어 나가면 된다. 좋다!

대신 조금 더 소통을 많이 했으면 좋겠다ㅠㅠ

처음에 컨벤션도 제대로 안 잡고 가서 할 일을 두 번한 느낌은 좀 별로였다.

그렇지만 이제부터 개선해나가기로 했으니까 걱정없다!

(어차피 시간이 많지 않아서 빠르게 개발해야 하기도 하고..)

이번에는 딱히 한게 없다ㅋㅋㅋ..

그래도 git 문제 해결해서 기분은 좋았다. (이 정도면 1인분!?)

## 회고 (TIL)

**2022.02.23 Daily 회고**

✏오늘 한 일

- 팀 과제 수행
  - 소셜 공유 기능 (카카오톡, 페이스북, 트위터)
  - git 충돌 해결 (rebase)
- 번들링 스터디

⁉느낀 점

서로 다른 커밋 히스토리를 가지고 있는 브랜치를 통합하는 과정에서 rebase에 대해 더 깊게 알아봤다.
원래 git rebase를 잘 안다고 생각했으나 생각되로 되지 않으니 매우 어려웠다.
하지만, 잘 찾아보니 해결할 수 있어서 좋았다.

팀 과제 중 주어진 역할에서의 구현은 다 했지만, 이후 같이 머지 해보면서 추가로 더 보아야 한다.

이번 팀 과제를 진행하면서 소통의 중요성을 더욱 절실히 깨닫는다. 또한, 개발 전 컨벤션 통합, 프로젝트 관리도 중요하다는 것을 새삼 깨닫고 있다.

확실히 모든 팀원분들이 구현을 잘 하셔서 구현 자체에는 문제가 없을 것 같다. 그래서 소통을 조금 더 늘려가고 싶다.

🎃현재 나의 상태

아직도 구현 못해본 기능들이 아주 많이 있다. 그래서 이번 처럼 다음 과제할 때에도 해보지 못한 기능들을 접해보고 싶다.

팀원들에게 적극적으로 물어보고 진행 사항을 공유해야겠다.

<hr>
