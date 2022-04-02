---
layout: single
title: "[Project] Etsy Main 페이지 마무리 및 Detail 페이지"
categories: Project
tag: [Project, React, 개인 프로젝트, Typescript, 클론]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**개인 프로젝트**

## React 개인 프로젝트 (Typescript)

### Main 페이지

#### Grid

```jsx
<S.TabContents>
  {tabProductList?.map(({ imageUrl, price }, index) => (
    <S.ImageCardWrapper
      key={imageUrl}
      index={index}
      className={S.gridIndex[index]}
    >
      <ImageCard width="100%" height="100%" price={price} image={imageUrl} />
    </S.ImageCardWrapper>
  ))}
</S.TabContents>
```

TabContents안에 tabProductList 요소들이 6개가 들어가고, 그 6개를 다음과 같이 정렬해야 했다.

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-02/15_01.JPG)

먼저 grid를 나누어 보면 작은 그림이 큰 그림의 1/2이다. 그러면, 총 column grid는 6개, row는 2개가 필요하다고 생각했다.

그래서 먼저 template을 나누어 준다!

```jsx
export const TabContents = styled.div`
  margin-top: 18px;
  display: grid;
  grid-template-rows: repeat(2, 1fr);
  grid-template-columns: repeat(6, 1fr);
  grid-template-areas:
    "one one two four four five"
    "one one three four four six";
  gap: 18px;
  transform: translateX(-10px);
  transition: all 0.2s ease-in-out;
`;
```

크기는 일정하므로 repeat을 이용해서 1fr으로 각 개수 2, 6 만큼 template을 나누었다.

그 다음, 이제 첫번째, 네번째 요소는 큰 이미지이어야 하고, 나머지 요소들은 큰 이미지의 오른쪽에 위아래로 위치하도록 해야한다.

grid에는 grid-area를 이용해서 이름을 정하고, 직관적으로 grid 배치할 수 있는 속성이 있다.

그래서 index를 받아와서 바로 grid-area에 이름이랍시고 넣었지만, 실패. 그 이유는 grid-area 의 값에 1, 1 / 1 / 2 / 4 등 해당 grid 요소가 사방으로 어디까지 차지하게 할 것인지를 정할 수 있기 때문에 number 값이 들어가면 내가 원하는 grid-template-areas를 사용할 수 없었다.

따라서 요소마다 index 0일 때 "one", 1일 때 "two"... 차례대로 className을 붙여주었다. 그 className을 기반으로

grid-template-areas에서

```
"one one two four four five"
"one one three four four six"
```

위와 같이 입력해준다. 첫번째와 네번쨰는 작은 이미지가 네 개 들어갈 정도의 크기를 차지하도록 한다. 아주 직관적이어서 사용하기 쉬울 것 같다고 생각되나, 이렇게 className을 위한 배열을 또 선언하고, map 돌 때 적용해주는 것이 좋을지는 찾아봐야 할 것 같다.

#### Main 페이지 마무리

메인페이지 완료

### Detail 페이지

#### section 분리

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-02/15_02.JPG)

이미지 슬라이더 부분, 오른쪽 제품 정보, review 부분 이렇게 크게 세 가지 section으로 구분될 수 있을 것 같아 바로 sections 폴더에 모아서 구현했다.

```jsx
<S.DetailWrapper>
  <S.DetailLeft>
    <ImageSection images={data?.images} />
    {data && <ReviewSection reviewCount={data.itemReviews.length} />}
  </S.DetailLeft>
  <S.DetailRight>
    {data && (
      <SideInformation
        provider={data.provider}
        title={data.title}
        sold={data.sold}
        price={data.price}
        finishOptions={data.finishOptions}
        lengthOptions={data.lengthOptions}
      />
    )}
  </S.DetailRight>
</S.DetailWrapper>
```

이미지 슬라이더 부분과 리뷰 부분이 왼쪽에 위치, 제품 정보란이 오른쪽에 오도록 구성했다.

#### Image initial slide

Swiper 자체는 정상적으로 동작하나 처음 로드될 시 처음 보이는 slide가 가장 첫번쨰 것이 아닌 문제가 있었다.

thumbSwiper와 mainSwiper가 서로 맞지 않는 문제도 있었다.

먼저, initialSlides 속성을 추가하였다.

```jsx
export const mainSwiperSettings = {
  loop: true,
  spaceBetween: 5,
  slidesPerView: swiperSettingType.slidesPerView,
  modules: [FreeMode, Navigation, Thumbs, EffectFade],
  navigation: true,
  effect: swiperSettingType.effectType,
  fadeEffect: { crossFade: true },
  speed: 500,
  watchSlidesProgress: true,
  allowTouchMove: false,
  lazy: true,
  initialSlide: 0, // 추가
};
```

하지만 전혀 적용이 되지 않았고, 찾아본 결과 loop true를 사용할 때 loopedSlides를 설정해주어야 한다는 것을 보고

공식 문서에 가서 찾아보았다.

그러고 써있는 설명:

```
loop: If you use it along with slidesPerView: 'auto' then you need to specify loopedSlides parameter with amount of slides to loop (duplicate).
```

```
loopedSlides: If you use slidesPerView:'auto' with loop mode you should tell to Swiper how many slides it should loop (duplicate) using this parameter
```

결론은 slidesPerView를 auto로 사용할 때, loopedSlides에는 loop를 몇개의 슬라이드가 돌아갈 것인지를 정해주어야 한다고 적혀있다.

```jsx
<Swiper
  {...mainSwiperSettings}
  thumbs={{
    swiper: thumbsSwiper,
  }}
  loopedSlides={images?.length ?? 1}
  className="mySwiper2"
>
```

loop의 개수가 동적이므로 data로 바로 설정하지 않고, 컴포넌트에서 설정해주었다. images.length가 undefined일 때에는 그냥 1로 두도록 한다 (어차피 1로 가지 않을테니)

이후 최초 로드 시에도 첫번째 슬라이드를 보여주는 것을 확인할 수 있다.

### 기타 할일

Main 페이지 UI layout 완성하기 (grid, logo 및 이미지, 배경) => ApplyingGrid 브랜치에 있는 것 머지 후 style 등 수정할 것 수정하기

ToolTip 마무리 => ApplyingGrid 브랜치에 있음

Detail 페이지 하기 o

Detail 페이지 personalization 하기

<hr>
