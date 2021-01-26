---
title: 'TypeScirpt #2 Interface & Type Aliases'
date: 2021-01-25 21:30:00
category: 'typescript'
draft: false
---

### 1) Type Alias

- `type 명칭` 방식으로 타입을 새로 별칭으로 만들 수 있다.

```tsx
interface HasPhoneNumber {
  name: string
  phone: number
}

interface HasEmail {
  name: string
  email: string
}

/**
 * (1) Type aliases allow us to give a type a name
 */
type StringOrNumber = string | number

// this is the ONLY time you'll see a type on the RHS of assignment
type HasName = { name: string }

// NEW in TS 3.7: Self-referencing types!
type NumVal = 1 | 2 | 3 | NumVal[]
```

### 2) Extends

- `extends` 키워드를 이용하여 interface를 확장 할 수 있다.(class와 비슷)

```tsx
// == INTERFACE == //
/**
 * (2) Interfaces can extend from other interfaces
 */

export interface HasInternationalPhoneNumber extends HasPhoneNumber {
  countryCode: string
}
```

### 3) Call & Construct Signature

- 함수 호출과 return값을 interface 와 type을 이용하여 정해줄수있다. (Call Signature)
- interface 의 경우 (파라미터1: 타입, 파라미터2: 타입 .....): 리턴타입 형태로 선언
- type의 경우 (파라미터1: 타입, 파라미터2: 타입 .....) ⇒ 리턴타입 형태로 선언
- `new` 를 앞에 붙여주면 new 생성자로 리턴을 받을 수 있습니다. (Construct Signature)

```tsx
/**
 * (3) they can also be used to describe call signatures
 */

interface ContactMessenger1 {
  (contact: HasEmail | HasPhoneNumber, message: string): void
}

type ContactMessenger2 = (
  contact: HasEmail | HasPhoneNumber,
  message: string
) => void

// NOTE: we don't need type annotations for contact or message
const emailer: ContactMessenger1 = (_contact, _message) => {
  /** ... */
}

/**
 * (4) construct signatures can be described as well
 */

interface ContactConstructor {
  new (...args: any[]): HasEmail | HasPhoneNumber
}
```

### 4) Dictionary Object & Index Signatures

- 객체 키값에 상관없이, value에 형태를 지정해 주고 싶을시 아래와 같이 할 수 있다.
- 객체 배열 형태를 지정해줄시 유용할거 같다.

```tsx
/**
 * (5) index signatures describe how a type will respond to property access
 */

/**
 * @example
 * {
 *    iPhone: { areaCode: 123, num: 4567890 },
 *    home:   { areaCode: 123, num: 8904567 },
 * }
 */

interface PhoneNumberDict {
  // arr[0],  foo['myProp']
  [numberName: string]:
    | undefined
    | {
        areaCode: number
        num: number
      }
}

const phoneDict: PhoneNumberDict = {
  office: { areaCode: 321, num: 5551212 },
  home: { areaCode: 321, num: 5550010 }, // try editing me
}

// at most, a type may have one string and one number index signature
```

### 5) Combinding Interfaces

- 똑같은 이름의 인터페이스를 선언하면 인터페이스가 머지 된다.

```tsx
interface phonenumberdict {
  // arr[0],  foo['myprop']
  [numbername: string]:
    | undefined
    | {
        areacode: number
        num: number
      }
}

const phonedict: phonenumberdict = {
  office: { areacode: 321, num: 5551212 },
  home: { areacode: 321, num: 5550010 }, // try editing me
}

// at most, a type may have one string and one number index signature

/**
 * (6) they may be used in combination with other types
 */

// augment the existing PhoneNumberDict
// i.e., imported it from a library, adding stuff to it
interface phonenumberdict {
  home: {
    /**
     * (7) interfaces are "open", meaning any declarations of the
     * -   same name are merged
     */
    areacode: number
    num: number
  }
  office: {
    areacode: number
    num: number
  }
}

phonedict.home // definitely present
phonedict.office // definitely present
phonedict.mobile // MAYBE present
```

### 6) TYPE ALIASES vs INTERFACES

- 인터페이스는 늦게 초기화 되기 때문에 합성 할 수 있다고 함 자기 자신 참조를 예를 들어 설명했는데 최신버전에 패치 되었는지 타입 알리아스도 잘 동작한다.....(잘모르겠다 나중에 공부 후 수정하겠다.)

```tsx
// == TYPE ALIASES vs INTERFACES == //

/**
 * (7) Type aliases are initialized synchronously, but
 * -   can reference themselves
 */

type NumberVal = 1 | 2 | 3 | NumberVal[]

/**
 * (8) Interfaces are initialized lazily, so combining it
 * -   w/ a type alias allows for recursive types!
 */

type StringVal = 'a' | 'b' | 'c' | StringArr

type StringArr = StringVal[]
// interface StringArr {
//   // arr[0]
//   [k: number]: "a" | "b" | "c" | StringVal[];
// }

const x: StringVal = Math.random() > 0.5 ? 'b' : ['a'] // ✅ ok!
```

### 7) Type Tests

- 아래가 잘 동작하게끔 만드시오

```tsx
import { JSONValue, JSONObject, JSONArray } from 'json-types'

function isJSONValue(val: JSONValue) {}
function isJSONArray(val: JSONArray) {}
function isJSONObject(val: JSONObject) {}

isJSONValue([])
isJSONValue(4)
isJSONValue('abc')
isJSONValue(false)
isJSONValue({ hello: ['world'] })
isJSONValue(() => 3) // $ExpectError

isJSONArray([])
isJSONArray(['abc', 4])
isJSONArray(4) // $ExpectError
isJSONArray('abc') // $ExpectError
isJSONArray(false) // $ExpectError
isJSONArray({ hello: ['world'] }) // $ExpectError
isJSONArray(() => 3) // $ExpectError

isJSONObject([]) // $ExpectError
isJSONObject(['abc', 4]) // $ExpectError
isJSONObject(4) // $ExpectError
isJSONObject('abc') // $ExpectError
isJSONObject(false) // $ExpectError
isJSONObject({ hello: ['world'] })
isJSONObject({ 3: ['hello, world'] })
isJSONObject(() => 3) // $ExpectError
```

- 내가 생각 하는 답

```tsx
// 💡 HINT: number[] and Array<number> mean the same thing
export type JSONValue = number | string | boolean | JSONArray | JSONObject
export type JSONArray = JSONValue[]
export type JSONObject = { [key: string]: JSONValue }
```

## 참조

https://frontendmasters.com/courses/typescript-v2/
