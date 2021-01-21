---
title: 'TypeScirpt #1 타입스크립트 기초'
date: 2020-12-30 18:11:00
category: 'typescript'
draft: false
---

[TypeScript 3 Fundamentals, v2](https://frontendmasters.com/courses/typescript-v2/) 강의를 보고 정리한 내용입니다.

## 1. 타입스크립트

- 마이크로소프트에서 개발한 정적 타입 언어
- 자바스크립트로 컴파일 필요
- 바벨7가 원할하게 동작

## 2. 타입스트립트를 왜 쓸까?

- 일반적인 실수를 잡을 수 있다. (ex 스펠링 에러등)
- 런타임때 발생할 에러를 컴파일 할때 에러로 알 수 있다.

## 3. 타입스크립 설정

tsconfig.json

```json
{
  "cocmpilerOptions": {
    "jsx": "react", // Transform JSX
    "strict": true, // Enable "strict" features
    "noImplicitAny": true, // Forbid implicit any
    "strictNullChecks": true, // Check + compile JS
    "alllowJs": true,
    "types": [],
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "moduleResolution": "node",
    "tartget": "es2015" // Target environment
  },
  "include": ["src"]
}
```

## 4. 변수 선언

### 1) 변수

- 초기 값을 입력하면 해당 타입으로 설정된다. 다른 타입 재할당시 에러 발생
- 어떤 값도 초기화 하지 않으면 any로 할당되어, 재할당시 타입 재약이 없지만 타입을 약화 시키기에 좋지 않다.
- 선언시 `:타입`(Type Annotation) 해줄 경우 값을 초기화 하지 않아도 해당 변수는 선언된 타입만 가질 수 있다.

```js
/**
 * (1) x is a string, b/c we’ve initialized it
 */
let x = 'hello world'

/**
 * (2) reassignment is fine
 */
x = 'hello mars'

/**
 * (3) but if we try to change type
 */
x = 42 // 🚨 ERROR

/**
 * (5) sometimes we need to declare a variable w/o initializing it
 */
let z
z = 41
z = 'abc' // (6) oh no! This isn't good

/**
 * (7) we could improve this situation by providing a type annotation
 * when we declare our variable
 */
let zz: number
zz = 41
zz = 'abc' // 🚨 ERROR Type '"abc"' is not assignable to type 'number'.
```

### 2) Array

- `:타입[]` 으로 타입을 선언 할 수 있다.
- 타입 데코레이션이 없을시 never 타입으로 선언되면, 어떤 타입의 값이 들어가도 에러가 발생한다.
- 한 개이상 배열 요소의 값을 가지고 초기화시 해당 타입들을 가질 수 있는 배열이 된다.
- :[number, string, string, number] 선언시 고정위치의 타입을 선언 할 수 있다. 배열 할당시에만 적용되고, push로 값을 추가시 number | string 만 체크하게 된다.

```js
/**
 * (8) simple array types can be expressed using []
 */
let aa: number[] = []
aa.push(33)
aa.push('abc') // 🚨 ERROR: Argument of type '"abc"' is not assignable to parameter of type 'number'.

let aa = []
aa.push(33) // 🚨 ERROR: Argument of type 33 is not assignable to parameter of type 'never'.
aa.push('abc') // 🚨 ERROR: Argument of type '"abc"' is not assignable to parameter of type 'never'.

let aa = [1]
aa.push(33)
aa.push('abc') // 🚨 ERROR: Argument of type '"abc"' is not assignable to parameter of type 'number'.

/**
 * (9) we can even define a tuple, which has a fixed length
 */
let bb: [number, string, string, number] = [
  123,
  'Fake Street',
  'Nowhere, USA',
  10110,
]
bb.push(1, 2, 3)

bb = [1, 2, 3] // 🚨 ERROR: Type 'number' is not assignable to type 'string'.
bb = [1, 'a', 'b', 4, 5]
bb = [1] // 🚨 ERROR: '[number]' 형식은 '[number, string, string, number]' 형식에 할당할 수 없습니다. 소스에 1개 요소가 있지만, 대상에 4개가 필요합니다.

/**
 * (10) Tuple values often require type annotations (  : [number, number] )
 */
const xx = [32, 31] // number[];
const yy: [number, number] = [32, 31]
```

### 3) Object Types & Interfaces

- `:{property1:타입; property2: 타입}` 세미콜론으로 프로퍼티를 구분하고, 재할당시 타입을 선언한 프로퍼티들이 존재해야한다.
- `?` 연산자를 사용할 경우 해당 프로퍼티는 존재하지 않아도 상관없다. 타입이 선언되지 않은 프로퍼티는 에러를 발생한다.
- `interface name {property1:타입; property2: 타입}` 인터페이스로 객체를 타입을 위와 똑같은 동작을 하는 타입선언 할 수 있으며, 인터페이스로 선언 할 경우 재사용이 가능한 장점이 있다.

```js
/**
 * (11) object types can be expressed using {} and property names
 */
let cc: { houseNumber: number, streetName: string }
cc = {
  streetName: 'Fake Street',
  houseNumber: 123,
}

cc = {
  houseNumber: 33,
}
/**
 * 🚨 Property 'streetName'
 * 🚨   is missing in type   '{ houseNumber: number; }'
 * 🚨   but required in type '{ houseNumber: number; streetName: string; }'.
 */

/**
 * (12) You can use the optional operator (?) to
 * indicate that something may or may not be there
 */
let dd: { houseNumber: number, streetName?: string }
dd = {
  houseNumber: 33,
}

// (13) if we want to re-use this type, we can create an interface
interface Address {
  houseNumber: number;
  streetName?: string;
}
// and refer to it by name
let ee: Address = { houseNumber: 33 }
```

### 4) Intersection & Union Types

- `: interface1 | interface2` 로 타입을 선언 할 경우 두 interface의 공통 property에만 접근 할 수 있다.
- `: interface1 & interface` 로 타입을 선언 할 경우 두 interface의 a모든 property가 있어야 한다.

```js
/**
 * (14) Intersection  types
 * Sometimes we have a type that can be one of several things
 */

interface HasPhoneNumber {
  name: string;
  phone: number;
}

interface HasEmail {
  name: string;
  email: string;
}

let contactInfo: HasEmail | HasPhoneNumber =
  Math.random() > 0.5
    ? {
        // we can assign it to a HasPhoneNumber
        name: "Mike",
        phone: 3215551212
      }
    : {
        // or a HasEmail
        name: "Mike",
        email: "mike@example.com"
      };

contactInfo.name; // NOTE: we can only access the .name property  (the stuff HasPhoneNumber and HasEmail have in common)

/**
 * (15) Union types
 */
let otherContactInfo: HasEmail & HasPhoneNumber = {
  // we _must_ initialize it to a shape that's asssignable to HasEmail _and_ HasPhoneNumber
  name: "Mike",
  email: "mike@example.com",
  phone: 3215551212
};

otherContactInfo.name; // NOTE: we can access anything on _either_ type
otherContactInfo.email;
otherContactInfo.phone;
const zzz: any = {} as never;
```

### 5) Functions

- 함수의 파라미터와 리턴 타입을 선언 할 수 있다.
- rest 파라미터는 배열형태로 타입을 선언 해주면 된다.
- Overload Signatures를 이용해 Overload를 구현 할 수 있다. 다른언어와 다르게 실행함수는 공통으로 만든 후 if문으로 분기해야한다.

```js
interface HasPhoneNumber {
  name: string;
  phone: number;
}

interface HasEmail {
  name: string;
  email: string;
}

// (1) function arguments and return values can have type annotations
function sendEmail(to: HasEmail): { recipient: string; body: string } {
  return {
    recipient: `${to.name} <${to.email}>`, // Mike <mike@example.com>
    body: "You're pre-qualified for a loan!"
  };
}

// (2) or the arrow-function variant
const sendTextMessage = (
  to: HasPhoneNumber
): { recipient: string; body: string } => {
  return {
    recipient: `${to.name} <${to.phone}>`,
    body: "You're pre-qualified for a loan!"
  };
};

// (3) return types can almost always be inferred
function getNameParts(contact: { name: string }) {
  const parts = contact.name.split(/\s/g); // split @ whitespace
  if (parts.length < 2) {
    throw new Error(`Can't calculate name parts from name "${contact.name}"`);
  }
  return {
    first: parts[0],
    middle:
      parts.length === 2
        ? undefined
        : // everything except first and last
          parts.slice(1, parts.length - 2).join(" "),
    last: parts[parts.length - 1]
  };
}

// (4) rest params work just as you'd think. Type must be array-ish
const sum = (...vals: number[]) => vals.reduce((sum, x) => sum + x, 0);
console.log(sum(3, 4, 6)); // 13

// (5) we can even provide multiple function signatures
// "overload signatures"
function contactPeople(method: "email", ...people: HasEmail[]): void;
function contactPeople(method: "phone", ...people: HasPhoneNumber[]): void;

// "function implementation"
function contactPeople(
  method: "email" | "phone",
  ...people: (HasEmail | HasPhoneNumber)[]
): void {
  if (method === "email") {
    (people as HasEmail[]).forEach(sendEmail);
  } else {
    (people as HasPhoneNumber[]).forEach(sendTextMessage);
  }
}

// ✅ email works
contactPeople("email", { name: "foo", email: "" });

// ✅ phone works
contactPeople("phone", { name: "foo", phone: 12345678 });

// 🚨 mixing does not work
contactPeople("email", { name: "foo", phone: 12345678 });
```

### 6) Lexical Scope

- `this` 를 파라미터로 타입을 선언 해주게 되면 scope 타입을 선언 해줄 수 있다.

```js
// (6) the lexical scope (this) of a function is part of its signature

function sendMessage(
  this: HasEmail & HasPhoneNumber,
  preferredMethod: 'phone' | 'email'
) {
  if (preferredMethod === 'email') {
    console.log('sendEmail')
    sendEmail(this)
  } else {
    console.log('sendTextMessage')
    sendTextMessage(this)
  }
}
const c = { name: 'Mike', phone: 3215551212, email: 'mike@example.com' }

function invokeSoon(cb: () => any, timeout: number) {
  setTimeout(() => cb.call(null), timeout)
}

// 🚨 this is not satisfied
invokeSoon(() => sendMessage('email'), 500)

// ✅ creating a bound function is one solution
const bound = sendMessage.bind(c, 'email')
invokeSoon(() => bound(), 500)

// ✅ call/apply works as well
invokeSoon(() => sendMessage.apply(c, ['phone']), 500)
```

## 참조

https://frontendmasters.com/courses/typescript-v2/
