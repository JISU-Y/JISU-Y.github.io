---
layout: single
title: "[Project] 번역가 페이지 기능 디테일 (2)"
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

### 디테일 수정

오늘은 자잘한 디테일 수정.

#### 카카오 로그인

카카오 로그인 로직에 대해 알게 되었다.

일단 프론트 단에서 인가 코드를 받아와서 백엔드에 전달하고, 로그인 정보 가져오는 것까지 모두 되었다.

자세한 내용은 내일 로그인 로직 처리를 명확하게 적어두고 글을 올리려고 한다.

#### firebase 파일 업로드

state 선언

```jsx
const [fileImage, setFileImage] = useState(""); // input으로 받아온 file
const [previewURL, setPreviewURL] = useState(""); // 파일의 실제 다운로드 url (unique)
const [preview, setPreview] = useState(null); // 파일 미리 볼 img 요소 state
```

파일 셋

```jsx
// file set
const handleFilChange = () => {
  const { files } = e.target;
  const file = files[0];
  const reader = new FileReader();
  reader.onloadend = () => {
    setFileImage(file);
    setPreviewURL(reader.result);

    // 파일 저장하러 가기
    saveToFirebaseStorage(file);
  };
  if (file) reader.readAsDataURL(file);
};
```

input에서 file이 선택이 될때 실행

target에서 가져온 file을 이용해서 file에 넣고, reader를 이용해서 url을 set한다.

이후에 이제 storage에 file을 저장하러 간다.

파일 저장

```jsx
const saveToFirebaseStorage = (file) => {
  // filename 겹치지 않게 변경
  const uniqueKey = new Date().getTime();
  const newName = file.name
    .replace(/[~`!#$%^&*+=\-[\]\\';,/{}()|\\":<>?]/g, "")
    .split(" ")
    .join("");

  const metaData = {
    contentType: file.type,
  };

  console.log("Images/" + newName + uniqueKey);

  const storageRef = sRef(storage, "Images/" + newName + uniqueKey);
  const UploadTask = uploadBytesResumable(storageRef, file, metaData);
  UploadTask.on(
    "state_changed",
    (snapshot) => {
      const progress = (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
      console.log(`Upload is ${progress}% done`);
    },
    (error) => {
      alert(`error: image upload error ${JSON.stringify(error)}`);
    },
    () => {
      getDownloadURL(UploadTask.snapshot.ref).then((downloadUrl) => {
        console.log(`완료 url: ${downloadUrl}`);
        setFormData({
          ...formData,
          profileFile: downloadUrl,
        });
      });
    }
  );
};
```

firebase storage에 파일을 업로드할 때, 이름이 겹치면 덮어쓰기가 되어서 모두 unique한 파일명을 가지고 있어야 한다.

그리고 나서 ref를 활용하여 폴더명/파일 이름 으로 저장한다.

downloadURL을 요청해서 그것을 백엔드에 전달해야하므로 formData안의 profileFile에 넣는다.

preview 파일

```jsx
// preview 파일
useEffect(() => {
  setPreview(
    <img
      src={fileImage !== "" ? previewURL : defaultProfile}
      alt="profileImg"
      width="64px"
      height="64px"
    />
  );
  return () => {};
}, [fileImage, previewURL]);
```

fileImage나 previewURL이 변경될 때마다 요소(컴포넌트) 내용을 바꿔준다.
preview를 컴포넌트로 사용하면 된다.

### 기타 할일

번역가 가입 폼

- 입력값 비어있는 것 처리 -> 프론트에서 처리하는 것으로, 필수 사항이 안 갔을 시에만 백엔드에서 에러 메시지
- 프로필 이미지 처리 -> 업로드 구현 완료 o, 다운로드 구현 완료 / 남경님 코드 머지 후 적용 예정
- 파일 input 또 안됨 (프로필 파일 올릴 때와 text 파일 올릴 때 어떻게 할 지 봐야함) o
- 지금 career 빈문자열로 넣어주어야 한다. 나중에 career는 지워야함
- 로그인 로직 처리 (제출하기 클릭 시 로그인 페이지로 이동, 승인 후 로그인 시 번역 의뢰 리스트로 이동)

번역 의뢰 리스트

- 견적 기간이 끝난 것들 삭제 안됨 -> 백에서 작업 예정
- Tag 많아질 때 어떻게 될 것인지 -> 슬라이더 구현하기로

견적서 작성 폼

- 입력값 비어있는 것 처리 -> 프론트 처리
- 파일 다운로드 구현 -> 남경님 코드 머지 후 처리 예정

내 번역

- 카드 희망날짜 색/배치 변경 (Card 컴포넌트)

견적서 디테일

- 파일 다운로드 구현 -> 남경님 코드 머지 후 처리 예정

채팅방 리스트

- lastChatDate는 항상 0으로 되어 있다. => 백엔드 구현 중
- isRead도 true true만 뜸 -> 백엔드 구현 중

채팅방

- 클라이언트에서 상담하기 클릭 시 번역가 쪽에서 봇 챗 날리기 o => 다 되었는데, TranslationDetail에서만 offerPrice와 isText를 가져올 수 있다. 그래서 번역가, 클라이언트 쪽에서도 Chatlist에서도 offerPrice와 isText를 가져와야 하고, 클라이언트 쪽 견적서 디테일에서도 이 두 개가 넘어와야 한다.
  => offerPrice, isText 쓰지 않기로 함
- 채팅 메시지를 기반으로 이름을 보여주다보니 잘못되어있는 경우 많고, 처음에는 이름이 없음.
  => client 쪽에서 상담하기 눌렀을 때만 보내주기만 하면 끝
- 채팅방 리스트 불러오기 클라이언트 쪽 이상함 (JISU 번역가만 모두 가져옴) o
- 호버 시 마우스 모양 그대로, 호버 시 스타일 처리 o

마이페이지

- 리뷰 data fetching 확인 => review들 가져올 때 왜 translatorId가 맞지 않지? 내꺼는 2인데 8로 요청해야 옴 => 리뷰는 건당 할 수 있으므로 requestId로 요청을 해야한다. 나머지 버그는 백엔드에서 처리 완료. o
- 그리고 작업 완료 많이 눌렀는데 왜 번역이 0건? => 백엔드 번역 카운팅 수정 완료

공통

- 스크롤 디자인 적용 o
- 헤더 리팩토링 o
- reload 기능 활성화 o
- Navigation 처리 o
- 햄버거 메뉴 -> 남경님
- 컴포넌트 props 추가된 부분 주석 추가
- form input validation 기준 정리 o
- 스켈레톤 UI or 스피너 적용
- 회원탈퇴 및 로그아웃 구현 o => api는 확인 완료. 로직은 자세하게 처리해야함
