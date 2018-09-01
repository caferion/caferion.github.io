```
---
layout: post
title:  "자바스크립트 배열을 좀 더 잘 사용 하는 방법"
date:   2018-09-01
excerpt: "배열 메소드를 조금 더 제대로 활용하는 방법에 대해서"
tag:
- Array 
- javascript
comments: true
---
```

# 자바스크립트 배열을 좀 더 잘 사용 하는 방법

[원글: Here’s how you can make better use of JavaScript arrays](https://medium.freecodecamp.org/heres-how-you-can-make-better-use-of-javascript-arrays-3efd6395af3c)

### 1. Array.indecOf를 Array. includes로 교체하자

배열에서 Array.indexOf를 사용 하여 무언가를 찾는다면, 내가 자바스크립트를 배웠을때 의심할 필요가 없는 맞는 코드였다.



Array.indexOf는 "찾는 값의 첫번째 index를 반환한다."고 MDN문서에 적혀있다. 우리가 반환된 index를 코드에서 사용한다면 Array.indexOf는 맞는 해결방법이다.



그러나 우리는 단지 배열에 값이 있는지 없는지를 알고싶은거라면?  Yes/no 질문과 같이, 불린 값을 알고 싶은 경우라면 나는 불린값을 반환하는 Array.includes를 추천한다.

~~~ javascript

'use strict';

const characters = [
  'ironman',
  'black_widow',
  'hulk',
  'captain_america',
  'hulk',
  'thor',
];

console.log(characters.indexOf('hulk'));
// 2
console.log(characters.indexOf('batman'));
// -1

console.log(characters.includes('hulk'));
// true
console.log(characters.includes('batman'));
// false
~~~



### 2. Array.filter 대신에  Array.find를 사용하자

Array.filter는 꾀 유용한 함수이다. 모든 항목에서 콜백인자에 맞는 새로운 배열을 만들어준다. 이름처럼 우리는 filtering된 새로운 배열이 필요할 경우에만 이 메소드를 사용해야한다.



그러나 우리가 오직 한개의 항목만 반환이 필요하다면 나는 filter 함수를 추천하지 않는다(예를 들어 유니크한 id를 찾는것처럼 ) 이 경우 Array.filter는 한개의 항목을 가지고 있는 배열을 반환한다. 구체적인 ID를 찾음으로써, 배열이 필요없음에도 오직 한개의 값을 가지는 배열을 만드는 것이다.



성능에 대해 말해보자. 콜백 함수와 매칭되는 모든 아이템을 반환하기 위해서 Array.filter는 배열 전체를 탐색해야하만 한다. 우리의 콜백 요소에 만족하는 수백개의 항목들이 있다고 생각해보면 필터된 배열은 꽤 클것이다.



이런 상황을 피하기 위해서 나는 Array.find를 추천한다. Array.filter처럼 콜백 요소를 요구하고, 콜백에 만족하는 첫번째 항목의 값을 반환한다. Array.find는 콜백에 만족하는 항목을 만나자마자 멈추게 된다. 배열 전체를 탐색할 필요가 없다.

~~~javascript
'use strict';

const characters = [
  { id: 1, name: 'ironman' },
  { id: 2, name: 'black_widow' },
  { id: 3, name: 'captain_america' },
  { id: 4, name: 'captain_america' },
];

function getCharacter(name) {
  return character => character.name === name;
}

console.log(characters.filter(getCharacter('captain_america')));
// [
//   { id: 3, name: 'captain_america' },
//   { id: 4, name: 'captain_america' },
// ]

console.log(characters.find(getCharacter('captain_america')));
// { id: 3, name: 'captain_america' }
~~~



### 3. Array.find를 Array.some으로 교체하자.

이것은 위 Array.indexOf/Array.includes 케이스와 매우 유사하다.



이전 케이스에서 우리는 Array.find는 콜백요소에 해당하는 한개의 항목의 값만 반환하는것을 확인했다. Array.find가 배열에 값을 포함하고 있는지 없는지를 확인하는 가장 좋은 방법일까? 아마도 그렇지 않을것이다. 왜냐면 불린값이 아닌 항목 값을 반환하기 때문이다.



이 경우 불린 값을 반환하는 Array.some을 추천한다.

~~~javascript
'use strict';

const characters = [
  { id: 1, name: 'ironman', env: 'marvel' },
  { id: 2, name: 'black_widow', env: 'marvel' },
  { id: 3, name: 'wonder_woman', env: 'dc_comics' },
];

function hasCharacterFrom(env) {
  return character => character.env === env;
}

console.log(characters.find(hasCharacterFrom('marvel')));
// { id: 1, name: 'ironman', env: 'marvel' }

console.log(characters.some(hasCharacterFrom('marvel')));
// true
~~~



### 4. Array.filter와 Array.map을 연계하는 대신에 Array.reduce를 사용하자.

Array.reduce는 의아 할거다. 그러나 우리가 Array.filter 후 Array.map을 사용하면 뭔가 놓친게 없나 라는 생각이 들거다.



여기서 우리는 배열을 2번이나 탐색한다. 첫번째는 filter이고, 필터된 짧아진 배열에서 2번째 탐색 후 새로운 값의 배열을 얻는다. 새로운 배열을 얻기 위해 우리는 2개의 배열 메소드를 사용했다. 각 메소드는 우리가 나중에 사용하지 않을 고유한 콜백 함수와 배열(Array.filter에 의해 생성된 배열)을 가지고있다.



이 주제 에 관하여 느린 퍼포먼스를 피하기 위해서, Array.reduce 사용을 권장한다. 더 나은 코드로 같은 결과를 얻을 수 있다. Array.reduce는 필터링하고 만족하는 항목을 accumulator에 추가 할 수 있다. 에를들어 accumulator는 증가할 숫자, 채울 객체, 문자열 또는 concat 배열일 수 있다.



우리의 경우 Array.map을 사용했으므로 concat배열의 accumulator로 Array.reduce를 사용하는게 좋다. 다음 예제에서는 env값에 따라 값을 accumulator에 더하거나 남길겁니다.

~~~javascript
'use strict';

const characters = [
  { name: 'ironman', env: 'marvel' },
  { name: 'black_widow', env: 'marvel' },
  { name: 'wonder_woman', env: 'dc_comics' },
];

console.log(
  characters
    .filter(character => character.env === 'marvel')
    .map(character => Object.assign({}, character, { alsoSeenIn: ['Avengers'] }))
);
// [
//   { name: 'ironman', env: 'marvel', alsoSeenIn: ['Avengers'] },
//   { name: 'black_widow', env: 'marvel', alsoSeenIn: ['Avengers'] }
// ]

console.log(
  characters
    .reduce((acc, character) => {
      return character.env === 'marvel'
        ? acc.concat(Object.assign({}, character, { alsoSeenIn: ['Avengers'] }))
        : acc;
    }, [])
)
// [
//   { name: 'ironman', env: 'marvel', alsoSeenIn: ['Avengers'] },
//   { name: 'black_widow', env: 'marvel', alsoSeenIn: ['Avengers'] }
// ]
~~~

