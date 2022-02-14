---
layout: single
title: "[Project] Etsy Main 페어 프로그래밍 1"
categories: Project
tag: [Project, React, 개인 프로젝트, Typescript, 클론, swr, generic]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**개인 프로젝트**

## React 개인 프로젝트 (Typescript)

### 페어 프로그래밍

#### 렌더링 최적화 및 초기 로딩 시간 단축

이미지 및 svg management만 해도 로딩시간을 많이 단축할 수 있다.

jpg png 말고 webp 사용하면 많이 단축

(현재는 링크로 사용하기 때문에 상관없는데 실제 jpg 다운받을 때는 webp 하면 초기 로딩속도 엄청 줌)

lazy load로 렌더링 최적화 height 뿐만 아니라 용량 큰 이미지에도 wrap해서 사용할 수 있음

#### img

img는 width height 주지 말고 그 상위에 div 만들어서 width와 height 크기를 주고,

img 요소에는 width 100%, height 100%을 주고 object-fit cover를 적용하여 가운데에 오도록 한다.

보통 img 요소에는 크기를 직접 주지 않는 것이 좋다.

#### constant

이것들은 data.ts로 처리

유관한 디렉토리에서만 사용하도록 (공용에서 쓰는거 아니면)

#### styles

zindex, color 등 공용으로 사용되는 css 속성들은

따로 ts 파일에 선언해두고 사용하는 것이 편하고 유지보수에도 용이하다.

#### swr

항상 처음 사용하는 라이브러리들은 공식문서를 먼저 확인하고, 개념이나 기본적인 동작 및 사용법을 익힌 후에 best practice를 검색하여 학습하는 것이 좋다.

- SWR이 나오게 된 계기

  기존에는 상위 컴포넌트에서 일괄적으로 Data를 useEffect를 이용해서 fetching 하고 가져온 data들을 하위 컴포넌트 들에게 props로 넘겨주는 방식을 사용했다.

  하지만, props drilling 문제와 하위 컴포넌트에서 어떤 데이터가 필요해질지 모르기 때문에 유지 보수 측면에서 좋지 않은 방법이다.

  그래서 swr을 이용해서 커스텀 hook을 만들고, 거기서 딱 한번 api request를 해서 data를 어느 컴포넌트에서나 사용할 수 있도록 하는 것이다.

  이로써 전역 상태 관리가 아닌데도 불구하고 서버에서 가져온 data들을 어느 컴포넌트에서든 사용할 수 있게 된다.

- 장점

  유지보수가 쉬워진다. 데이터 fetching 하는 곳은 딱 한 곳 custom hook 이기 때문에 잘 못된 곳을 금방 찾을 수 있게 될 것이다.

  자동으로 서버와 로컬의 데이터 동기화, useEffect 처럼 수시로 데이터를 요청하는 것이 아니라 automatic revalidation을 통해 탭 이동, 잠금모드 해제 시, 페이지 포커싱 될 시, 데이터가 변경되었을 시 등등 폴링 포인트가 있어 자동으로 서버에 있는 데이터를 로컬과 맞춰줄 수 있어 좋다. (만약 데이터가 절대로 바뀌지 않는다면 immutable을 사용할 수도 있다.)

  코드가 깔끔해진다. redux를 사용했을때와 다르게 장황한 코드들이 없다. fetcher와 useSWR만 있으면 data fetching이 끝난다. redux였다면 store 생성하고, middleware 코드, dispatch 코드 등등 생겨나야 할 것들이 매우 많다.

- 사용법

  - global swr fetcher 정의 (index.tsx / 가장 상위 파일)

    ```jsx
    import { SWRConfig } from "swr";
    import axios from "axios";

    // swr global config fetcher

    const API = axios.create({
      baseURL: "http://localhost:3000/data/",
      headers: {
        "content-type": "application/json;charset=UTF-8",
        accept: "application/json",
      },
    });

    const getData = (url: string) =>
      API.get(url)
        .then((response) => response.data)
        .catch((err) => console.error(err));

    const fetcher = async (url: string) => await getData(url);

    ReactDOM.render(
      <React.StrictMode>
        <SWRConfig value={{ fetcher }}>
          <Router>
            <App />
          </Router>
        </SWRConfig>
      </React.StrictMode>,
      document.getElementById("root")
    );
    ```

    url을 인수로 받아서 axios get을 실행해주도록 하는 getData 함수 (fetcher)를 정의한다.

    그 fetcher를 모든 컴포넌트에서 사용할 수 있도록 value로 넘겨준다.

  - swr 요청을 해주는 custom hook

    ```jsx
    import useSWR from "swr";

    interface DataResponse<T> {
      data: T;
      menu?: string[];
    }

    export function useRequest<T>(url: string) {
      const { data: result, error } = useSWR < DataResponse < T >> url;

      // error 예외처리
      if (error) console.log(error);

      return { data: result?.data, menu: result?.menu };
    }
    ```

  - 각각의 data fetching하는 custom hook

    useRecentView

    ```jsx
    import { useEffect, useState } from 'react';
    import { useRequest } from '../../../hooks/useRequest';
    import { ProductList } from '../types';

    function useRecentView() {
      const { data } = useRequest<ProductList[]>('productList.json');
      const [recentViewsData, setRecentViewsData] = useState<ProductList[]>();

      useEffect(() => {
        setRecentViewsData(data?.filter(({ viewed }) => viewed));
      }, []);

      return { recentViewsData };
    }

    export default useRecentView;
    ```

    useRequest에 url이 필요하므로 각 custom hook 마다 맞는 url을 입력한다.

    data를 받고, 그 data를 토대로 가공을 해서 return 해준다.

    여기서 가공을 하게 되면, 이 데이터를 사용하는 컴포넌트에서 state를 따로 생성하지 않아도 되고, useEffect hook을 따로 사용하지 않고, render 문에서도 굳이 filtering을 해주지 않아도 되어 굉장히 깔끔해진다.

  - custom hoook을 사용하는 컴포넌트

    ```jsx
    function RecentList() {
      const { recentViewsData } = useRecentView();
      const history = useHistory();

      const goToDetail = (id: number) => history.push(`/detail/${id}`, { id });

      return (
        <S.RecentListWrap>
          <S.RecentTitle>
            <S.RecentLeft>Recently viewed & more</S.RecentLeft>
            <S.RecentRight>Show more from the ivoryMR shop</S.RecentRight>
          </S.RecentTitle>
          {recentViewsData?.map(({ id, price, imageUrl }) => (
            <ImageCard
              key={id}
              width={250}
              height={167}
              price={price}
              image={imageUrl}
              onClick={() => goToDetail(id)}
            />
          ))}
        </S.RecentListWrap>
      );
    }
    ```

    custom hook만 사용하면 useState, useEffect 모두 필요없고, 바로 사용 가능하며 data도 특정 시기(탭 이동, 잠금화면 해제, 서버 데이터 변경 등등)마다 데이터가 자동으로 캐싱되므로 깔끔한 코드를 볼 수 있다.

#### generic

**제네릭(generic)이란?**

```
클래스나 함수의 타입을 미리 못 박아 지정해두는 것이 아니라,
이 클래스나 함수를 사용할 때에 결정할 수 있도록 하는 프로그래밍 기법이다.
```

제네릭을 이용하면 정적 언어인 java, c++, typescript에서도 범용적인 함수, 클래스를 사용할 수 있게 된다.

**기본 예시)**

```jsx
function getText<T>(text: T): T {
  return text;
}
```

보통 T로써 제네릭 타입을 나타낸다.

```jsx
getText < string > "hi";
getText < number > 10;
getText < boolean > true;
```

이런 식으로 getText를 사용할 때에 T에 들어갈 타입을 지정해주어서 사용한다.

**내 코드에서 generic 사용**

처음에 generic 자체의 개념이 익숙치가 않아서 쓸 줄은 알아도 어떻게 한다는 건지 감을 못잡았다.

역시 삽질을 해봐야 머리 속에 각인이 되는 것인가..

여러번의 시도(generic 때문만은 아님) 중 generic을 조금씩 알게 되었다.

- useRequest (swr custom hook)

  ```jsx
  interface DataResponse<T> {
    data: T;
    menu?: string[];
  }

  export function useRequest<T>(url: string) {
    const { data: result, error } = useSWR < DataResponse < T >> url;

    // error 예외처리
    if (error) console.log(error);

    return { data: result?.data, menu: result?.menu };
  }
  ```

  위와 같이 useSWR의 response는 어떤 데이터(url)를 불러오느냐에 따라서 type이 달라진다.

  type을 지정해주지 않아도 사용은 가능하지만, any로 전달이 되므로 좋지 않다.

  따라서 useRequest를 사용할 때 type이 그때그때 달라지도록 설정해야 한다.

  여기서 헤메었다.

  그냥 T를 받아서 useSWR에 T를 넣으면 될 줄 알았지만, 그렇지 않았다.

  useSWR의 response가 객체 안에 여러 속성이 있고, 그 중 data 안에 또 data라는 속성이 있고, 그 안에 정해진 데이터 객체들이 배열로 있는 형태이다.

  그렇기 때문에 그냥 useSWR<<T>, any>를 백날 해봐야 response 가장 상위 객체에는 우리가 정해둔 객체가 존재 하지 않는다.

  ```json
  {
    config: {transitional: {…}, transformRequest: Array(1), transformResponse: Array(1), timeout: 0, adapter: ƒ, …}
    data: {data: Array(5)}
    headers: {accept-ranges: 'bytes', access-control-allow-headers: '*', access-control-allow-methods: '*', access-control-allow-origin: '*', cache-control: 'public, max-age=0', …}
    request: XMLHttpRequest {onreadystatechange: null, readyState: 4, timeout: 0, withCredentials: false, upload: XMLHttpRequestUpload, …}
    status: 200
    statusText: "OK"
  }
  ```

  따라서 다음과 같이 data 프로퍼티에 T를 정해준다.

  ```jsx
  interface DataResponse<T> {
    data: T;
    menu?: string[];
  }
  ```

  data에는 특정type[]이렇게 들어갈 것이다.

### 기타 할일

피드백 대로 코드 수정 o

Main 페이지 UI layout 완성하기 (grid, logo 및 이미지, 배경) => ApplyingGrid 브랜치에 있는 것 머지 후 style 등 수정할 것 수정하기

ToolTip 마무리 => ApplyingGrid 브랜치에 있음

Hover 하면 after에서 그림자 숙 나오는거 컴포넌트 화 (너무 많이 쓰임)

Detail 페이지 하기

<hr>
