---
title: '호이스팅(Hoisting)의 이해'
date: 2020-09-14 03:30:13
category: 'javascript'
draft: false
---

자바스크립트를 하다 보면 한 번쯤은 호이스팅이라는 말을 들어봤을거다.

그럼 호이스팅은 뭘까?

## 1. 호이스팅이란?

먼저 MDN을 확인해보자.

> 호이스팅(hoisting)은 ECMAScript® 2015 언어 명세 및 그 이전 표준 명세에서 사용된 적이 없는 용어입니다. **호이스팅은 JavaScript에서 실행 콘텍스트(특히 생성 및 실행 단계)가 어떻게 동작하는가에 대한 일반적인 생각**으로 여겨집니다. 하지만 호이스팅은 오해로 이어질 수 있습니다.
>
> 예를 들어, 호이스팅을 변수 및 함수 선언이 물리적으로 작성한 코드의 상단으로 옮겨지는 것으로 가르치지만, 실제로는 그렇지 않습니다. **변수 및 함수 선언은 컴파일 단계에서 메모리에 저장되지만, 코드에서 입력한 위치와 정확히 일치한 곳에 있습니다.**

호이스팅은

1. 자바스크립트 실행컨텍스트 동작을 이해하기 위해 생긴 개념
2. 변수 및 함수 선언은 컴파일 단계에서 메모리에 저장하면서 상단에 옮겨지는 것처럼 보이지만 실제로는 코드에 입력한 위치에 존재한다

위와 같이 정리할 수 있지만, 우리가 동작을 이해하는 데는 변수 선언과 함수 선언은 상단으로 끌어올리는 것으로 이해하면 좋을 거 같다.

그럼 호이스팅이 무엇인지 코드로 확인해보자

```jsx
console.log(a) // undifined
console.log(b) // function b() { console.log('함수입니다') }
b() // 함수입니다

var a
function b() {
  console.log('함수입니다')
}
```

변수랑 함수가 선언되기 전에 호출을 했는데 에러가 발생하지 않는 걸 확인할 수 있다. 타 언어를 한 사람들에게 너무 이상하겠지만 이것이 자바스크립트에서 말하는 호이스팅이다.

그럼 왜 에러가 나지 않은 걸까?

위 코드를 우리가 이해하기 쉬운 코드로 작성하면 아래와 같다. (MDN에서도 적혀 있듯이 실제로 이렇게 동작하는 것은 아니다. 이해를 돕기 위한 코드로 봐주면 될 거 같다)

```jsx
var a // 자바스크립트 var 변수는 선언과 동시 undefined 값으로 초기화한다.
function b() {
  console.log('함수입니다')
}

console.log(a) // undifined
console.log(b) // function b() { console.log('함수입니다') }
b() // 함수입니다console.log(
```

위와 같이 변수 선언과 함수 선언은 제일 위로 끌어 올려지게 되어 에러가 나지 않는다.

## 2. 호이스팅 TEST

호이스팅의 개념을 이해했다면 몇 가지 상황을 통해 한 번 더 점검해보자.

아래 코드를 실행하면 어떤 값을 출력 되게 될까?

```jsx
console.log(sum)
sum = 5
console.log(sum)
sum += 5
console.log(sum)
var sum
console.log(sum)

// 정답은 아래에

console.log(sum) // undefined
sum = 5
console.log(sum) // 5
sum += 5
console.log(sum) // 10
var sum
console.log(sum) // 10
```

위 상황은 호이스팅을 이해하지 못해 많이 착각할 수 있는 예제이다. 호이스팅을 제대로 이해하지 못했다면 마지막 값이 `undefined`라고 생각했을 수 있다. 왜 `10`이 출력 된 걸까?

우리가 이해하기 쉬운 코드로 작성해보자.

```jsx
var sum // undefined로 초기화  호이스팅 동작
console.log(sum) // undefined
sum = 5
console.log(sum) // 5
sum += 5
console.log(sum) // 10
// var sum;     <---- 해당 부분이 호이스팅 돼서 사라짐
console.log(sum) // 10
```

위와 같이 `var sum`이 호이스팅 되어 위로 올라가면서 마지막 sum이 10이 출력되는 것을 확인할 수 있다.

아래 코드는 어떤 결과가 나올까?

```jsx
console.log(sum)
sum = 5
console.log(sum)
sum += 5
console.log(sum)
var sum
console.log(sum)

// 정답은 아래에

console.log(sum) // undefined
sum = 5
console.log(sum) // 5
sum += 5
console.log(sum) // 10
var sum = 0
console.log(sum) // 0
```

`var sum;`을 `var sum = 0;`으로 변경해보니 마지막 sum 값이 `0`으로 출력 되는 것을 확인할 수 있다.

위 코드도 이해하기 쉬운 코드로 작성해보자.

```jsx
var sum // undefined로 초기화  호이스팅 동작
console.log(sum) // undefined
sum = 5
console.log(sum) // 5
sum += 5
console.log(sum) // 10
sum = 0 // 변수에 0값을 할당
console.log(sum) // 0
```

위와 같이 `var sum = 0;` 전체가 호이스팅 되는 것이 아니라 `var sum;` 선언 부분만 호이스팅 되기 때문에 마지막 결과 값은 `0`이 나오는 것을 알 수가 있다.

마지막으로 변수 선언과 함수 선언 중 어떤 게 먼저 선언될까? 다음 코드를 확인해보자.

```jsx
console.log(a)
var a
console.log(a)
function a() {}
console.log(a)

console.log(b)
var b = 0
console.log(b)
function a() {}
console.log(b)

// 정답은 아래에

console.log(a) // function a() {}
var a
console.log(a) // function a() {}
function a() {}
console.log(a) // function a() {}

console.log(b) // function b() {}
var b = 0
console.log(b) // 0
function b() {}
console.log(b) // 0
```

위 코드를 통해 변수 선언이 함수 선언보다 더 위에 호이스팅이 되었다는 것을 확인해 볼 수 있다.

위 코드도 이해하기 쉬운 코드로 작성해보자.

```jsx
// a파트
var a // undefined로 초기화  호이스팅 동작
function a() {} // 함수 호이스팅 동작
console.log(a) // function a() {}
// var a;    <---- 해당 부분이 호이스팅 돼서 사라짐
console.log(a) // function a() {}
// function a() {}  <---- 해당 부분이 호이스팅 돼서 사라짐
console.log(a) // function a() {}
```

```jsx
// b파트
var b // undefined로 초기화  호이스팅 동작
function b() {} // 함수 호이스팅 동작
console.log(b) // function b() {}
b = 0 // b변수에 0값을 할당
console.log(b) // 0
// function b() {}  <---- 해당 부분이 호이스팅 돼서 사라짐
console.log(b) // 0
```

## 3. 호이스팅 심화

호이스팅은 실제 자바스크립트 동작에서 실행컨텍스트를 생성시 변수와 함수를 메모리에 매핑하는 과정을 말한다. 고로 ES6 이후에 등장한 let, const라는 변수 선언에서도 호이스팅은 발생한다.

var 변수는 매핑시 undefined라는 값을 초기화 해주기 때문에 선언전에 사용을 해도 에러가 발생하지 않는다.

함수는 메모리에 매핑 되었기 때문에 선언전에 사용해도 에러가 발생하지 않는다.

let, const는 매핑시 초기화를 해주지 않기에 '초기화전에는 접근 할 수 없다'는 에러가 발생한다.

- ES6이전에는 VO(Variable Object)라는 곳에 저장
- ES6이후로는 var는 VE(Variable Environment)라는 곳에 저장 let, const, 함수는 LE(Lexical Environment)라는 곳에 저장

## 결론

1. 호이스팅은 변수 선언과 함수 선언을 상단으로 끌어올리는 것을 말한다.(실행컨텍스트 생성시 변수와 함수를 메모리에 매핑하는 과정)
2. 실제 동작은 변수 및 함수 선언은 컴파일 단계에서 메모리에 저장되지만, 코드에서 입력한 위치와 정확히 일치한 곳에 있다.
3. 변수 선언이 함수 선언보다 더 위로 호이스팅 된다.
4. let, const 도 호이스팅 된다. 하지만 변수 선언 이전에 실행시 에러가 발생한다.

### 참고자료

[MDN - Hoisting](https://developer.mozilla.org/ko/docs/Glossary/Hoisting)
