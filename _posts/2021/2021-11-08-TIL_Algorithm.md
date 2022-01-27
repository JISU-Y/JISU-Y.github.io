---
layout: single
title: "[TIL] Algorithm - 1일차"
categories: Algorithm
tag: [TIL, VanillaJS, html, css, bootcamp]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# [TIL]

## Team project

이번 주는 알고리즘 주이다!!
엄청 겁 먹고 있었는데.. 백준보다 프로그래머스 1단계가 그나마 쉬웠던 거구나 싶다.

백준 단계별 풀어보기하면서 눈물 찔끔흘렸는데..ㅋㅋㅋ

쨌든 오늘 배운 것 정리해서 다음에 잘 써먹을 수 있도록 해야겠다!!

1. repeat 함수
   간단한 네모 별찍기에서 좋은 답변을 보았는데, for문을 두번 쓰지 않아도 repeat 함수를 사용하면 되더라.

```jsx
const row = "*".repeat(a);
for (let i = 0; i < b; i++) {
  console.log(row);
}
```

이런 식으루 row를 모두 \*로 채워넣을 때 굳이 for문을 돌리지 않고 repeat(a는 가로 개수) 만큼 채워넣으면 되겠다.

repeat함수는 뭘까?

```jsx
str.repeat(count);
```

count는 문자열을 반복할 횟수.

```jsx
"abc".repeat(-1); // RangeError
"abc".repeat(0); // ''
"abc".repeat(1); // 'abc'
"abc".repeat(2); // 'abcabc'
"abc".repeat(3.5); // 'abcabcabc' (count will be converted to integer)
"abc".repeat(1 / 0); // RangeError
```

이렇게 알고 있다가 잘 써먹으면 좋겠다.

2. 배열 자르기 (substr, substring ...)
   배열 메서드를 정말 잘 알아두어야한다는 것을 깨달았다.

1) substr

```jsx
string.substr(start, length);
```

substr은 start index부터 length 길이만큼 string 잘라내는 것

2. substr

```jsx
string.substring(start, end);
```

substring은 start index부터 end index까지 string을 잘라내는 것 (start와 end 사이의 string을 꺼내옴)
start가 end보다 커지면 값 바꾸어서 처리

3. slice

```jsx
string.slice(start, end);
```

slice 또한 start index부터 end index까지 string을 잘라내는 것 (start와 end 사이의 string을 꺼내옴)
slice는 start가 end보다 커지면 "" 반환
slice(-4)하면 뒤에서부터 4개를 잘라서 반환

3. 배열 구조 분해
   이건 코딩앙마에서 들었던 내용인데, 구조분해를 이용해서 손쉽게 값을 스위치할 수 있는 것을 알게되었고 써먹어봤다!

```jsx
[a, b] = [b, a];
```

4. parseInt vs Number
   둘 다 string을 number로 변환시켜주는 것이다.
   parseInt => Number와는 다르게 문자가 혼합되어 있어도 정수로 변환
   parseInt('10px') 이면 parseInt는 10을 반환, Number는 NaN을 반환
   parseInt('f3') -> NaN 반환

5. 배열 메서드 (reduce, map, ...)

1)  reduce

```jsx
arr.reduce((acc, cur, idx) => {
  return result;
}, 0);
```

reduce는 이렇게 쓰고 축적(누적값) acc에 cur(현재값)을 이용하여 덧셈활용에 좋다. ,0는 초기값!

2.  map

```jsx
arr.reduce((요소, 인덱스, arr) => {
  return result;
}, 0);
```

```jsx
const numbers = [1]
numbers.map((number, index, source) => {
// number: 요소값
// index: source에서 요소의 index
// source: 순회하는 대상
console.log(number) // 1
console.log(index) // 0
console.log(source) // [1]
return number \* number
})
```

map은 이렇게 쓰고 요소와 인덱스를 이용해서 값을 바꾸거나 할 때 사용한다!
주어진 배열의 값들을 오름차순으로 접근해 callbackfn을 통해 새로운 값을 정의하고 신규 배열을 만들어 반환한다.

출처: https://7942yongdae.tistory.com/48 [프로그래머 YD]

3. filter

```jsx
Array.prototype.filter ( callbackfn [ , thisArg ] )
```

말그대로 call back function 안에 있는 내용에 따라 filtering을 실행하여 반환한다.

4. fill

```jsx
Array(n).fill(x);
```

모든 배열들을 특정 요소로 채우고자 할 때 사용

6. 배열 정렬
   arr.sort() 하면 바로 알바펫순/오름차순 정렬 가능

numbers.sort((a, b) => a - b); // 이건 원본배열 수정하는 오름차순 정렬

7. 가우스 공식
   1부터x까지의 합 = n\*(n+1)/2
   를 이용하여 유용하게 문제 풀 수 있다.

8. Date 메서드

```jsx
function getDayName(a, b) {
  var date = new Date(2016, a - 1, b); // month는 월을 의미하는 0~11까지의 정수

  return date.toString().slice(0, 3).toUpperCase();
}
```

9. split을 이용한 문제

```jsx
s.toUpperCase().split("P").length;
```

이렇게 하면 PPoooyy 되면 [ '', '', 'OOOYY' ] 이렇게 나옴. 이렇게 해서 p의 개수를 알 수 있다.

10. 정규 표현식

정규 표현식은 문자열에 나타는 특정 문자 조합과 대응시키기 위해 사용되는 패턴입니다. 자바스크립트에서, 정규 표현식 또한 객체입니다.  
그렇다더라...

=> 이건 내일 따로 들어봐야겠다.....

11. 진수 변환

```jsx
toString(16);
toString(2);
parseInt(hex, 16);
```

일단 내일 한번 다시 훑어보고 남은 문제들 풀어봐야겠다!
