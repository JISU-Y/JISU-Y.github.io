---
layout: single
title: "[Course / TIL] Wanted Pre Onboarding FE Course 5주차 - 개인 과제 9 시작"
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

#### 무한 스크롤 개선

**문제점**

검색을 했을 때 이 전에 있던 검색 결과가 한번 렌더링된 후에 새로 검색된 결과가 나타났다.  
또한, 검색했을 때 page가 한번 넘어가서 결과가 처음부터 보이지 않는 현상이 있었다.

**의심 부분**

처음에 아래와 같이 구현했었다. intersect하는 구간에서 next page를 불러오는 부분을  
컴포넌트 내 state로 관리해서 검색 결과가 있을 때 다른 것을 검색했을 때 page가 건너뛰어지는 것 같다고 판단했다.

```jsx
useEffect(() => {
  const onIntersect = async ([entry], observer) => {
    // 겹침 유무 확인 => 겹칠 경우에만 실행
    if (entry.isIntersecting) {
      // 겹쳤다면 잠시 관찰 종료
      observer.unobserve(entry.target);
      // **next page (그 다음 데이터 불러오기 부분)**
      setPage((prev) => prev + 1);
      // 다시 관찰 시작
      observer.observe(entry.target);
    }
  };

  // observer 생성
  let observer;
  if (target) {
    observer = new IntersectionObserver(onIntersect, {
      threshold: 0.4,
    });
    observer.observe(target);
  }

  // unmount 시 관찰 멈추기
  return () => observer && observer.disconnect();
}, [target]);
```

**해결**
따라서 page를 store에서 관리하도록 변경했다.  
컴포넌트에서는 page를 1씩 올려주는 역할만하고, 나머지 연산(page를 set하거나 item을 page 별로 나누거나)은 reducer에서 하도록 했다.

```jsx
// reducers
  loadMore: (state, action) => {
     // 컴포넌트에서 +1을 해서 준 page
    const page = action.payload
    const { data } = current(state)
    // 전체 items을 10개씩 쪼갬
    const pageItems = data?.items.slice((page - 1) * 10, page * 10)
    // +1된 page를 전역 상태에 반영
    state.page = page
    // 가장 마지막 페이지를 계산해서 전역 상태에 반영
    state.maxPage = Math.ceil(data?.items.length / 10)
    // 무한 스크롤이므로 10개씩 기존 배열 뒤에 갖다 붙임
    state.pageItems = [...state.pageItems, ...pageItems]
  },
```

이렇게 함으로써 page가 건너뛰는 현상은 해결되었다.

추가적으로, 검색 결과가 있는 상태에서 또 검색했을 때 기존 검색 결과를 다시 보여준 다음에  
새로운 검색 결과를 보여주는 현상은 data를 담는 상태를 한 번 클리어해주어야 한다고 생각했다.

따라서 다음과 같이 했다.

```jsx
    .addCase(fetchRepos.fulfilled, (state, action) => {
      state.loading = false
      state.page = 0
      state.pageItems = []
      state.data = [] // data 넣기 전에 클리어
      state.data = action.payload
    })
```

data를 이전 것을 먼저 비워 준 후 다시 새로 fetch한 data를 할당하니 이전 결과는 보이지 않게 되었다.

**구현 결과**
![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-03/21_01.gif)

#### feedback 추가

repo를 추가하고 삭제할 때 사용자에게 알려주기 위해 feedback을 추가하고자 했고, 이를 위한 전역 state와 reducer를 만들었다.

**store state**

```jsx
const initialFeedback = {
  type: "",
  msg: "",
};

const initialState = {
  // 생략
  feedback: initialFeedback, // 추가
};
```

type에는 success, failure, notice 등등이 들어갈 것이다.  
msg는 사용자에게 보여주고 싶은 메시지를 할당할 것이다.

**reducer**

```jsx
// reducers
    addRepoToStorage: (state, action) => {
      const repo = action.payload
      const reposFromLocal = JSON.parse(localStorage.getItem("repos"))
      const { savedRepos } = current(state)

      if (reposFromLocal.find((el) => el.id === repo.id)) {
        state.feedback = {
          type: "failure",
          msg: "이미 추가하신 repo입니다.",
        }
        return
      } else if (reposFromLocal.length >= 4) {
        state.feedback = {
          type: "failure",
          msg: "최대 저장 repo를 초과하였습니다. (최대 4개)",
        }
        return
      }

      localStorage.setItem("repos", JSON.stringify([...reposFromLocal, repo]))
      state.savedRepos = [...savedRepos, repo]
      state.feedback = {
        type: "success",
        msg: "repo를 추가하였습니다.",
      }
    },
    deleteRepoFromStorage: (state, action) => {
      const { id } = action.payload
      const filteredRepos = JSON.parse(localStorage.getItem("repos")).filter((repo) => repo.id !== id)

      localStorage.setItem("repos", JSON.stringify(filteredRepos))
      state.savedRepos = filteredRepos
      state.feedback = {
        type: "success",
        msg: "repo를 삭제하였습니다.",
      }
    },
```

위와 같이 추가하거나 삭제 시 아이템과 선택된 id를 비교하여 중복된 repo 인지 혹은 배열이 저장 범위를 초과하였는지 등을 확인했다.

**사용 컴포넌트**

```jsx
// 해결 X
useEffect(() => {
  if (!feedback.msg) return;
  alert(feedback.msg);
  return () => dispatch(cleanupFeedback());
}, [feedback, dispatch]);
```

useEffect로 구현하다 보니 unmount될 때에도 alert가 동작되어 필요없는 피드백이 켜졌다. 그래서 cleanup 함수를 추가했다.

그러나 계속해서 나타남

console 로 계속 확인해본 결과 라우팅이 되어 컴포넌트가 새로 올라올 때 이미 store에 저장되어 있던  
feedback이 한 번 alert가 뜰 수 밖에 없었다.

왜냐하면, early return은 오로지 feedback msg가 비어있을 때만 return 하는데, 그 전에 feedback이 세팅이 된 상태에서  
컴포넌트가 mount된다면 early return 문을 지나서 alert이 한 번 발생하게 되었다.

따라서, 다음과 같이 cleanup을 alert 뜨기 전에 한번 해주었다.

```jsx
// 해결
useEffect(() => {
  dispatch(cleanupFeedback());
  if (!feedback.msg) return;
  alert(feedback.msg);
}, [feedback, dispatch]);
```

해결되었다!

#### Pagination 구현

**설계**

- 결과물을 6개씩 자르기
- 페이지 네비게이션 양쪽에 `<` `>`으로 움직이기
- 가운데 숫자는 3개가 보이고 나머지는 ...으로 처리
- 1 페이지와 가장 마지막 페이지는 무조건 보이도록 처리

**구현**

배열을 6개씩 잘라서 보여주려면 현재 페이지와 배열이 기본적으로 필요하다.

```jsx
// reducers
  showCurrentRepo: (state) => {
    // 선택한 repo를 localStorage에 저장해두었다.(새로고침해도 확인 가능하도록)
    const selectedRepo = JSON.parse(localStorage.getItem("selectedRepo"))
    // 꺼내온 repo와 issues 정보들을 전역 상태에 반영
    state.repo = selectedRepo.repo
    state.issues = selectedRepo.issues
    // 가장 마지막 페이지 계산
    state.totalPage = Math.ceil(selectedRepo.issues.length / 6)
    // 쪼개서 화면에 보여질 items 계산
    state.pageItems = splitIssuesByPage(selectedRepo.issues, state.page, state)
  },
```

배열을 쪼개는 함수

showCurrentRepo 뿐만 아니라 다른 reducer에도 필요한 부분이 많아 함수로 따로 만들었다.

```jsx
const splitIssuesByPage = (items, page, state) => {
  // 기본적으로 page에 따라 6개씩 자른다.
  const pageItems = items.slice((page - 1) * 6, page * 6);

  const { totalPage } = current(state);
  // page가 계산했던 가장 마지막 페이지보다 커지면 안된다.
  // 또한, 1보다 작아지면 안된다.
  // 따라서 totalPage를 가져와서 다음과 같이 if문을 구성한다.
  if (page >= state.totalPage) {
    // totalPage보다 커지지 못하도록 set 해준다.
    state.page = state.totalPage;
    // 가장 마지막 페이지는 마지막 요소 전 6개를 보여준다.
    return items.slice((totalPage - 1) * 6, totalPage * 6);
  } else if (page <= 1) {
    // 1보다 작아지지 못하도록 계속 1로 set 해준다.
    state.page = 1;
    // 첫번째 페이지는 제일 첫번째 부터 6번째까지만 보여준다.
    return items.slice(0, 6);
  } else {
    state.page = page;
    return pageItems;
  }
};
```

page를 옮기는 것은 더 쉽다.  
page만 바꿔주면 splitIssuesByPage에서 알아서 쪼개서 게시할 pageItems을 연산해준다.

```jsx
// reducers
    movePage: (state, action) => {
      const page = action.payload
      state.pageItems = splitIssuesByPage(state.issues, page, state)
    },
```

```jsx
// 실제 사용 컴포넌트
const goPrev = () => dispatch(movePage(page - 1));

const goNext = () => dispatch(movePage(page + 1));
```

그 다음, page navigation에서 숫자를 어떻게 보여줄지 고민을 했다.

pageNumbers는 [1, 2, 3, 4 ...] totalPage 만큼 들어있다.

그래서 나의 현재 페이지에서 양 옆(왼쪽, 오른쪽)은 항상 숫자가 보이도록 해야하므로  
`(el >= page - 1 && el <= page + 1)`이러한 조건을 추가한다.  
추가로 나는 가장 첫번째 페이지(1)과 가장 마지막 페이지는 항상 보이도록 설정할 것이기 때문에 `el === 1 || el === totalPage` 이 조건 또한 추가한다.

이제 세 개의 숫자 양쪽에 ...을 붙이려면 왼쪽에는 `el === page - 2 && el !== 1`,  
오른쪽에는 `el === page + 2 && el !== totalPage`의 조건을 추가하여 ...을 표시한다.

`el === page - 2` / `el === page + 2` 이 조건 모두 현재 페이지의 두 칸 옆쪽 페이지인데, 이 때부터 ...을 나타내도록 하는 것이다.  
하지만, 두 칸 옆쪽이 만약 1이거나 가장 마지막 페이지라면?  
따라서 `el !== 1` / `el !== totalPage` 조건을 추가한다.

```jsx
<PagesWrapper>
  {Array.from(pageNumbers, (el) => (
    <PageNumberBox key={el}>
      {el === page - 2 && el !== 1 && <Dots>...</Dots>}
      {((el >= page - 1 && el <= page + 1) || el === 1 || el === totalPage) && (
        <PageNumber isActive={page === el} onClick={() => goThatPage(el)}>
          {el}
        </PageNumber>
      )}
      {el === page + 2 && el !== totalPage && <Dots>...</Dots>}
    </PageNumberBox>
  ))}
</PagesWrapper>
```

**구현 결과**
![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-03/21_02.gif)

페이지네이션.. 생각보다 너무 재미있었다.

### TODO

레포 추가 제한 (4개) o  
레포 추가 시 같은 레포 block o  
레포 추가 및 삭제 시 alert o

레포 추가 후 다시 메인으로 갔을 때 load more 시 다시 처음부터 불러오는 현상 개선 o

레포 보관함에 있는 레포 선택 시 모든 issue 불러오기 o  
issue 클릭 시 url 연결 o  
issue pagenation 적용 o

검색결과 없음 추가 o

repoCard language 추가 o

렌더링 최적화  
api 호출 최적화

시간되면  
스켈레톤 UI  
피드백 alert에서 toast msg로 변경  
UI 꾸미기  
코드 이쁘게 정리

## 회고 (TIL)

**2022.03.21 Daily 회고**

✏오늘 한 일

- 개인 과제 설계 및 수행

⁉느낀 점

팀 프로젝트로 했었으면 어떤 부분을 했었을까 생각해본다.  
그래도 오랜만에 처음부터 끝까지 하려고 하니 재미는 있다.

확실히 팀 프로젝트로 많이 성장한 것 같다. 그 전에는 고려조차 하지 않았을 것들(렌더링 최적화, api 호출 최적화 등) 같은 것들을 생각하는 것을 보니  
확실히 보는 시야가 조금은 넓어졌다고 해야하지 않을까 생각한다.

팀 프로젝트도 이런데 입사하게되면 새로운 세상이 열리겠구나.

🎃현재 나의 상태

서류가 많이 떨어지지만 뛰어난 사람이 많으니 어쩔 수 없는 것으로 위안을 삼는다.  
그나마 조금 괜찮은데 들어가서 경력을 쌓는 게 먼저라는 생각이 든다!

<hr>
