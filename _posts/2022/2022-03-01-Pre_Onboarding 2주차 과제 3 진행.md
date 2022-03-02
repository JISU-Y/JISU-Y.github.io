---
layout: single
title: "[Course / TIL] Wanted Pre Onboarding FE Course 2주차 - 팀 과제 3 진행"
categories: Course
tag: [Course, TIL, Wanted, PreOnboarding, React, 면접 대비]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Course

**Wanted Pre Onboarding FE Course**

## 팀 과제 구현

### datepicker range 구현

라이브러리로 react-datepicker를 사용했다.

react-datepicker 사용법을 알아보자.

### datepicker 사용법

1. 달력 추가하기

```jsx
<DatePicker
  selected={tempStartDate}
  startDate={tempStartDate}
  endDate={tempEndDate}
  dateFormat="M월 dd일"
  onChange={onChange}
  minDate={new Date()}
  locale={ko}
  shouldCloseOnSelect={false}
  renderCustomHeader={CustomHeader}
  monthsShown={2}
  selectsRange
  inline
/>
```

dateFormat은 손쉽게 위와 같이 바꿔줄 수 있다.

minDate에는 오늘 이전 날짜는 선택하지 못하도록 했다.

locale은 추가적인 dependency가 필요하다.

date-fns를 설치하고, 다음을 import 하여 사용한다.

```jsx
import { ko } from "date-fns/esm/locale";
```

shouldCloseOnSelect={false}는 날짜 선택해도 달력 창이 없어지지 않도록 한다.

renderCustomHeader={CustomHeader}는 달력 위쪽 부분 (제목과 화살표 부분)을 커스텀할 수 있다.

```jsx
const CustomHeader = ({
  monthDate,
  prevMonthButtonDisabled,
  nextMonthButtonDisabled,
  decreaseMonth,
  increaseMonth,
}) => (
  <CalendarHeader>
    <SVGWrap
      className="btn_month btn_month-prev"
      onClick={decreaseMonth}
      disabled={prevMonthButtonDisabled}
    >
      <ArrowLeft />
    </SVGWrap>
    <MonthDay>
      {monthDate.toLocaleString(ko, {
        month: "long",
        year: "numeric",
      })}
    </MonthDay>
    <SVGWrap
      className="btn_month btn_month-next"
      onClick={increaseMonth}
      disabled={nextMonthButtonDisabled}
    >
      <ArrowRight />
    </SVGWrap>
  </CalendarHeader>
);
```

monthsShown={2}는 달력을 두 개 띄운다. 하나는 현재 달 하나는 다음 달을 나타내준다.

selectsRange 기간 선택이 되도록 할 것이기 때문에 selectRange를 true로 사용한다.

inline input이 있고, 그 input을 눌렀을 때 달력이 나오는 것이 아니고 바로 달력이 보이도록 했다. (왜냐하면 input이 아니라 시작점, 종료점을 눌렀을 때 모달이 띄워지는 형태이기 때문이다.)

```jsx
<RangeSelect>
  <InputBox onClick={openCalendarModal}>
    <InputName>시작일</InputName>
    <Input active={startDate}>
      <span>{startDate ? dateFormatting(startDate) : "날짜 선택"}</span>
      <CalendarIcon />
    </Input>
  </InputBox>
  <InputBox onClick={openCalendarModal}>
    <InputName>종료일</InputName>
    <Input active={endDate}>
      <span>{endDate ? dateFormatting(endDate) : "날짜 선택"}</span>
      <CalendarIcon />
    </Input>
  </InputBox>
</RangeSelect>
```

InputBox를 누르면 달력을 가지고 있는 모달이 뜨도록 한다.

### datepicker css 커스텀

css 커스텀이 굉장히 어려웠다. css 커스텀할 수 있도록 api나 props를 제공해주는 줄 알았지만

찾아보니 무조건 css className을 이용해서 수정해야 했다.

- 골격

  ```css
  .react-datepicker {
    border: none;
    background-color: white;
    color: ${textColor.primary};
  }
  .react-datepicker__header {
    background-color: white;
    border: none;
  }
  .react-datepicker__month-container {
    width: 100%;
    border-bottom: 1px solid ${backgroundColor.light};
    margin-bottom: 16px;
  }
  .react-datepicker__day-names {
    width: 100%;
    display: flex;
    justify-content: space-around;
  }
  .react-datepicker__week {
    display: flex;
    justify-content: space-around;
    align-items: center;
    height: 2.75rem;
  }
  ```

- 날짜 하나하나

```css
/* 날짜(숫자)와 요일 */
.react-datepicker__day-name,
.react-datepicker__day {
  font-size: ${fontSize.sm};
  color: ${textColor.primary};
  width: 100%;
  height: 100%;
  margin: 0;
  border-radius: initial;
  line-height: 2.75rem;
  position: relative;
  z-index: 2;
}
/* 오늘 날짜 */
.react-datepicker__day--today {
  border-radius: 50%;
  background-color: ${backgroundColor.light};
}
/* 선택 불가 항목 날짜들 (오늘 이전) */
.react-datepicker__day--disabled {
  color: ${textColor.secondary};
}
/* 선택 기간 (startDate와 endDate도 in-range에 포함됨) => 연하게 */
.react-datepicker__day--in-range {
  background-color: ${backgroundColor.selected};
}
/* 클릭한 날짜, 기간 시작점, 기간 종료점, 나머지는 키보드 => 진하게 나와야 함 */
.react-datepicker__day--selected,
.react-datepicker__day--range-start,
.react-datepicker__day--range-end,
.react-datepicker__day--keyboard-selected,
.react-datepicker__month-text--keyboard-selected,
.react-datepicker__quarter-text--keyboard-selected,
.react-datepicker__year-text--keyboard-selected {
  border-radius: 50%;
  background-color: ${backgroundColor.primary};
  color: white;
}
/* 기간 시작점과 종료점 동그라미와 중간 기간들 연결 구간 => 연한 색 */
.react-datepicker__day--range-start::after,
.react-datepicker__day--range-end::after {
  content: '';
  width: 1.5rem;
  height: 2.75rem;
  position: absolute;
  top: 0;
  right: 0;
  background-color: ${backgroundColor.selected};
  z-index: -2;
}
.react-datepicker__day--range-end::after {
  right: 50%;
  ${({ isSameDay }) => css`
    background-color: ${isSameDay ? 'white' : backgroundColor.selected};
  `}
}
/* 기간 시작점과 종료점의 동그라미 => 진한색 */
.react-datepicker__day--range-start::before,
.react-datepicker__day--range-end::before {
  content: '';
  position: absolute;
  width: 2.75rem;
  height: 2.75rem;
  top: 0;
  right: 0;
  border-radius: 50%;
  background-color: ${backgroundColor.primary};
  color: white;
  z-index: -1;
}
/* 달 넘어가는 쪽 날짜 안적힌 곳 => 아예 투명하게 */
.react-datepicker__day--outside-month,
.react-datepicker__day--outside-month:hover,
.react-datepicker__day--outside-month::after,
.react-datepicker__day--outside-month::before {
  background-color: transparent !important;
}
/* 기간 선택 중 hover 효과 없애기 (원래 연한 파란색으로 기간 나옴) */
.react-datepicker__day:hover:not(.react-datepicker__day--selected, .react-datepicker__day--range-start, .react-datepicker__day--range-end, .react-datepicker__day--in-range, .react-datepicker__day--today),
.react-datepicker__day--in-selecting-range:not(.react-datepicker__day--selected, .react-datepicker__day--range-start),
.react-datepicker__day--in-selecting-range:not(.react-datepicker__day--selected, .react-datepicker__day--range-end),
.react-datepicker__month-text:hover,
.react-datepicker__quarter-text:hover,
.react-datepicker__year-text:hover {
  background-color: transparent;
}

/* 기간 날짜들 hover 되었을 때 연한색과 같도록 */
.react-datepicker__day--in-range:hover {
  background-color: ${backgroundColor.selected};
}
/* 기간 시작점/종료점 hover 되었을 때 진한색 동그라미와 같도록 */
.react-datepicker__day--range-start:hover,
.react-datepicker__day--range-end:hover {
  background-color: ${backgroundColor.primary};
  border-radius: 50%;
}
/* 오늘 날짜 hover 되었을 때 회색 동그라미와 같도록 */
.react-datepicker__day--today:hover {
  border-radius: 50%;
  background-color: ${backgroundColor.light};
}
```

#### after before css

둘다 z-index -1 이어야지 원본보다 뒤로 감

## 회고 (TIL)

**2022.03.02 Daily 회고**

✏오늘 한 일

- 팀 과제 회의 및 수행
  - 달력 기간 선택 기능
- 자소서 쓰기
- 면접 대비

⁉느낀 점

결국 기능이 되긴 되었는데, 한가지 아쉬운 것이 기간 선택하는 것이 정확히 일치하지는 않아서 아쉽다.

그냥 datepicker 말고 date-range를 쓸 걸 그랬나..

🎃현재 나의 상태

오늘은 쉬엄쉬엄!

자소서도 쓰고 공고도 보고 해야지

<hr>
