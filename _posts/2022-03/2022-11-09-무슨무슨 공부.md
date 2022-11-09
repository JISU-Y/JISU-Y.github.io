---
layout: single
title: "[Javascript, Typescript] 비트 연산자와 Optional Chaining"
categories: Javascript
tag: [Javascript, Typescript, 비트, 연산자]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Javascript / Typescript

**Javascript의 기본 비트 연산자와  
Null 병합 연산자/논리 연산자 OR의 비교,  
Optional Chaining/Non-null assertion의 비교에 대해 알아본다.**

## 비트 연산자

문득 궁금했다.  
비트 연산은 기본적으로 학부 1학년 때부터 배우는 것인데, 실무에서 사용이 되는 것은 맞나?  
굳이 필요없는 것 같은데 도대체 언제 쓰일까?

### 비트 연산자 종류

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 비트 연산자

- AND (&): 피연산자 둘 모두 1인 경우에만 1 출력
- OR (|): 피연산자들 중 하나라도 1이면 1 출력
- XOR (^): 피연산자 둘이 값이 다르면 1 같으면 0 출력
- NOT (~): 피연산자의 부정 출력
</aside>

### 비트 연산자 동작

> a를 5, b를 3이라고 해보자.

```jsx
a = 5 → (101)
b = 3 → (011)
```

**AND (&) 연산자**

5 즉, 101  
3 즉, 011  
이 비트 하나하나를 and 연산하면 둘 다 1인 경우는 마지막 자리밖에 없다!  
따라서 001 즉, 1이 된다.

```
a & b === 001 → 1
```

**OR (|) 연산자**

마찬가지로 둘 중의 하나라도 1이 되는 비트가 3자리 모두이므로  
111 즉, 7이 된다.

```
a | b === 111 → 7
```

**XOR(^) 연산자**

비트 값이 서로 다른 비트를 따져 연산하면  
110 즉, 6이 된다.

```
a ^ b === 110 → 6
```

**NOT (~/Tilde) 연산자**

부정 연산자, 틸드 연산자를 사용하면 그 값이 반전(부호 반전)되어 나온다.

```
~5 === -5
~-4 === 4
```

### 비트 연산자의 활용

비트 연산자는 알고는 있는데, 이를 어디에 쓸 수 있을까?

Javascript에서 활용할 수 있는 방법은 여러 가지가 있으나 몇 가지만 알아보자.

#### math floor의 대체

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
📌 Math.floor() replacements

- ~~n
- n|n
- n&n
</aside>

→ n|n이 가장 빠르다고 한다.  
→ 그러나 이는 코드 가독성을 감수하면서까지 사용할 정도로 빠르지 않으므로 웬만해서는 Math.floor를 사용하는 것이 좋을 것.

#### config flag로서 활용

boolean으로된 옵션을 인자로 받아 옵션이 어떻게 되어 있는지에 따라 분기처리 할 때  
아래와 같이 냅다 인자로 풀어서 받아버리면 매우 보기 힘들다.

```tsx
function normalizeList (makeFraction = true, makeSet = true, makeSort = false, ...) {
  // something happens here...
}
```

그래서 보통 다음과 같이 option을 object로 따로 뽑아서 사용한다.

```tsx
const defaultOptions = {
  makeFraction: true,
  makeSet: true,
  makeSort: false,
  ...
};

function normalizeList (options = defaultOptions) {
  // something happens here...
	if(options.makeFraction) {
    const max = Math.max(...list);
    list = list.map(value => Number((value / max).toFixed(2)));
	}

	// ...
}
```

이 방법도 좋다.

그러나 옵션마다 그냥 true/false을 나타내는 값인데 object를 구성하게 된다.  
모든 속성이 boolean이면 integer와 bit 연산자를 사용할 수도 있다.

```tsx
const LIST_DEFAULT = 0x0; // (000)
const LIST_FRACTION = 0x1; // (001)
const LIST_UNIQUE = 0x2; // (010)
const LIST_SORTED = 0x4; // (100)

function normalizeList(list, flag = LIST_DEFAULT) {
  if (flag & LIST_FRACTION) {
    const max = Math.max(...list);
    list = list.map((value) => Number((value / max).toFixed(2)));
  }

  if (flag & LIST_UNIQUE) {
    list = [...new Set(list)];
  }

  if (flag & LIST_SORTED) {
    list = list.sort((a, b) => a - b);
  }

  return list;
}
```

즉, `LIST_DEFAULT` (0x0) 값을 `flag`에 전달하면 if문 아무것도 타지 않고 `return list`를 하게 된다.  
000은 아무거나 and 연산을 해도 1(true)가 되지 않기 때문이다!

또한, `flag`에 101을 전달한다면 `LIST_FRACTION`, `LIST_SORTED` 이 두 옵션을 켠 것과 동일하다고 보면 된다!

이 역시 코드 가독성이 그리 좋은 것은 아니라고 생각되나  
이런 방법으로도 구현할 수 있고, 이런 방법으로 인사이트를 얻을 수도 있지 않을까?

이 이외에도 color hex 코드를 추출하는 활용도 있었다. (bit shift 이용)

확실히 실무에서 많이 쓰이는 부분은 아니라고 생각되었다.  
그러나 알아두면 좋을 듯 했다.

## Null 병합 연산자 VS 논리 연산자 OR

Object 데이터가 nullable 할 때, 보통은 `||`을 많이 사용하는 것 같다.  
그런데 `??` 와는 무슨 차이가 있는 것일까?

### Null 병합 연산자 (Nullish coalescing operator)

> 피연산자가 `null` 또는 `undefined`일 때만 오른쪽 피연산자를 반환, 아니면 왼쪽 피연산자를 반환한다.

```tsx
console.log(12 ?? "not found"); // 12
console.log(0 ?? "not found"); // 0 // <- ||과 다르게 나타나는 연산

console.log("jane" ?? "not found"); // "jane"
console.log("" ?? "not found"); // "" // <- ||과 다르게 나타나는 연산

console.log(true ?? "not found"); // true
console.log(false ?? "not found"); // false // <- ||과 다르게 나타나는 연산

console.log(undefined ?? "not found"); // "not found"
console.log(null ?? "not found"); // "not found"
```

### 논리 연산자 OR (logical OR)

> 원래는 논리합처럼 boolean || boolean 값으로 비교하면 boolean 값만 반환하지만,
> 둘 중에 하나라도 boolean이 아닌 값이 들어오면 non-boolean 값을 반환할 수도 있다.
> (피연산자를 강제로 boolean으로 casting)

왼쪽 피연산자가 **falsy** 값

즉, null, undefined, NaN, 0, ‘’(empty string) 일 때 오른쪽 피연산자를 반환한다.

```tsx
console.log(12 || "not found"); // 12
console.log(0 || "not found"); // "not found"

console.log("jane" || "not found"); // "jane"
console.log("" || "not found"); // "not found"

console.log(true || "not found"); // true
console.log(false || "not found"); // "not found"

console.log(undefined || "not found"); // "not found"
console.log(null || "not found"); // "not found"
```

따라서 `0`이나 `empty string` , `false` 를 유효한 value로 사용하고 싶을 때는  
Null 병합 연산자(`??`)를 사용해야한다.

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
📌 Javascript falsy 값

**1. null**  
**2. NaN**  
**3. 0 ( 숫자 영(Zero) )**  
**4. 빈 문자열 ( Empty String ) : 총 3개.**  
**- 4-1. ""(큰따옴표)**  
**- 4-2. ''(작은따옴표)**  
**- 4-3. ``(템플릿 리터럴)**  
**5. undefined**  
**6. false**

</aside>

## Optional Chaining VS Non-Null Assertion

예전에 arr!.[0] 이런 코드를 본 적이 있어 이게 뭘까 알아보고 싶었다.

데이터를 다루면서 특정 Object의 속성 혹은 메서드를 읽으려 chaining할 때  
해당 데이터가 null/undefined인 경우 흔히 보는  
`Object is possibly “null” or “undefined”.` 에러가 발생한다.

이럴 때 optional chaining(?.)을 흔히 사용하는데,  
간혹 (!.)를 사용한 경우도 있다!

### optional chaning (?.)

> chaining 연산자(`.`)와 비슷하게 동작하지만, 참조가 nullish한 값(null 혹은 undefined)이라면 undefined를 반환해준다.

```tsx
function getUserName(user: User | null): string {
  return user.name; // Object is possibly “null” or “undefined”.
}
```

이 경우 에러가 발생하며 optional chaining을 사용하지 않는다면 다음과 같이 쓸 수 있다.

```tsx
function getUserName(user: User | null): string {
  if (user) {
    return user.name;
  }

  return "";
}
```

optional chaining으로 좀 더 간단히 사용 가능하다.

```tsx
function getUserName(user: User | null): string {
  return user?.name; // user && user.name과 같다.
}
```

### non-null assertion (!)

> postfix에 느낌표 연산자(!)를 붙임으로써 해당 피연산자가 null, undefined가 아니라고 단언해준다.

Typescript에서 지원하는 이 non-null assertion은 비슷하게 사용될지는 몰라도 사실 optional chaining과 성격이 다르다고 봐야한다.

```tsx
function getUserName(user: User | null): string {
  return user!.name;
}
```

따라서 user가 절대로 null / undefined가 들어올 수 없는 변수인데 type check가 strong하게 되어 있어 빌드 타임에서 에러가 난다면  
user는 절대 null / undefined가 될 수 없다라는 사실을 typescript에게 알려준다 **(postfix로 !를 붙임으로써)**

### 따라서 이 둘의 차이점

non-null assertion은 chaining guard를 해주지 않는다.  
단지 변수가 절대 null이 들어올 수 없으니 chaining할 수 있도록 타입을 단언해준 것 뿐이다.  
따라서 실제로 null이 들어오면 런타임에서 에러가 날 수 밖에 없다.

이에 반해 optional chaining은 속성에 chaining하여 접근하기 전 변수의 속성을 체크하여 실제로 null 가드가 된다.

실제로도 eslint에서 typescript의 non-null assertion은 strict null-checking mode의 이점을 이용하고 있지 않아 권장하지 않는다고 한다.

결론: 조용히 Optional Chaining 사용해서 물음표 살인마가 되자.

### 참고 아티클

[비트 연산자](https://blog.logrocket.com/interesting-use-cases-for-javascript-bitwise-operators/)

[비트 연산의 활용](https://dev.to/puritanic/nsfw-use-cases-for-bitwise-operators-in-js-2om5)

[optional chaining과 non null assertion의 차이](https://codeburst.io/optional-chaining-vs-assertion-operator-in-typescript-522947c927a5)

<hr>
