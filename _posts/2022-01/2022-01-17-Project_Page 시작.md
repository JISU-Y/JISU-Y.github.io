---
layout: single
title: "[Project] React 컴포넌트 만들기 (3)"
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

### 의문 사항 정리

- profileUrl 받는 곳이 없는데 여기에 추가하는지?
  아니면 마이페이지에만 넣을지?
  => profile이미지 추가

- request/list api response에 userName은 DB에 없는데 추가 필요한지 아예 안쓸것인지? => 카카오에서 nickname을 받아올 수 있을지 불확실. 안되면 카카오의 유저 id의 뒤의 네 자리로 사용할 것 => username 있었다. request/list가 아닌 estimate/list에

- deadline은 string(0000-00-00) 형식으로 하시는지, 날짜 형식(2022-01-30T04:33:14.0900)으로 하는 것인지 => 프론트에서 저장하는 대로. 일단 0000-00-00 타입으로 할 것

- needs(클라이언트 쪽에서 요청하는 것 어디다가 넣을지?) => summary card에 추가함

- 지금 클라이언트는 풀 견적서를 보지 못합니다.
  따라서 번역가 페이지에 있는 견적서 페이지를 클라이언트에서도 볼 수 있도록 하면 좋을 것 같은데, 어떻게 하면 좋을까요?

      클라이언트가 견적서 내용(본인이 설정한 것들도)을 다 볼 수 있으면 좋을 것 같다는 의견인데, 이대로 해도 엄청 큰 문제가 될 것 같지는 않아서 여쭙습니다!

      => 지금 이미 나온 상태로 작업!

- 리뷰에서 api/review 이거를 내 리뷰만 어떻게 가져올 수 있을지? => 회의 필요

- api/estimate/list/:id POST 요청 계속 라우터 존재하지 않는 에러 발생. 내일 회의 예정

### TranslationList page

```jsx
const TranslationList = () => {
  const [estimates, setEstimates] = useState([]);
  const navigate = useNavigate();

  // mobx에서 mobx에 있는 함수를 사용해서 비동기 처리해야함
  // 그 함수로 useEffect 안에도 넣고, PageHeader의 reloadEvent에도 넣어야 한다.
  useEffect(() => {
    const fetchEstimateList = async () => {
      const {
        data: { data },
      } = await apis.estimatesList(); // sendEstimate이것으로 바꿔야 하나 지금 프로필을 만들 수 없어 임시로 client 요청하는 중
      console.log(data);
      setEstimates(data);
    };
    fetchEstimateList();
  }, []);

  const handleClick = (estimate) => {
    navigate("/translator/estimate/form", {
      state: {
        estimate,
      },
    });
  };

  return (
    <div>
      <PageHeader title="번역 리스트" useReloadButton />
      <p>나중에 필터 기능 들어갈 곳</p>
      <h2>곧 마감되는 요청!(마감임박 순 구현 필요)</h2>
      {estimates.slice(0, 2).map((estimate) => (
        <EstimateCard
          key={estimate.id}
          userName={estimate.User.username}
          field={estimate.field}
          beforeLanguage={estimate.beforeLanguage}
          afterLanguage={estimate.afterLanguage}
          isText={estimate.isText}
          deadline={estimate.deadline}
          createdTime={estimate.createdAt}
          onClick={() => handleClick(estimate)}
        />
      ))}
      <h2>번역의뢰 리스트</h2>
      {estimates.map((estimate) => (
        <EstimateCard
          key={estimate.id}
          userName={estimate.User.username}
          field={estimate.field}
          beforeLanguage={estimate.beforeLanguage}
          afterLanguage={estimate.afterLanguage}
          isText={estimate.isText}
          deadline={estimate.deadline}
          createdTime={estimate.createdAt}
          onClick={() => handleClick(estimate)}
        />
      ))}
    </div>
  );
};
```

setState로 estimates 변수를 두고, 비동기 처리를 안에서 하여 setEstimates로 값을 업데이트 해주었다.

클릭 시 해당 데이터를 가지고 form 페이지로 이동하는 것까지 구현 완료했다.

- react-router useHistory가 useNavigate로 모두 대체되었다.

  - history

  ```jsx
  const history = useHistory();

  history.push("/");
  history.goback();
  history.go(-2);
  history.push(`/user/${user._id}`);
  ```

  - navigate

  ```jsx
  const navigate = useNavigate();

  navigate("/");
  navigate(-1);
  navigate(-2);
  navigate(`/user/${user._id}`);
  ```

  navigation에서 state를 담아 보낼 경우,

  ```jsx
  navigate(
    'thepath',
    {
      state: {
        //...values
      }
    }
  })
  ```

해야하는 것:

setState 사용하지 않고, 전역 상태 관리를 통해 비동기 처리하고, 응답 값을 가져오는 것으로 해야한다.

estimate card 나타내는 데에 위의 부분은 두 개만, 그것도 마감 임박한 것만 보여주어야 한다.

### EstimateForm Page

TranslationList에서 클릭된 Estimate의 정보를 가져와서 page에 보여주는 것 완료되었다.

하지만, 그 id를 가지고 견적서 POST 요청을 하는데 404 에러가 계속 발생하여 백엔드 개발자와 상의가 필요하다.
