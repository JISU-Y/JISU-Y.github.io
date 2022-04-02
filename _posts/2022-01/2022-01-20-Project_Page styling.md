---
layout: single
title: "[Project] 번역가 페이지 style 적용"
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

### style 적용

#### Navigation Bar 컴포넌트

Navigation bar는 Translator와 User 모두 완료

추후에 icon 크기 및 font size 조정 시 변경 될 수 있음

#### Header 컴포넌트

Header 컴포넌트는 PageHeader의 경우 햄버거 메뉴가 absolute로 위치 지정되어 있어 정렬이 되지 않는 것 이외 스타일링은 되었다.

SubPageHeader는 스타일링 모두 적용 되었고,
Chat 페이지에서 user 화면에서는 번역가의 이름을 Header에 담고 있어야하므로 추가되었다.

#### Input 컴포넌트

CheckBoxInput을 제외하고 모든 Input에 대해 스타일 적용을 완료했다.

- select

  기본적으로 달려있는 화살표 모양을 삼각형 모양으로 변경해야 했기 때문에 다음과 같이 적용하였다.

  ```css
  appearance: none;
  background: url(${props=>props.bgUrl}) no-repeat;
  background-position: right 13px top 50%;
  ```

  select 태그의 background에 img를 원하는 모양(삼각형)으로 추가하여 나타내고 position을 통해 삼각형 위치를 조정했다.

  선택이 되지 않았을 경우 회색 글씨, 옵션에서 어떠한 것을 선택했을 경우 검은색 글씨로 나타내야 했기 때문에 props로 option의 value와 defaultOption의 내용을 받아왔다.

  ```jsx
  /* value(선택된 옵션)이 defaultOption이면 회색, 다른 것이면 검은색
  추가로, defaultOption나 value가 정해지지 않은 상태라면 무조건 회색으로 나타낸다.  */
  color: ${props =>
    props.defaultOption && props.value
      ? props.value === props.defaultOption
        ? '#C4C4C4'
        : '#000'
      : '#C4C4C4'};
  ```

  처음 상태에는 value나 dafaultOption 내용이 undefined이기 때문에 무조건 회색 글씨로 보여준다.

  만약 선택이 되어서 value가 있다면 검은색 글씨로 나타내 주는데, value랑 defaultOption과 같은 내용을 가지고 있다면 다시 회색 글씨로 나타내준다.

  그리고, focus 시 border에 색상을 적용했다.

  ```jsx
  &:focus {
    outline: none !important;
    border: 1px solid var(--main-color);
  }
  ```

- FileInput

  FileInput은 커스터마이징이 꽤 어렵다.

  [참고] https://velog.io/@sklove96/inputtypefile-%EC%BB%A4%EC%8A%A4%ED%85%80%ED%95%98%EA%B8%B0

  이 글을 참고하여 적용하였다.

  ```jsx
  <InputContainer>
    <Label htmlFor="input-file">파일 올리기(텍스트)</Label>
    <UploadName
      onChange={onChange}
      value={fileName}
      placeholder="선택된 파일 없음"
    />
    <Input
      id="input-file"
      type="file"
      name={name}
      accept=".txt"
      onChange={handleFileChange}
    />
  </InputContainer>
  ```

  먼저, 실제 input (type이 file로 지정된)을 대신 클릭하게 해줄 label을 만들고, for(React에서는 htmlFor)와 input의 id를 맞춘다.

  업로드만 구현하려면 label과 실제 input만 있으면 되지만, 추가로 파일 이름을 나타내려고 하기 때문에 input 을 따로 추가하고 여기서 value로 파일 이름을 명시해주려고 한다.

  그래서 실제 input에서 파일이 선택 되면 onChange 이벤트가 발생하므로 거기서 e.target.value(fakepath가 포함된 파일명과 경로)를 앞 부분을 잘라서 파일명만 state로 저장하여서 파일명을 나타낼 input에 value로 지정해준다.

#### EstimateDetail 컴포넌트

### 기타 할일

Chat에서 렌더링 최적화 필요

형래님 button cursor pointer 바꿀 수 있게끔 바꿔주셔야 합니다.
TopDownButton 네비게이션에 걸려서 위치를 바꾸거나 z-index 조정을 해야할 것 같습니다.

api는 리뷰하고, chat list만 남았다.

스타일 적용
button border radius 필요

textinput 쓰는데에서 value 써주어야 함.. 그래야 활성화 비활성화 체크를 할 수 있다.
