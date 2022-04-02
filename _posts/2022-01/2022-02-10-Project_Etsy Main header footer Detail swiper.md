---
layout: single
title: "[Project] Etsy 프로젝트 Main 페이지 Header/Footer, Detail 페이지 캐러셀"
categories: Project
tag: [Project, React, 개인 프로젝트, Typescript, 클론, swiper]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**개인 프로젝트**

## React 개인 프로젝트 (Typescript)

### Main 페이지

#### Header, Footer

마크업

### Detail 페이지

### swiper

swiper는 현재

fade effect를 제외하면 되지만, effect를 적용하는 순간 동작하지 않는다.

css를 import 하지 않은 것이 있었다 (fade 관련)

구글에 하는 방법 치지 말고, 공식문서를 "먼저" 보자

일단, 해당 사진 처럼 구현하고자 했다.

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-02/10_01.JPG)

큰 이미지가 있고, 모든 이미지들의 썸네일을 navigation을 사용할 수 있는 형태이다.

이 부분은 swiper에서 쉽게 구현이 가능하다.

Swiper 사이트 Demo에 보면 Thumbs gallery loop 라는 형태를 기본으로 커스텀하면 된다.

```jsx
<Swiper
  {...ThumbSwiperSettings}
  onSwiper={setThumbsSwiper}
  className="mySwiper"
>
  {images?.map((imageUrl: string, index: number) => (
    <SwiperSlide key={imageUrl}>
      <img src={imageUrl} alt={`preview${index}`} />
    </SwiperSlide>
  ))}
</Swiper>
<Swiper
  {...MainSwiperSettings}
  thumbs={{
    swiper: thumbsSwiper,
  }}
  className="mySwiper2"
>
  {images?.map((imageUrl: string, index: number) => (
    <SwiperSlide key={imageUrl}>
      <img src={imageUrl} alt={`preview${index}`} />
    </SwiperSlide>
  ))}
</Swiper>
```

첫번째 Swiper가 thumbNail을 나타내는 swiper이다.

그리고, 두번째 Swiper가 큰 이미지로 나타나는 swiper이다.

기본적인 Swiper 속성은 다음과 같다

```jsx
const swiperSettingType = {
  effectType: 'fade',
  slidesPerView: 'auto',
  direction: 'vertical',
} as const;

const ThumbSwiperSettings = {
  spaceBetween: 10,
  slidesPerView: swiperSettingType.slidesPerView,
  watchSlidesProgress: true,
  modules: [Navigation, Thumbs],
  direction: swiperSettingType.direction,
};

const MainSwiperSettings = {
  loop: true,
  spaceBetween: 5,
  modules: [FreeMode, Navigation, Thumbs, EffectFade],
  navigation: true,
  effect: swiperSettingType.effectType,
  fadeEffect: { crossFade: true },
  speed: 500,
  watchSlidesProgress: true,
  allowTouchMove: false,
  lazy: true,
};
```

썸네일 Swiper에는 direction을 수직으로 바꾸고, Navigation과 Thumbs 모듈을 import 하여 넣어준다.

slidesPerView는 각 데이터 마다 이미지 개수가 다르기 때문에 auto로 해준다.

Main Swiper에는 FreeMode, Navigation, Thumbs, EffectFade를 import 해주고,

effect와 fadeEffect를 추가한다.

여기서 내가 빠뜨린 것이

```jsx
import "swiper/css/effect-fade";
```

fade effect css 파일이다. fade가 css가 적용이 안되었으니 동작하지 않는 것이 당연했다.

나는 일단 구현하는 방법을 구글에 치고 따라했었는데, 그게 아니라 일단 공식문서를 확인해서 해당 기능을 어떻게 사용하는지 기본적으로 판단하는 것이 먼저라고 생각이 들었다.

당연한거지만... 하다 보면 왜 자꾸 그냥 구글링을 하게 되는지 모르겠다. ㅠ

추가적으로 thumbSwiper state를 사용하는데, typescript에서 계속 onSwiper 속성에 Error를 띄웠다.

```
Type 'Dispatch<SetStateAction<undefined>>' is not assignable to type '(swiper: Swiper) => void'.
  Types of parameters 'value' and 'swiper' are incompatible.
    Type 'Swiper' is not assignable to type 'SetStateAction<undefined>'.
      Type 'Swiper' provides no match for the signature '(prevState: undefined): undefined'.
```

이유는 onSwiper에 들어가는 setThumbsSwiper 이 함수의 타입이 React.Dispatch<React.SetStateAction<undefined>> 인데,

onSwiper의 타입은 IntrinsicAttributes & SwiperProps & { children?: ReactNode; } 이것이라고 나와있다.

```jsx
const [thumbsSwiper, setThumbsSwiper] = React.useState<SwiperCore>();
```

그래서 SwiperCore 타입을 import 해서 thumbsSwiper useState에 넣어준다.

### 기타 할일

피드백 대로 코드 수정

Main 페이지 UI layout 완성하기 (grid)

Main 페이지 중복되는 컴포넌트 템플릿화 (근데 이렇게 하는 게 맞는건가)

<hr>
