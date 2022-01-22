---
layout: single
title: "[Project] 번역가 페이지 기능 구현"
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

### 기능 구현

#### 견적서 보내기

견적서 보내기에서 comment 길면 차단됨

### 번역가 가입 폼

#### 프로필 미리보기 기능 추가

이미지를 선택해서 프로필 미리 보여주어야 한다.

아직 이미지 자체를 저장할 공간이 없어서 미리보기라도 되도록 설정하려고 했다.

```jsx
if (id === "profileFile") {
  setFileImage(URL.createObjectURL(files[0]));
}
```

onChange 때 profileFile이 id라면 createObjectURL 메서드를 이용해서 파일을 저장하고, 보여주도록 한다.

#### 가능언어 추가 기능 추가

제대로 이야기가 되지 않은 채로 기능을 구현해야 했다.

일단, 어느 정도 이야기가 되었던 것이 가능 언어 추가할 수 있는 버튼이 있어 그 밑으로 셀렉트가 계속 생기는 것으로 했었다.

그래서 그것을 기반으로 기능을 구현해놓기로 했다.

로직은 다음과 같다.

    1. 셀렉트 인풋을 늘린다. (혹은 줄인다.)
    2. 그 셀렉트 인풋마다 value를 다르게 한다.
    3. 그러려면 value를 담고 있는 state를 생성한다.
    4. 그 객체를 돌면서 화면에 나타내준다.

```jsx
const [selectInputs, setSelectInputs] = useState([0])
const [languages, setLanguages] = useState({})

const handleSelectChange = e => {
  const { name, value } = e.target
  setLanguages({ ...languages, [name]: value })
}

const addSelectInput = () => {
  setSelectInputs(prev => [...prev, prev.length.toString()])
}

const removeSelectInput = () => {
  if (selectInputs.length === 1) return // 기본 한 개는 가지고 있어야 하므로

  // 가능 언어 추가했다가 아직 선택 안했을 경우에도 삭제가 되어야 하므로
  // languages object 길이 early return 보다 먼저 해준다.
  setSelectInputs(selectInputs.slice(0, selectInputs.length - 1))

  if (Object.keys(languages).length === 1) return

  // 그냥 바로 수정 하면 당연히 안될 듯 그래서 복제함
  const tempLanguages = { ...languages }
  delete tempLanguages[Object.keys(tempLanguages).length - 1]
  setLanguages(tempLanguages)
}

useEffect(() => {
  const language = Object.values(languages).join(',')
  setFormData({ ...formData, language })
}, [languages])

return (
  {selectInputs.map((select, index) => (
    <SelectInput
      key={select}
      id="language"
      name={select.toString()}
      value={languages[index]}
      onChange={handleSelectChange}
      defaultOption="가능언어"
      options={language}
    />
  ))}
)
```

일단 selectInput의 개수를 정해주는 state를 생성한다. selectInput 배열 안에 있는 내용이 중요하진 않다. 때문에 차례대로 숫자를 넣어준다. [0, 1, 2, ...] 되도록.

이 안에서 SelectInput 컴포넌트가 반복된다.

그 다음 SelectInput 컴포넌트의 onChange 이벤트에서의 함수를 만든다.

각 SelectInput를 선택했을 때 value를 따로 따로 담아야 하기 때문에, 그 value를 담을 state를 생성한다. 언어의 배열을 담고 있어야 하므로 languages라고 한다.

languages 객체로 만든다. 왜냐하면 selectInput가 가진 name이 0, 1, 2, 3, 이고, 배열은 name이 겹쳤을 때 확인하는 절차가 장황하기 때문이다. 그래서 0: '한국어' 이런식으로 될 수 있도록.

그래서 화면에 나타낼 때에는 value={languages[index]} 이렇게 함으로써 각각의 selectInput 마다 value를 가질 수 있게 된다.

languages 또한, 전체 FormData에 들어가야 한다.

따라서 languages가 변할 때 마다 setFormData를 해준다.

! 추가적으로 가능 언어를 삭제해야할 때의 로직도 필요해졌다.

먼저, languages의 길이가 1인 경우는 더 이상 삭제하지 못하도록 early return을 한다.

그 다음 languages에 있는 마지막 프로퍼티를 삭제해주어야 하는데, languages를 직접 건드릴 수 는 없다.

따라서 먼저 languages 객체를 복사한 다음 거기에서 delete 키워드를 이용하여 마지막 프로퍼티와 값을 제거한 후 그 복사한 객체를 setLanguages 해주면 된다.

그 다음 selectInputs 또한 하나를 줄여준다.

그런데, setSelectInputs을 languages early return보다 늦으면 가능 언어 추가했다가 아직 선택이 되지 않은 경우에는 삭제가 되지 않는다. 그래서 setSelectInputs을 languages early return보다 먼저하고, selectInputs 길이도 1인 경우 early return을 한다.

### 번역가 내 번역

#### 보낸 견적, 진행중, 완료 필터 기능 추가

보낸 견적, 진행 중, 완료 필터 기능을 추가하였다.

```jsx
const [clickedStatus, setClickedStatus] = useState("ready");
const [clickNumber, setClickNumber] = useState(-1);

useEffect(() => {
  // 필터 적용
  setClickNumber(0);
}, []);

const handleToggleMenu = (number) => setClickNumber(number);

// ToggleMenu filter
useEffect(() => {
  switch (clickNumber) {
    case 0:
      // 보낸 견적 (ready)
      setClickedStatus("ready");
      break;
    case 1:
      // 진행 중 (processing)
      setClickedStatus("processing");
      break;
    case 2:
      // 진행 완료 (done)
      setClickedStatus("done");
      break;
    default:
      setClickedStatus("ready");
      return;
  }
}, [clickNumber]);

// return
{
  estimates
    .filter((el) => el.status === clickedStatus)
    .map((estimate) => <EstimateCard />);
}
```

clickNumber는 ToggleMenu에서 필요한 state이다. 어떤 것이 클릭되었는지 알 수 있는 state.

이 clickNumber의 값에 따라 clickedStatus가 무엇인지 판단한다. (switch 문으로 작성)

clickedStatus가 set되었으면 그것을 기반으로 원래의 list(estimates)를 filtering 한다. (선택된 status만 map 돌 수 있도록 한다.)

### 기타 할일

번역가 가입 폼

- 가능언어 선택 시 배열에 추가되도록 - 어떻게 추가하게 할 것인지 다시 이야기 필요하다 o
- 프로필 선택 시 이미지 바뀌도록 o
- 버튼 크기 변경 o

번역 의뢰 리스트

- 필터 기능 o
- 필터 모달 추가
- 선택 시 배열에 추가하여 태그로 나타내기
- 가능 언어 받아와서 디폴트로 체크하기
- 견적 기간이 끝난 것들 삭제 안됨

견적서 작성 폼

- 페이지 구성
- 버튼 크기 변경

내 번역

- 보낸 견적, 진행중, 완료 필터 기능 o

견적서 디테일

- 페이지 스타일 적용
- 파일 다운로드 구현

채팅방 리스트

- chatlistcard에 날짜 가져올 데이터가 없음 -
- chatlist 지금 확정하기가 되어야지만 내 채팅 리스트에 뜸
- isRead도 true true만 뜸
- api/chatroom/client bad request 뜸
- lastChatDate는 항상 0으로 되어 있다.

채팅방

- 클라이언트에서 상담하기 클릭 시 번역가 쪽에서 봇 챗 날리기 o => default chat이나 시간, 번역가 이름을 받아올 수 있어야 할 것 같다. (클라이언트 쪽에서 상담하기 누를 때 번역가 정보를 같이 주는 것이 좋을 것 같다.) // 남경님 - 클라이언트 번역가디테일 페이지에서 상담하기 누르면서 번역가 이름하고 채팅 생성한 시간도 보내주어야 한다. (채팅 리스트에서는 생성한 시간을 알 수 없음)
- 채팅 메시지를 기반으로 이름을 보여주다보니 잘못되어있는 경우 많고, 처음에는 이름이 없음.
  => 남경님이 보내주기만 하면 끝, chatList client 해결되면 끝
- Client 쪽에서는 작업 완료 말고 확정하기 버튼

마이페이지

- profileInfo 카드 스타일 적용
- 리뷰 data fetching 확인

공통

- 스크롤 디자인 적용
- 토글 메뉴 state 밖으로 빼서 props로 받기 o
- 의뢰 요청에서 fileInput에 useUploadName, label="파일 올리기(텍스트)" 속성 추가해주어야 한다. o

- 헤더 둘다 fixed로 변경 o
- 헤더 640px 이상으로 가면 height 80px으로 변경 o
- width: 100% 변경 o
- z-index: 5 o
- main 에는 hamburger 메뉴 삭제 o

- FileInput.jsx 하드코딩되어있는부분 수정 o

- prop 추가된부분 주석 추가해주세요.
- 채팅 Client에서 작업완료 버튼 안보이도록 수정
