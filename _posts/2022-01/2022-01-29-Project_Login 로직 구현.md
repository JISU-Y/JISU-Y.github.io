---
layout: single
title: "[Project] 유저/번역가 로그인 로직 구현 및 파일 업/다운로드 구현"
categories: Project
tag: [Project, React, 팀 프로젝트]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**팀 프로젝트 Page**

## React 팀 프로젝트

### 페이지 구현

#### 로그인 로직 구현

우리 서비스에는 로그인이 두 종류가 있다.

client(번역 요청자)로 로그인하기, Translator(번역가)로 로그인하기

두개 모두 카카오 로그인을 사용하기로 했다.

로그인을 하나만 구현한다면 바로 구현이 가능할 것 같지만, 카카오 로그인 자체도 꽤 알아볼 것이 많고, 그것을 두개로 나누어서 해야하다보니 로그인 로직을 시각화 해야할 필요가 있다고 느꼈다.

**기본적인 카카오 로그인 로직**

먼저, 카카오 로그인 시 프론트와 백 역활에 대해서 알아보겠다.

1. 프론트

   카카오 로그인 창을 띄워준다.
   유저에게 아이디/비밀번호를 입력하게 해서 카카오에게 인가 코드를 요청한다.
   카카오에게 인가 코드가 오면 그것을 백엔드에 전달한다.
   백엔드에서 처리해서 가입 관련 데이터를 받아서 처리한다.

2. 백엔드

   프론트에게서 받은 인가 코드로 카카오에 토큰을 요청한다.
   카카오에게서 토큰이 오면 그 토큰과 로직을 기반으로 auth(번역가인지, 클라이언트인지)에 대한 정보를 프론트에게 넘겨준다.

- 카카오 로그인 구현

처음 카카오 로그인에 대해서 아무것도 모를 때, 백엔드에서 알아서 인가 코드 요청하고, 토큰 요청해서 프론트에서는 토큰만 받으면 되는 줄 알았다.

하지만, 막상 구현하려고 보니 프론트에서 카카오 로그인 창을 띄우고 로그인했는데 다음과 같은 에러가 발생했다.

```
Access to XMLHttpRequest at 'https://accounts.kakao.com/login?continue=https%3A%2F%2Fkauth.kakao.com%2Foauth%2Fauthorize%3Fresponse_type%3Dcode%26redirect_uri%3Dhttp%253A%252F%252F52.79.79.67%253A3000%252Fapi%252Fauth%252Fkakao%252Fcallback%26client_id%{클라이언트ID}' (redirected from 'http://52.79.79.67:3000/api/auth/kakao/client%27) from origin 'http://localhost:3000/' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

CORS error가 뜨며 내 로컬호스트 3000에서 띄울라고 한 'http://52.79.79.67:3000/api/auth/kakao/client%27' 이 주소를 리다이렉트 할 수 없다는 것이었다.

지금 생각해보니까 너무 당연한데, 할 때는 도대체 뭐가 문젠지 모른다는 것이 함정.

일단 여기서 잘 못한 것은 리다이렉트 하는 주소도 프론트엔드여야 한다. 지금 52.79.79.67은 백엔드 주소이다. 프론트에서 해야한다는 것을 모르고 백엔드 주소를 갔다가 리다이렉트 하려고 했으니 당연히 안된다. (~~왜이렇게 바보같지~~)

따라서,

카카오 인증 설정?에 redirect url 설정을 프론트엔드 주소로 해두고 그 설정한 url로 리다이렉트를 시도해야 한다.

클릭하면 리다이렉트 되어야 할 주소

```
https://kauth.kakao.com/oauth/authorize?client_id={클라이언트ID}&redirect_uri=http://localhost:3000&response_type=code
```

일단 클라이언트 id와 리다이렉트 될 주소를 입력해준다.
테스트 해보려고 일단 main 화면으로 redirect되도록 주소를 입력했다.

```jsx
<a
  href={
    "https://kauth.kakao.com/oauth/authorize?client_id={클라이언트ID}&redirect_uri=http://localhost:3000&response_type=code"
  }
>
  <Button content="카카오톡으로 로그인" bgColor="#F9E000" color="#000" />
</a>
```

이제 카카오톡 로그인 버튼 (사실 a 태그)를 클릭하면 로그인 창이 뜨고, 아이디/비밀번호를 입력해서 http://localhost:3000 이 주소로 인가 코드와 함께 리다이렉트 해준다.

인가 코드는 데이터로 전달되는 것이 아니고 주소 맨 뒤에 code={인가코드} 이렇게 온다.

그래서 url을 통해서 인가코드를 가져오는 작업을 해주어야 한다.

```jsx
let params = new URL(document.location.toString()).searchParams;
let code = params.get("code");
```

현재 url에서 code 파라미터의 값만 가져오는 것이다.

이제 인가 코드를 프론트에서 가지고 있기 때문에 이것을 그대로 백엔드에 전달해주어야 한다.

우리의 백엔드 api는 api/auth/kakao/client/${code} 이므로 코드를 담아서 보내준다. (translator도 마찬가지)

그러면 백엔드에서 이 인가코드를 가지고 카카오에 다시 토큰을 요청하고 그 토큰을 받고, auth 정보도 구해서 다시 프론트로 넘겨주게 된다.

그러면 우리가 결과적으로 받는 데이터는

```json
{
  "token": "엄청긴토큰",
  "auth": "client"
}
```

가 된다.

이것을 가지고 localStorage 혹은 sessionStorage, cookie 등에 저장을 하고 자동 로그인 또한 구현할 수 있게 된다.

**유저 로그인 로직**

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/29_1.jpg)

- 카카오톡으로 로그인 클릭

- 리다이렉트 주소: localhost:3000/oauth/callback/kakao/client
  (추후 배포 주소도 설정해야함)

- 요청(백엔드 api): api/auth/kakao/client/${code}

- 응답: { token: 'token', auth: 'client' }

- 이동할 페이지:

  - 처음 가입한 경우: /client/main

    ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/29_2.jpg)

  - 번역가로 등록되어 있는데 클라이언트로 가입한 경우: /

    번역가로 로그인해주세요.라는 모달과 함께 메인으로 이동

**번역가 로그인 로직**

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/29_1.jpg)

- 번역가로 로그인 클릭

- 리다이렉트 주소: localhost:3000/oauth/callback/kakao/translator
  (추후 배포 주소도 설정해야함)

- 요청(백엔드 api): api/auth/kakao/translator/${code}

- 응답: { token: 'token', auth: 'translator', isPofile: true/false }

- 이동할 페이지:

  - 처음 가입한 경우: /

    승인을 기다려달라는 모달과 함께 메인으로 이동

  - 가입되었지만 approve가 안된 경우: /

    메인 페이지(로그인 페이지)로 이동하여서 알람 띄우기 (아직 승인되지 않은 번역가 입니다. => 모달창 구현 예정)

  - 승인이 된 번역가이나, 번역가 정보를 제출하지 않은 경우: /translator/signup

    ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/29_3.jpg)

  - 승인이 된 번역가이고, 번역가 정보를 제출한 경우: /translator/list

    ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-01/29_4.jpg)

    ```jsx
    const loginTranslator = async () => {
      let params = new URL(document.location.toString()).searchParams;
      let code = params.get("code"); // 인가코드 받는 부분

      console.log(code);

      try {
        const { data } = await apis.translatorLogin(code);
        console.log(data);

        // sessionStorage에 auth랑 token 저장
        sessionStorage.setItem("token", data.token);
        sessionStorage.setItem("auth", data.auth);
        // sessionStorage 에 저장? 다른데 못 가도록 처리 필요

        if (data.isProfile) {
          // 가입 폼 제출했으면
          navigate("/translator/list");
        } else {
          // 아직 가입 폼이 없으면
          navigate("/translator/signup");
        }
        alert("로그인 성공.");
      } catch (error) {
        const errorMessage = error.response.data.message;
        navigate("/");
        // 가입 폼 제출 안했을 시
        if (errorMessage === "번역가 승인 후 로그인 가능합니다.") {
          alert("번역가 승인을 기다려주세요."); // 추후 모달 처리
        } else if (errorMessage === "번역가로 로그인할 수 없습니다.") {
          // 번역가가 아닐 경우
          alert("번역가가 아닙니다. 카카오톡으로 로그인하기를 눌러주세요."); // 추후 모달 처리
        }
      }
    };
    ```

#### 파일 다운로드 구현

보통 이미지를 올리고 다운로드를 받아서 그 url로 이미지를 보여준다.

하지만, 번역가 가입 폼에서는 프로필 선택을 할 때마다 이미지를 올리면 리소스가 많이 들게 되므로 제출하기 할 때 이미지를 storage에 올리는 것으로 했다.

그래서 이미지 url을 바로 다운받아서 보여줄 수가 없었다.

그래서 이미지 미리보기를 구현했다.

저번에 했던 로직과 동일하다. 위치가 바뀌었을 뿐

```jsx
// image preview
useEffect(() => {
  setPreview(
    <img
      src={file ? URL.createObjectURL(file) : defaultProfile}
      alt="profileImg"
      width="64px"
      height="64px"
    />
  );
  return () => {};
}, [file]);
```

유저가 올리는 file 이 바뀔 때마다 preview라는 컴포넌트를 설정해준다.

file ? URL.createObjectURL(file) : defaultProfile

그래서 file이 있으면 미리보기 URL.createObjectURL(file)하고, file이 없는 경우에만 defaultProfile 로 보여주기로 한다.

그 다음 백엔드에 저장하는 이름을 정한다.

나중에 다운받거나 url로 바로 사용하기 쉽도록 백엔드에 저장할 때 이미지가 바로 열리는 url로 저장하고자 했다.

그런데, fileName만 가지고서는 download를 하지 않는 이상 full url을 받아올 수 없다.

그래서 찾아본 결과 링크가 일정 형식이 갖추어져있고, fileName만 가지고도 링크를 구현할 수 있었다.

```
firebase storage 경로:
https://firebasestorage.googleapis.com/v0/b/

bucket object
translation-talk-efa1a.appspot.com/o/

폴더 (profile/)
${folder}%2F

file 이름
${fileName}

?alt=media
```

firebase storage 경로와 bucket object 까지는 항상 같고, 폴더는 profile과 file만 있으므로 둘 중 하나로 선택하면 된다.

그 다음에는 fileName을 넣어주고, 마지막 ?alt=media 무조건 넣어주어야 파일 링크로 이동할 수 있다.

원래 firebase에서 DownloadURL하면 오는 링크에는 엑세스 토큰까지 있지만 그것은 없어도 링크는 제대로 동작한다.

```jsx
// TranslatorSignupForm
// fileName 변경되었을 때 formData.profileUrl fileName 넣기
useEffect(() => {
  if (fileName === "") return;
  // url로 만들어서 백엔드에 전달
  const profileUrl = getDownloadUrl("profile", fileName);
  setFormData({
    ...formData,
    profileUrl: profileUrl,
  });
}, [fileName]);

// firebase.js에 있는 함수
// url 받기
export const getDownloadUrl = (folder, fileName) => {
  if (folder !== "file" && folder !== "profile") return "wrong folder";
  return `https://firebasestorage.googleapis.com/v0/b/translation-talk-efa1a.appspot.com/o/${folder}%2F${fileName}?alt=media`;
};
```

fileName이 바뀔 때마다 즉, file이 바뀔 때마다

### git 사용

현재 로그인 페이지를 작업을 하고 있는데, 카카오 redirect url 설정을 다른 분이 해주셔야 해서 시간이 조금 더 필요할 것 같았다.

그런데 파일 업로드와 다운로드를 구현한 버전이 master로 올라왔기 때문에 그것을 가져와서 적용을 하려고 한다.

이 때 사용하는 것이 git rebase 이다.

#### git rebase

아직 구현이 덜 된 changes에 대해서 commit을 하는 것은 아니기 때문에 rebase를 통해서 새로운 버전을 내 로컬에 받아들이고, 내 changes를 새로운 버전에 갖다붙이는 것이다.

- 내 현재 branch: yjs/FeatDetailFix

- 새로운 버전이 올라온 branch: master

git checkout master

git pull origin master

git checkout yjs/FeatDetailFix

git rebase master

를 하면 간단하게 해결할 수 있다.

하지만, 문제는 새로운 버전(master)에 login.jsx 파일이 수정이 되었고, 내가 수정한 사항에도 login.jsx가 수정이 되어 있는 상태이기 때문에 git pull origin master 시

```
error: Your local changes to the following files would be overwritten by merge:
        src/pages/common/Login.jsx
Please commit your changes or stash them before you merge.
```

이러한 에러를 발견할 수 있다.

그렇다고 커밋을 할 수는 없고, 가져오고는 싶을 때 어떻게 해야할까?

일단 내 changes 들을 들고?있다가 새로운 버전 적용할 거 하고 싶은 느낌이 강했다.

이때 사용하는 것이 보통 stash이다.

#### git stash

stash는 내 변경사항들을 안전한 곳을 대피시켜두었다가. (stash stack)

pull을 받을 수 있는 상황을 만들어 주는 것이다.

그래서 먼저 master branch (새로운 버전이 있는)

git stash

git pull origin master

하면 새로운 버전이 적용된다!

하지만, 내 changes는 아직 stash stack에 남아있고 실제 버전에 적용이 되지 않았다.

그래서 적용시켜 주려고 하는데, 나는 master에다가 changes를 적용시켜 줄 것이 아니므로

git checkout yjs/FeatDetailFix

이동 후

git stash apply

이렇게 하면 stash에 있는 내용이 적용이 된다.

그런데 login.jsx 파일이 충돌이 있었기 때문에 그것만 처리해주면 된다.

이제 새로운 버전과 내 changes들이 만나게 되었다.

아 stash에 있는 내용을 삭제하려면

git stash pop

git 재미있다. (~~물론 잘 될때만~~)

### 기타 할일

번역가 가입 폼

- 입력값 비어있는 것 처리 -> 프론트에서 처리하는 것으로, 필수 사항이 안 갔을 시에만 백엔드에서 에러 메시지 (남)
- 프로필 이미지 처리 -> 업로드 구현 완료 o, 미리보기 구현 완료
- 지금 career 빈문자열로 넣어주어야 한다. 나중에 career는 지워야함
- 프로필 이미지 delete 구현
- 로그인 로직 처리 (제출하기 클릭 시 로그인 페이지로 이동, 승인 후 로그인 시 번역 의뢰 리스트로 이동) o

번역 의뢰 리스트

- 견적 기간이 끝난 것들 삭제 안됨 -> 백에서 작업 예정
- Tag 많아질 때 어떻게 될 것인지 -> 슬라이더 구현

견적서 작성 폼

- 입력값 비어있는 것 처리 -> 프론트 처리 (남)
- 파일 다운로드 구현 o

내 번역

- 기능 완료
- 번역가 쪽에서 확정하기 누르면 갑자기 duplicate도 됨

견적서 디테일

- 파일 다운로드 구현 o

채팅방 리스트

- lastChatDate는 항상 0으로 되어 있다. => 백엔드 구현 중
- isRead도 true true만 뜸 -> 백엔드 구현 중
- 만약 이것들 기능 구현 안될 시 빼야함

채팅방

- 채팅 메시지를 기반으로 이름을 보여주다보니 잘못되어있는 경우 많고, 처음에는 이름이 없음. => client 쪽에서 상담하기 눌렀을 때만 보내주기만 하면 끝
- 확정하기/작업완료 ready 상태에서 안보여지기 하려면 이전 단계에서 status 가져와야 하는데 어떻게 할 것인지
- ChatDate 수평선 그리기

마이페이지

- 세팅 페이지에 회원탈퇴 기능 추가 (추후)
- 프로필 기존에 있던 이미지 삭제하기

공통

- 컴포넌트 props 추가된 부분 주석 추가
- 스켈레톤 UI or 스피너 적용
- logo 크기 조정
- navigation 하고, filterContainer z-index 1로 변경
- env 파일에 넣을 것 넣기
- NoList 변경되었으므로 그거 쓰는 곳들 수정해주기
- console 슬슬 지우기
- error 처리 (trycatch로 바꾸기, error 오는 것 받아서 모달로 처리)
