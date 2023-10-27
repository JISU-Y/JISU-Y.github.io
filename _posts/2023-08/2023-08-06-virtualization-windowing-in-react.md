---
layout: single
title: "[React] Virtualization/Windowing in React"
categories: React
tag: [React, hooks]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Virtualization/Windowing in React

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 회사 프로덕트에 채팅 기능이 있는 프로덕트가 있는데, 채팅방 로드 시 로딩이 꽤 길어 페이지네이션(혹은 무한 스크롤)과 성능 향상을 위한 작업을 하고 싶었다.

그에 따라 가상화(혹은 windowing) 기법을 적용하고자 알아보게 되었다.

</aside>

## 가상화 (virtualization / windowing)

> 가상화 혹은 윈도윙 기법은 대형 테이블/리스트를 효율적으로 보여줄 수 있도록 하는 기법.
>
> 사용자에게 보이는 것만 렌더링하는 개념.

### 🤷 사용하는 이유?

보여주어야 할 목록 리스트가 무겁고 매우 많은 경우, 모든 데이터를 가지고 렌더링 하면 오래 걸리게 된다.

왜냐하면, 최근 모던 앱에서 흔하게 구현되는 요소들을 보자면

- interactive 한 버튼이 있다거나
- 무거운 이미지/영상 등 컨텐츠가 있다거나
- transition/animation이 있는

등의 비싼 동작을 가진 요소를 모두 렌더하는 것은 무리가 있다.

따라서, 사용자가 보는 화면 (viewport) 기준으로만 요소를 보여주면 훨씬 성능이 좋아진다.

### ☝️ 특징

스크롤 되면, **스크롤바 위치에 따라** 어떤 요소를 디스플레이 해야 하는지를 계산하고,  
viewport에 들어오거나 나가는 요소들을 **DOM 노드에 추가하거나 제거**하거나 하게 된다.

**react 공식 문서**

공식 문서에서도 최적화 중 긴 목록을 렌더링하는 경우 windowing 기법을 사용하는 것을 안내해주고 있다.

대표적인 windowing 라이브러리로는 [react-window](https://github.com/bvaughn/react-window), [react-virtualized](https://github.com/bvaughn/react-virtualized)가 있다.

> If your application renders long lists of data (hundreds or thousands of rows), we recommend using a technique known as “windowing”. This technique only renders a small subset of your rows at any given time, and can dramatically reduce the time it takes to re-render the components as well as the number of DOM nodes created.
>
> [출처]: [react docs](https://legacy.reactjs.org/docs/optimizing-performance.html#virtualize-long-lists)

장점

- 렌더링 성능 향상: 보이는 곳만 렌더링하므로
- 메모리 사용 감소: 필요한 아이템만 렌더링하므로 메모리 사용량 줄어듦.
- 부드러운 스크롤링: 대량의 데이터가 있는 리스트에서도 스크롤링이 부드럽게 처리.

### 🪤 가상화 활용

- 수천, 수만건 테이블 혹은 리스트
- 이미지가 포함된 무거운 리스트
- 채팅 리스트 (이미지나 영상이 있는 경우가 많고, 텍스트 리스트도 매우 많음)  
  → 실제로 어떻게 최적화할 것인지 면접에서 질문받은 적도 있었음.
- 웹에서 horizontal swipeable views (개인적인 궁금함. 이걸로도 할 수 있을까?)
  - react-swipeable-views에서 필요한 경우에만 렌더링되도록 처리가 되고, onSwitching 등 이벤트를 감지할 수 있어 가상화 없이 구현 가능하다.
  - 그래도 구현하고자 한다면 react-window도 충분히 사용 가능할 것.

## 가상화 실습

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
☝ 구현 앱

강아지 종류를 선택하면 해당 강아지 종의 이미지와 이름, 번호를 가진 리스트를 나열하는 웹 앱

</aside>

### 🙅‍♂️ 가상화 적용하지 않은 경우

```jsx
import React, { useState, useEffect } from "react";
import axios from "axios";
import "./DogImageApp.css";

function DogImageApp() {
  const [breeds, setBreeds] = useState([]);
  const [selectedBreed, setSelectedBreed] = useState("");
  const [images, setImages] = useState([]);

  useEffect(() => {
    axios
      .get("https://dog.ceo/api/breeds/list/all")
      .then((response) => {
        const breedNames = Object.keys(response.data.message);
        setBreeds(["all", ...breedNames]);
      })
      .catch((error) => {
        console.error("Error fetching breeds:", error);
      });
  }, []);

  useEffect(() => {
    if (selectedBreed === "all") {
      const fetchAllImages = async () => {
        try {
          const allImagePromises = breeds.map((breed) => {
            if (breed !== "all") {
              return axios.get(`https://dog.ceo/api/breed/${breed}/images`);
            }
            return null;
          });

          const allImagesResponses = await Promise.all(allImagePromises);
          const allImagesData = allImagesResponses.reduce((acc, response) => {
            if (response) {
              return [...acc, ...response.data.message];
            }
            return acc;
          }, []);

          setImages(allImagesData);
        } catch (error) {
          console.error("Error fetching images:", error);
        }
      };

      fetchAllImages();
    } else if (selectedBreed) {
      axios
        .get(`https://dog.ceo/api/breed/${selectedBreed}/images`)
        .then((response) => {
          setImages(response.data.message);
        })
        .catch((error) => {
          console.error("Error fetching images:", error);
        });
    }
  }, [selectedBreed, breeds]);

  const handleBreedSelect = (event) => {
    setSelectedBreed(event.target.value);
  };

  return (
    <div className="app-container">
      <h1>Dog Image App</h1>
      <select onChange={handleBreedSelect}>
        <option value="">Select a breed</option>
        {breeds.map((breed) => (
          <option key={breed} value={breed}>
            {breed}
          </option>
        ))}
      </select>
      <div className="card-container">
        {images.map((image, index) => {
          const uniqueName = image.split("/").pop();
          return (
            <div key={index} className="card">
              <img src={image} alt={`Dog ${uniqueName}`} />
              <p>{`Dog ${uniqueName}`}</p>
              <span className="number">{index + 1}</span>
            </div>
          );
        })}
      </div>
    </div>
  );
}

export default DogImageApp;
```

일부러 “all” 선택지를 이용하여 모든 breed에 해당하는 모든 이미지를 가져오도록 했다.

모든 이미지를 가져오는 api는 없어서 반복하여 요청하는 것은 어쩔 수 없었고,  
대신 promise all 메서드를 이용해서 병렬적으로 처리하게끔 했다.

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-08/06-dog-list-no-windowing.gif)

10000개가 넘는 리스트라 스크롤 하면 확실히 느려졌다.

스크롤 했을 때 그 위치에서 바로 이미지를 가져오지 못하고 한참이 지나야 했다.

이 때 개발자 도구에서 요소를 확인하면 모든 요소가 잡힌다. 그래서 엄청 느려진다.

이럴 때 사용자의 view port를 제외한 데이터들은 가상화를 통해 성능 개선을 기대하면 좋을 것 같다.

일단, 현재 구현된 것은 리스트 카드들의 height가 일정하지 않기 때문에, FixedSizeList를 사용할 수 없겠다.

### 🙆‍♂️ 가상화를 적용한 경우

**Fixed Size List**

height가 고정되어 있으면 사용하기 매우 쉽다.

여기서 size는 리스트를 담는 container의 size를 말한다.

```jsx
<FixedSizeList height={500} width={500} itemSize={120} itemCount={items.length}>
  {Row} // list component
</FixedSizeList>
```

이 컨테이너가 사용자가 사이즈를 변경시킬 때 마다 변해야 한다면 이를 사용하기 어렵다.

→ 주의! 저 height는 inline으로 설정되기 때문에 css에서 height를 줘봤자 적용되지 않는다.

**Variable Size List**

`react-window`에는 height를 자동으로 계산해주는 메서드 및 컴포넌트가 없다. (`react-virtualized`에는 있다.)

따라서 `react-virtualized-auto-sizer`를 추가해준다.

- AutoSizer는 사용자의 viewport를 계산해주어 width와 heigth가 변화할 때마다 넣어줄 수 있다.
- Fixed Size List 에서 고정으로 넣어주었던 값을 width, heigth prop으로 넘겨주면 된다.

```jsx
import React, { useState, useEffect } from "react";
import axios from "axios";
import { VariableSizeList as List } from "react-window"; // Import VariableSizeList
**import AutoSizer from "react-virtualized-auto-sizer";**
import "./DogImageApp.css";

function VirtualizedDogImageList() {
  const [breeds, setBreeds] = useState([]);
  const [selectedBreed, setSelectedBreed] = useState("");
  const [images, setImages] = useState([]);
  const [imageSizes, setImageSizes] = useState({}); // Store image sizes

  useEffect(() => {
    axios
      .get("https://dog.ceo/api/breeds/list/all")
      .then((response) => {
        const breedNames = Object.keys(response.data.message);
        setBreeds(["all", ...breedNames]);
      })
      .catch((error) => {
        console.error("Error fetching breeds:", error);
      });
  }, []);

  useEffect(() => {
    if (selectedBreed === "all") {
      const fetchAllImages = async () => {
        try {
          const allImagePromises = breeds.map((breed) => {
            if (breed !== "all") {
              return axios.get(`https://dog.ceo/api/breed/${breed}/images`);
            }
            return null;
          });

          const allImagesResponses = await Promise.all(allImagePromises);
          const allImagesData = allImagesResponses.reduce((acc, response) => {
            if (response) {
              return [...acc, ...response.data.message];
            }
            return acc;
          }, []);

          setImages(allImagesData);
        } catch (error) {
          // console.error("Error fetching images:", error);
        }
      };

      fetchAllImages();
    } else if (selectedBreed) {
      axios
        .get(`https://dog.ceo/api/breed/${selectedBreed}/images`)
        .then((response) => {
          setImages(response.data.message);
        })
        .catch((error) => {
          console.error("Error fetching images:", error);
        });
    }
  }, [selectedBreed, breeds]);

  useEffect(() => {
    const fetchImageSizes = async () => {
      const sizes = await Promise.all(
        images.map(async (image) => {
          const response = await axios.head(image); // Fetch image metadata
          const contentLength = response.headers["content-length"];
          return contentLength ? parseInt(contentLength, 10) : 0;
        })
      );

      const sizesObject = {};
      sizes.forEach((size, index) => {
        sizesObject[index] = size / 100;
      });

      setImageSizes(sizesObject);
    };

    fetchImageSizes();
  }, [images]);

  const handleBreedSelect = (event) => {
    setSelectedBreed(event.target.value);
  };

  const Row = ({ index, style }) => {
    const image = images[index];
    if (!image) return null;
    const uniqueName = image.split("/").pop().replace(".jpg", "");
    const cardHeight = imageSizes[index] || 200;

    return (
      <div style={{ ...style, height: cardHeight - 20 }} className="card">
        <img src={image} alt={`Dog ${uniqueName}`} />
        <p>{`Dog ${uniqueName}`}</p>
        <span className="number">{index + 1}</span>
      </div>
    );
  };

  return (
    <div className="app-container">
      <h1>Dog Image App</h1>
      <select onChange={handleBreedSelect}>
        <option value="">Select a breed</option>
        {breeds.map((breed) => (
          <option key={breed} value={breed}>
            {breed}
          </option>
        ))}
      </select>

      <AutoSizer>
        {({ width, height }) => {
          if (selectedBreed && !imageSizes?.[0]) return <div>loading...</div>;

          return (
            <List
              width={width}
              height={height}
              itemCount={images.length}
              itemSize={(index) => imageSizes[index] || 200}
            >
              {Row}
            </List>
          );
        }}
      </AutoSizer>
    </div>
  );
}

export default VirtualizedDogImageList;
```

windowing을 적용하면 개발자 도구에서 요소도 딱 viewport에서 보여지고 있는 요소들만 올라와 있는 걸 확인할 수 있고,

최적화되어 좀 더 이미지가 빨리 로딩된다.

## 무조건 가상화?

그래서 무조건 가상화가 좋은가?

역시 여느 기술이나 기법과 마찬가지로 무조건 좋은 것은 없다.

**단점:**

1. **복잡성 증가:** 가상화를 구현하는 것은 추가적인 코드와 로직을 필요.
   - 이는 라이브러리가 생각보다 어렵지 않으므로 괜찮은 정도인 것 같다. (개발 공수와 성능 향상 가성비를 따지자면 괜찮은 듯)
2. **몇몇 특정한 경우에만 적합:** 작은 데이터셋이나 정적인 리스트의 경우에는 가상화가 오히려 성능 저하.
   - AutoSizer 등 지속적으로 viewport의 width, heigth를 계산하는 경우 좋지 않은 성능을 줄 수도.
3. **레이아웃 문제:** 아이템의 크기를 고정하거나 계산을 해주어야 하기 때문에 신경쓰지 않으면 좋지 않은 UI가 나올 수도 있다.
4. 초기 **로딩 속도**
   - 지금 예제에서도 이미지의 사이즈를 먼저 계산하므로 그냥 뿌려줄 때보다 초기 렌더가 늦다.

### 개인적인 의견

생각보다 단순히 그려주는 리스트는 수천개 까지는 생각보다 괜찮은 로딩을 보여주는 것 같다.

(물론 단순한 정도도 다 다르겠지만, 이번에 구현한 이미지, 텍스트 정도면 몇천개 정도는 솔직히 그냥 아무 문제 없이 스크롤되더라.)

대신 몇 만개가 넘어가면 조금 버벅였다.

또한 요소를 검사하려고 할 때 정말 오래 걸렸다.

확실히 수천 개 리스트라면 가상화를 적용하면 훨씬 좋을 것 같다.

DOM이 렌더하여 보여주고 있는 것이 진짜로 몇개 되지 않기 때문에, 디버깅도 수월해질 것이고

쾌적한 사용을 할 수 있겠다.

### 참고 링크

[virtualize-long-lists-react-window](https://web.dev/i18n/ko/virtualize-long-lists-react-window/)

[react-performance-optimization-windowing](https://blog.logrocket.com/react-performance-optimization-windowing-vs-component-recycling/)

<hr>
