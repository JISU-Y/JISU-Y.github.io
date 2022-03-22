---
layout: single
title: "[Course / TIL] Wanted Pre Onboarding FE Course 5주차 - 개인 과제 9 진행"
categories: Course
tag: [Course, TIL, Wanted, PreOnboarding]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Course

**Wanted Pre Onboarding FE Course**

## 과제

### 구현

#### 렌더링 최적화

**memo**

```jsx
const HeaderComponent = () => <Header title="레포 검색" />;

const MemoHeader = memo(HeaderComponent);

const RepoCardComponent = ({ repo }) => (
  <RepoCard key={repo.id} repoInfo={repo} />
);

const MemoRepoCard = memo(RepoCardComponent);
```

```jsx
return (
  <Container>
    <MemoHeader />
    <ContentWrapper>
      <SearchForm onSubmit={handleSubmit}>
        <Input
          placeholder="repo를 검색해주세요."
          value={text}
          onChange={(e) => setText(e.target.value)}
        />
        <SearchButton type="submit">검색</SearchButton>
      </SearchForm>
      {loading
        ? Array.from([1, 2, 3, 4, 5], (el) => <Skeleton key={el} />)
        : pageItems?.map((repo) => <MemoRepoCard key={repo.id} repo={repo} />)}
    </ContentWrapper>
    {data?.items.length < 1 && <NoList msg="검색 결과가 없습니다." />}
    {data?.items.length > 0 && page !== maxPage && (
      <TargetDiv ref={setTarget} />
    )}
  </Container>
);
```

input에 값을 입력할 때마다 RepoCard 컴포넌트와 Header 컴포넌트가 렌더링이 일어났다.  
input에 값을 입력한다고 값이 바뀌거나 렌더링이 일어날 이유는 없으므로 memo를 통해 불필요한 렌더링을 줄이고자 했다.

**구현 결과**

| 최적화 전                                                                   | 최적화 후                                                                   |
| --------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-03/22_01.gif) | ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-03/22_02.gif) |

#### skeleton UI

**설계**

RepoCard와 IssueCard는 비동기 data fetching을 통해 디스플레이되는 요소들이기 때문에,  
loading이 true되는 경우에 Loader를 적용하려고 했다.  
Spinner는 많이 적용해보았지만 Skeleton UI를 적용해보고 싶었다!

Spinner는 매우 간단하지만, UI적으로 기다리는 느낌이 많이 강하다.  
유튜브 등 많은 서비스에서 스켈레톤 UI를 사용한다.

스켈레톤 UI는 최대한 실제 요소와 그 내부의 배치된 요소와 비슷하게 구현할수록 좋다.

**요소 만들기**

```jsx
const ContentContainer = styled(ContentBox)`
  height: 100px;
  &:hover {
    transform: none;
  }
`;
```

ContentBox는 실제 요소에서도 사용하던 Container이다.  
똑같은 스타일 컴포넌트를 사용해서 요소 껍데기를 만든다.

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-03/22_03.png)

**요소 내 요소 배치**

카드 컴포넌트마다 내부 요소가 배치되어 있다.  
위치를 맞추어서 회색 네모로 만들 것이다.

```jsx
export const GrayBox = styled.div`
  width: 150px;
  height: 20px;
  background-color: lightgray;
`;
```

따라서 반복되어 사용될 것이기 때문에 공통 컴포넌트에 넣는다.  
이를 사용해서 width와 height를 요소에 맞게 설정하여 실제와 비슷하도록 설정한다.

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-03/22_04.png)

**로딩 애니미에션**

위의 요소만 가만히 있는다면 살아있는 느낌이 나지 않을 것이다.  
따라서 보통 왼쪽에서 오른쪽으로 흰색 선이 움직이도록 구현하려고 했다.

이를 위해 움직일 요소를 만든다.

```jsx
export const loadingAnimation = css`
  &::before {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 30px;
    height: 100%;
    background: linear-gradient(to right, #ddd, #eee, #ddd);
    animation: loading 1.5s infinite linear;
  }

  @keyframes loading {
    0% {
      transform: translateX(0);
    }
    50%,
    100% {
      transform: translateX(440px);
    }
  }
`;
```

가상 요소로 만든 이유는 사용되는 GrayBox(배치되는 요소)마다 흰색 선이 움직여야 하므로 가상 요소 부분과 keyframe 부분을 따로 빼둔 것이다.

```jsx
export const GrayBox = styled.div`
  width: 150px;
  height: 20px;
  background-color: lightgray;
  position: relative;
  overflow: hidden;

  ${loadingAnimation}
`;
```

모든 GrayBox에 가상 요소와 keyframe이 들어갈 것이다!  
추가적으로 가상 요소가 GrayBox 바깥으로 나가지 않아야 하니까 `overflow: hidden`을, 가상 요소가 absolute이므로 GrayBox에서는 `position: relative`를 설정해준다.

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-03/22_05.gif)

**RepoCard Skeleton**

```jsx
const SkeletonRepo = () => {
  return (
    <ContentContainer>
      <RepoInfoWrapper>
        <GrayBox></GrayBox>
        <Description></Description>
        <Owner></Owner>
      </RepoInfoWrapper>
      <Button></Button>
    </ContentContainer>
  );
};
```

IssueCard도 마찬가지로 해준다!

**IssueCard Skeleton**

```jsx
const SkeletonIssue = () => {
  return (
    <ContentContainer>
      <RepoTitle></RepoTitle>
      <IssueTitle></IssueTitle>
      <IssueBody></IssueBody>
      <BottomInfo>
        <BottomInfoEl></BottomInfoEl>
        <BottomInfoEl></BottomInfoEl>
      </BottomInfo>
    </ContentContainer>
  );
};
```

**구현 결과**

| RepoCard                                                                    | IssueCard                                                                   |
| --------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-03/22_06.gif) | ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-03/22_07.gif) |

### TODO

렌더링 최적화 o  
api 호출 최적화 => github api가 header cache가 막혀있음

스켈레톤 UI o  
UI 꾸미기 o  
코드 이쁘게 정리 o

## 회고 (TIL)

**2022.03.22 Daily 회고**

✏오늘 한 일

- 개인 과제 수행
  - 렌더링 최적화
  - Skeleton UI 적용
- 면접 준비

⁉느낀 점

요구 기능은 모두 완성했다.  
확실히 디자인은 많이 다른 영역이라는 생각이 든다.. 너무 못생겼다.

팀 프로젝트를 하기 전에 했다면 결과물이 많이 달랐을 것이라고 생각한다.

면접 준비를 해도 항상 생각 이외의 것을 물어보니 속수무책이다.. 언제, 어디까지 공부해야지는 모르겠다.

🎃현재 나의 상태

어찌됐든 잘 적응하고 경력을 잘 키울 수 있을 회사에 입사했으면 좋겠다.

<hr>
