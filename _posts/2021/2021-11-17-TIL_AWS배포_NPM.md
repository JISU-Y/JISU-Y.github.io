---
layout: single
title: "[TIL] React, Web - AWS S3 배포 및 NPM"
categories: web
tag: [TIL, React, Javascript, AWS S3, 배포, 패키지 매니저, NPM, YARN]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## Javascript

### AWS S3 배포

#### S3 버킷에 배포하기

S3 버킷에 배포한 후 사이트에서 주소창에 /이동할페이지 를 입력해서 이동하면 오류가 뜬다.

1. 발생하는 이유
   일단 웹앱 안에서 동작하는 링크(혹은 버튼)을 눌러서 이동하는 것은 동작한다.
   <br>왜냐면 react는 이 동작을 인지하고 있고, listen해서 route를 업데이트 해줄 수 있기 때문이다. <br>그런데, 로드되지 않은 스크립트(index.html, bundle.js)로 이동하게 되면, JS는 로드된 적이 없고 url에서의 request를 처리할 방법이 없는 것이다.

   react router 사용할 때 url /home /review 이런식으로 page를 가지고 있는데,
   <br>문제는 S3에서 저 가상 url에서 refresh하게 되면, 404 혹은 403 에러가 발생하게 된다.
   <br>왜냐면 S3 버켓에 저기에 상응하는 파일이 없기 때문이다.
   <br>(/home /review 이건 컴포넌트로 이동하는 뭐 가상 url이지 진짜로 home.html 파일이 있어서 거기 페이지로 이동하는 것이 아니니까..?)

2. 해결 방법
   정적 웹사이트 호스팅(Static website hosting) 설정에서 Routing Rules를 추가해주는 것이다!
   가상 url로 이동했을 때 404, 403 error가 발생하므로

   ![](https://images.velog.io/images/jisu129/post/8c3f93bb-86f0-457d-bfd4-032aaf409641/reactrouterdom1.png)

   저기 안에 이러한 코드를 넣는 것이다.

   ```jsx
   <RoutingRules>
     <RoutingRule>
       <Condition>
         <HttpErrorCodeReturnedEquals>404</HttpErrorCodeReturnedEquals>
       </Condition>
       <Redirect>
         <HostName>myhostname.com</HostName>
         <ReplaceKeyPrefixWith>#!/</ReplaceKeyPrefixWith>
       </Redirect>
     </RoutingRule>
     <RoutingRule>
       <Condition>
         <HttpErrorCodeReturnedEquals>403</HttpErrorCodeReturnedEquals>
       </Condition>
       <Redirect>
         <HostName>myhostname.com</HostName>
         <ReplaceKeyPrefixWith>#!/</ReplaceKeyPrefixWith>
       </Redirect>
     </RoutingRule>
   </RoutingRules>
   ```

   이 코드는 S3에서 404 / 403 error 발생하면 url을 /home /review 앞에 #!를 붙여서 다시 쓰게 하는 것이다.

   그러면 저 더러운 url을 그대로 쓸 수 있는가?

   는 아님.

   그래서 render 될 때 처리 한 번 더 해주어야 함.

   ```jsx
   if (app) {
     // 1. Set up the browser history with the updated location
     // (minus the # sign)
     const path = (/#!(\/.*)$/.exec(location.hash) || [])[1];
     if (path) {
       history.replace(path);
     }

     // 2. Render our app
     render(<App />, app);
   }
   ```

   path에 #! 있으면 빼주고 다시 url을 /home /review로 바꿔줌으로써 해결할 수 있다.

3. 근데 난 왜 이거 안해줘도 되었던 거지?

Error Document에 index.html 넣어주는 것도 tricky하게 해결할 수 있는 방법이라고 한다.
<br>애초에 404 403 error가 발생했어도 index.html을 반환하게 하면 된다는 설명이다.

[출처] https://via.studio/journal/hosting-a-reactjs-app-with-routing-on-aws-s3
[출처] https://stackoverflow.com/questions/51218979/react-router-doesnt-work-in-aws-s3-bucket

### 패키지 매니저

NPM은 Node Package Manager로 모바일로 치면 앱 스토어 같은 것이다.

Node.JS를 설치하면 자동으로 설치되는 것이다.

잠깐, Node.JS가 정확히 뭐야..

- Node.JS란?

  > 크롬 V8 자바스크립트 엔진으로 빌드된 런타임 환경
  > <br>런타임(특정 언어로 만든 프로그램들을 실행할 수 있는 환경)

  자바스크립트 언어로 서버를 구축할 때 쓰인다라는 것으로 이해하자.
  <br>javascript는 일반적으로 브라우저에 내장되어 있기 때문에 브라우저 콘솔에 입력해도 잘 동작하는 것이다.
  <br>이런 javascript는 브라우저에 종속된 언어인데, 이를 밖으로 꺼내서 사용하는 데에 Node.js가 필요한 것이다.
  => server(밖)에는 브라우저가 없으니 그냥 javascript를 사용할 수 없으니 이러한 런타임이 필요한 것.

#### NPM이 무엇인지?

그래서 Node.js 깔았더니 NPM(Node Package Manager)이 왔다는 것이다.

풀 네임처럼 Node.JS에 필요한 패키지들을 관리한다.

필요하다면 npm을 이용해서 **구축하려고하는 어플리케이션에 필요한 모듈을 찾아보고, 다운로드 할 수 있다는 것**이다.
<br>(내가 필요한 패키지들을 검색, 설치, 삭제, 업데이트 할 수 있는 것)

- NPM의 특징

1.  장점
    <br>사실 NPM의 장점은 아니고, NPM이 제공되어 좋은 모듈을 다운받을 수 있다는 것이 좋은 것이다.
    <br>처음부터 잘 짜여진 모듈(패키지 => 소프트웨어 / 어플리케이션 / 프로그램 등)을 다운 받도록 도와준다.
    <br> -> react-app을 혼자서도 구축할 수 있겠지만 그 많은 hook들하고 기능하고 언제 다할까. 이미 다 되어서 페이지만 커스텀할 수 있는 모듈을 다운 받으면 그만인데.
    <br>잘 짜여진 모듈을 다운 받는 것이므로 프로젝트의 성능도 좋아지고, 보안면에서도 아주 좋다고 한다.

2.  단점
    <br>하지만, 위의 장점은 언제까지나 잘 짜여진 모듈에 해당하는 것이다.
    <br>모듈이 페이지와 맞지 않아 커스텀하거나 모듈에 에러가 있는 경우 그 모듈을 수정하기 쉽지 않다.
    <br>또한, 이것은 진짜 npm의 단점인데, npm은 모듈 설치가 비교적 느리다.
    <br>(이를 대체하기 위해 페이스북에서 yarn이라는 것을 도입했다. 밑에서 따로 NPM과 YARN을 비교해보도록 한다.)

- 사용 방법
  패키지들은 완성품도 있고, 부품도 있다.

[npmjs.com] 여기서 필요한 패키지들을 검색할 수 있다.

기본적으로 npm은 CLI(Command Line Interface)으로 git 처럼 git clone , git add와 같이 커맨드 창에서 명령어로 실행할 수 있다.

1.  패키지 다운

    > npm install -g local-web-server

    -g 글로벌 즉 전역에서 설치
    <br>아무것도 안쓰면 로컬에서 설치

    > sudo npm install -g local-web-server

    sudo 를 앞에 붙여주면 관리자 권한으로 실행해주는 명령어가 된다.

2.  실행
    <br>다운받은 패키지 실행방법을 찾아보고 실행하면 된다.
    보통 ^c (ctrl + c) 종료

3.  탐색
    <br>커맨드 창에 npm 치면 사용할 수 있는 명령어들 나온다.

npm list
<br>현재 폴더에 깔린 패키지 리스트 알려줌

npm list -g
<br> 전역에 깔린 패키지 알려줌

npm list -g --depth-0
<br>직접 설치한 패키지만 알려줌

4.  패키지 업데이트 / 삭제

    - 업그레이드
      <br>npm update local-(패키지 이름) -g

    - 삭제
      <br> npm uninstall -g (패키지 이름)

#### yarn

아까 전에 yarn이 더 빠르다고 했는데, 왜 어떻게 빠른 것일까?

1.  병렬 설치
    <br>패키지를 설치하면 작업을 수행하게 되는데, npm은 여러 패키지를 설치할 때 하나의 패키지가 완전히 설치가 끝나야 다른 패키지를 설치하기 시작한다(순차적으로 진행)
    <br>하지만, yarn은 이러한 작업을 **병렬로 설치하기 때문에 퍼포먼스와 속도가 증가**하게 된다.

        따라서 react를 설치했을 때 yarn으로 설치했을 때가 훨씬 빠른 것을 확인할 수 있다.

        npm — 3.572 seconds
        Yarn — 1.44 seconds

2.  자동 lock 파일 생성
    <br>npm과 yarn 모두 패키지에서 프로젝트의 종속성(dependency)의 버전 번호를 추적한다.
    <br>json 파일의 종속성들을 설치할 때마다 종속성 버전이 버전 번호 앞에 ^시작되는 것들이 있다.

        즉, 다른 시스템에 모든 패키지를 설치하거나 설치할 명령을 수동으로 실행할 때마다 패키지 관리자가 릴리즈된 최신 버전을 찾는다.
        <br>최신 버전이 있으면 패키지 파일에 있는 버전이 아니라 최신 버전을 그냥 깔아버린다.

        이를 방지하기 위해서는
          <br>1) 패키지 파일에 ^를 제거하는 방법과
          <br>2) 잠금파일을 생성해서 한번에 특정 버전만 설치하는 방법
          <br>이 있다.

        종속성이 추가되면 yarn은 yarn.lock 파일을 자동으로 추가한다.
        <br>npm은 npm shrinkwrap 명령어로 lock 파일을 생성했으나, 현재는 업데이트되면서 lock.json 파일을 제공하면서 추가적으로 명령어를 입력하지 않아도 되도록 업데이트되었다.
        <br>그러나, 속도는 yarn을 따라가지 못한다.

3.  보안
    <br>npm은 다른 패키지를 즉시 포함시킬 수 있는 코드를 자동으로 실행하므로 보안에 취약해진다.
    <br>반면, yarn은 yarn.lock 혹은 package.json 파일에 있는 파일만 설치한다.
    <br>이런 면에서 yarn의 보안성이 더 좋다고 말하는 것이다.

[출처] https://javascript.plainenglish.io/npm-vs-yarn-choosing-the-right-package-manager-a5f04256a93f
