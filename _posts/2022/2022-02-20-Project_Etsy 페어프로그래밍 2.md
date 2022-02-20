---
layout: single
title: "[Project] Etsy 페어 프로그래밍 2"
categories: Project
tag: [Project, React, 개인 프로젝트, Typescript, 클론, react-hook-form]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**개인 프로젝트**

## React 개인 프로젝트 (Typescript)

### Context API

#### Cart Context

**JSX.Element vs ReactNode vs ReactElement 의 차이**

클래스형 컴포넌트는 render 메소드에서 ReactNode를 리턴한다.
함수형 컴포넌트는 ReactElement를 리턴한다.

React.createElement의 return type이 ReactElement와 JSX.Element이다.

그리고 이것을 모두 포함시키는 넓은 범위가 ReactNode이다.

출처: https://simsimjae.tistory.com/426

따라서,

```jsx
interface Props {
  // children은 ReactNode 타입
  children: ReactNode;
}

const CartContextProvider = ({ children }: Props): JSX.Element => {
  return (
    <CartContext.Provider value={{ cartItems }}>
      {children}
    </CartContext.Provider>
  );
};
```

ContextProvider의 Prop으로 children이 있는데 이는 모든 React의 컴포넌트 타입이 들어갈 수 있으므로 혹은 null이나 undefinde도 됨 => ReactNode type으로 사용한다.

### 요소 filtering

#### 요소 filtering 시 naming

```jsx
useEffect(() => {
  setTabProductList(data?.filter(({ category }) => category === menu!![currentTab]));
}, [data, currentTab]);
```

항상 1줄로 줄이려고, setState 함수 안에서 무조건 filtering을 했었다.

하지만, 이는 다른 개발자들이 한 눈에 보았을 때, 어떤 filtering을 하고 있는지 알 수 없다. 배열을 filtering한 결과가 어떤 것을 나타내는 지를 알 수 있도록 명시적으로 하는 것이 좋다.

따라서 아래와 같이 Refactoring했다.

```jsx
useEffect(() => {
  const currentTabContents =
    data?.filter(({ category }) => category === menu!![currentTab]) || [];

  setTabProductList(currentTabContents);
}, [data, currentTab]);
```

filtering을 한 이유가 currentTab에 따른 컨텐츠들을 나타내주는 것이기 때문이라는 것을 변수명으로 알 수 있도록 명시적으로 작성하였다.

### formData 관리

formData는 양이 많아질수록 일일히 state 관리하기가 힘들기 때문에, form-control 라이브러리를 사용한다.

대표적으로 formik, react-hook-form를 많이 사용한다.

#### react-hook-form

이번에는 react-hook-form을 이용해서 refactoring을 하기로 했다.

- 기존 코드

  ```jsx
  // color, length, personailzation 내용을 받는 formData의 state를 선언
  const [formData, setFormData] = useState({
    color: "",
    length: "",
    personalization: "",
  });

  // input의 onChange 함수
  const handleChange = (
    e:
      | React.ChangeEvent<HTMLSelectElement>
      | React.ChangeEvent<HTMLTextAreaElement>
  ) => {
    if (!e.target.value) return;

    setFormData({
      ...formData,
      [e.target.id]: e.target.value,
    });
  };

  // itemData state > cart로 넘어갈 data
  // options에 formData가 들어간다.
  const [itemData, setItemData] =
    useState <
    CartItemProps >
    {
      seller: provider,
      image: image,
      name: title,
      options: formData,
      price: price,
      originalPrice: 0,
      discount: discount,
      shipping: 20,
      quantity: 1,
      id: id,
    };

  // formData가 변경될 때마다 itemData set
  useEffect(() => {
    setItemData((prev) => ({
      ...prev,
      options: { ...formData },
    }));
  }, [formData]);

  // return
  return (
    <S.Select id="color" onChange={handleChange}>
      <S.Option value="">select an option</S.Option>
      {finishOptions.map((option) => (
        <S.Option key={option} value={option}>
          {option}
        </S.Option>
      ))}
    </S.Select>
  );
  ```

- 리팩토링 (react-hook-form)

  ```jsx
  // react hook form의 useForm 훅
  // register : select input에 사용할 객체
  // handleSubmit : form에서 input들을 submit할 때 동작시킬 함수
  // watch : 현재 input 들을 실시간으로 확인 (getValues는 rerender되지 않고 그 때의 data만 확인)
  // dirtyFields를 이용해서 값의 유무를 확인 => error 알림에 사용
  const {
    register,
    handleSubmit,
    watch,
    formState: { dirtyFields },
  } = useForm<FormData>();

  const onSubmit: SubmitHandler<FormData> = data => {
    console.log(data);
  };

  const formData = watch();
  const { personalization } = formData;

  const addToCart = () => {
    if (!dirtyFields.color || !dirtyFields.length) {
      setHasError(true);
      return;
    }

    // itemData를 state로 선언하지 않고 cart에 넘겨주기 전에 선언한다.
    // 굳이 rerender가 되지 않아도 되는 data들이기 때문에
    // useState hook을 사용하지 않는 것이 나을 것 같다.
    const itemData = {
      seller: provider,
      image: image,
      name: title,
      options: formData,
      price: price,
      originalPrice: 0,
      discount: discount,
      shipping: 20,
      quantity: 1,
      id: id,
    };

    addItemtoCart?.(itemData);
    history.push('/cart');
  };

  // return
  return (
    <S.ProductSelector onSubmit={handleSubmit(onSubmit)}>
      <S.SelectWrapper>
        <S.NormalName>Color</S.NormalName>
        <S.Select {...register('color', { required: 'required' })}>
          <S.Option value="">select an option</S.Option>
          {finishOptions.map(option => (
            <S.Option key={option} value={option}>
              {option}
            </S.Option>
          ))}
        </S.Select>
      </S.SelectWrapper>
      <S.SelectWrapper>
        <S.NormalName>Length</S.NormalName>
        <S.Select
          id="length"
          {...register('length', { required: 'required' })}
        >
          <S.Option value="">select an option</S.Option>
          {lengthOptions.map(option => (
            <S.Option key={option} value={option}>
              {option}
            </S.Option>
          ))}
        </S.Select>
      </S.SelectWrapper>
      {hasError && <span>Color, Length selections are required</span>}
    </S.ProductSelector>
  )
  ```

### 기타 할일

배포 (github actions 사용)

read me 작성

<hr>
