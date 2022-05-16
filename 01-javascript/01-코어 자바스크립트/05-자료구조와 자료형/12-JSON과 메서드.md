# JSON과 메서드
## JSON.stringify
JSON(JavaScript Object Notation)은 값이나 객체를 나타내주는 범용 포맷으로,  RFC 4627 표준에 정의되어 있다. JSON은 원래 자바스크립트에서 사용할 목적으로 만들어진 포맷이다. 그런데 자바스크립트가 아닌 언어에서도 라이브러리를 사용해서 JSON을 데이터 교환 목적으로 사용하는 경우가 많다.

자바스크립트가 제공하는 JSON 관련 메서드는 아래와 같다.
-   `JSON.stringify`  – 객체를 JSON으로 바꿔준다.
-   `JSON.parse`  – JSON을 객체로 바꿔준다.

```js
let student = {
  name: 'John',
  age: 30,
  isAdmin: false,
  courses: ['html', 'css', 'js'],
  wife: null
};

let json = JSON.stringify(student);

alert(typeof json); // 문자열이다.

alert(json);
/* JSON으로 인코딩된 객체:
{
  "name": "John",
  "age": 30,
  "isAdmin": false,
  "courses": ["html", "css", "js"],
  "wife": null
}
*/
```

`JSON.stringify(student)`를 호출하자 `student`가 문자열로 바뀐 것을 확인할 수 있다.

이렇게 변경된 문자열은 **JSON으로 인코딩된(JSON-encoded)**, **직렬화 처리된(serialized)**, **문자열로 변환된(stringified)**, **결집된(marshalled)** 객체라고 부른다. 객체는 이렇게 문자열로 변환된 후에 네트워크를 통해 전송하거나 저장소에 저장할 수 있다.

JSON으로 인코딩된 객체는 다음과 같이 일반 객체와 다른 특징을 가진다.
-   문자열은 큰따옴표로 감싸야 한다. JSON에서는 작은따옴표나 백틱을 사용할 수 없다.
-   객체 프로퍼티 이름은 큰따옴표로 감싸야 한다.

`JSON.stringify`는 객체뿐만 아니라 배열과 원시값에도 적용할 수 있다. 적용할 수 있는 자료형은 다음과 같다.
-   객체  `{...}`
-   배열  `[...]`
-   원시형
    -   문자형
    -   숫자형
    -   불린형 값  `true`/`false`
    -   `null`

```js
alert(JSON.stringify(1)) // 1

// 문자열은 큰따옴표로 변경된다.
alert(JSON.stringify('test')) // "test"

alert(JSON.stringify(true)); // true

alert(JSON.stringify([1, 2, 3])); // [1,2,3]
```

JSON은 데이터 교환을 목적으로 만들어진 언어에 종속되지 않는 포맷이다. 따라서 자바스크립트 특유의 객체 프로퍼티는 `JSON.stringify`가 처리할 수 없다.

`JSON.stringify` 호출 시 무시되는 프로퍼티는 다음과 같다.
-   함수 프로퍼티 (메서드)
-   심볼형 프로퍼티 (키가 심볼인 프로퍼티)
-   값이  `undefined`인 프로퍼티

```js
let user = {
  sayHi() { // 무시된다.
    alert('Hello');
  },
  [Symbol('id')]: 123, // 무시된다.
  something: undefined // 무시된다.
};

alert(JSON.stringify(user)); // {}, 빈 객체가 출력된다.
```
`JSON.stringify`는 중첩 객체도 자동으로 문자열로 바꿔준다.
```js
let meetup = {
  title: 'Conference',
  room: {
    number: 23,
    participants: ['john', 'ann']
  }
};

alert(JSON.stringify(meetup));
/* 객체 전체가 문자열로 변환되었다.
{
  "title":"Conference",
  "room":{"number":23,"participants":["john","ann"]},
}
*/
```
`JSON.stringify`를 사용할 때 객체에 순환 참조가 있으면 에러가 발생한다.
```js
let room = {
  number: 23
};

let meetup = {
  title: 'Conference',
  participants: ['john', 'ann']
};

meetup.place = room;       // meetup은 room을 참조한다.
room.occupiedBy = meetup; // room은 meetup을 참조한다.

JSON.stringify(meetup); // Error: Converting circular structure to JSON
```

## replacer로 원하는 프로퍼티만 직렬화하기
`JSON.stringify`의 전체 문법은 다음과 같다.
```js
let json = JSON.stringify(value, [replacer], [space])
```
### value
인코딩 하려는 값

### replacer
JSON으로 인코딩 하길 원하는 프로퍼티가 담긴 배열 또는 매핑 함수 `function(key, value)`

### space
서식 변경 목적으로 사용할 공백 문자의 수

대다수의 경우에는 `JSON.stringify`에 인수를 하나만 넣어서 사용한다. 그런데 순환 참조를 다뤄야 하는 상황처럼 전환 프로세스를 정교하게 조정할 필요가 있다면, 두 번째 인수를 사용해야 한다.

JSON으로 변환하길 원하는 프로퍼티가 담긴 배열을 두 번째 인수로 넘겨주면 이 프로퍼티들만 인코딩할 수 있다. 아래 예시에서는 순환 참조를 발생시키는 프로퍼티 `room.occupiedBy`만 제외하고 모든 프로퍼티를 배열에 넣었다.
```js
let room = {
  number: 23
};

let meetup = {
  title: 'Conference',
  participants: [{ name: 'John' }, { name: 'Alice' }],
  place: room // meetup은 room을 참조한다.
};

room.occupiedBy = meetup; // room은 meetup을 참조한다.

alert(JSON.stringify(meetup, ['title', 'participants', 'place', 'name', 'number']));
/*
{
  "title":"Conference",
  "participants":[{"name":"John"},{"name":"Alice"}],
  "place":{"number":23}
}
*/
```
이번에는 `replacer` 자리에 함수를 넣어보겠다. `replacer`에 전달되는 함수는 프로퍼티 `(키, 값)` 쌍 전체를 대상으로 호출되는데, 반드시 기존 프로퍼티 값을 대신하여 사용할 값을 반환해야 한다. 특정 프로퍼티를 직렬화에서 제외하려면 반환 값을 `undefined`로 만들면 된다.
```js
let room = {
  number: 23
};

let meetup = {
  title: 'Conference',
  participants: [{ name: 'John' }, { name: 'Alice' }],
  place: room // meetup은 room을 참조한다.
};

room.occupiedBy = meetup; // room은 meetup을 참조한다.

alert(JSON.stringify(meetup, function replacer(key, value) {
  alert(`${key}: ${value}`);
  return (key == 'occupiedBy') ? undefined : value;
}));

/* replacer 함수에서 처리하는 키:값 쌍 목록
:             [object Object]
title:        Conference
participants: [object Object],[object Object]
0:            [object Object]
name:         John
1:            [object Object]
name:         Alice
place:        [object Object]
number:       23
*/
```
`replacer` 함수가 중첩 객체와 배열의 요소까지 포함한 모든 키-값 쌍을 처리한다. `replacer` 함수는 재귀적으로 키-값 쌍을 처리하는데, 함수 내에서 `this`는 현재 처리하고 있는 프로퍼티가 위치한 객체를 가리킨다.

첫 얼럿창에 문자열 `":[object Object]"`이 뜨는데, 이는 함수가 최초로 호출될 때 `{"": meetup}` 형태의 '래퍼 객체'가 만들어지기 때문이다. `replacer`함수가 가장 처음 처리하는 `(key, value)` 쌍의 키는 빈 문자열, 값은 변환하고자 하는 객체(meetup) 전체가 되는 것이다.

## space로 가독성 높이기
`JSON.stringify(value, replacer, space)`의 세 번째 인수 `space`는 가독성을 높이기 위해 중간에 삽입해 줄 공백 문자의 수를 나타낸다.
```js
let user = {
  name: 'John',
  age: 25,
  roles: {
    isAdmin: false,
    isEditor: true
  }
};

alert(JSON.stringify(user, null, 2));
/* 공백 문자 두 개를 사용하여 들여쓰기가 된다.
{
  "name": "John",
  "age": 25,
  "roles": {
    "isAdmin": false,
    "isEditor": true
  }
}
*/

/* JSON.stringify(user, null, 4)라면 아래와 같이 좀 더 들여쓰기가 된다.
{
    "name": "John",
    "age": 25,
    "roles": {
        "isAdmin": false,
        "isEditor": true
    }
}
*/
```

## 커스텀 "toJSON"
`toString`을 사용해 객체를 문자형으로 변환시키는 것처럼, 객체에 `toJSON`이라는 메서드가 구현되어 있으면 객체를 JSON으로 바꿀 수 있다. `JSON.stringify`는 이런 경우를 감지하고 `toJSON`을 자동으로 호출해준다.
```js
let room = {
  number: 23,
  toJSON() {
    return this.number;
  }
};

let meetup = {
  title: 'Conference',
  room
};

alert(JSON.stringify(room)); // 23

alert(JSON.stringify(meetup));
/*
  {
    "title":"Conference",
    "room": 23
  }
*/
```

## JSON.parse
[JSON.parse](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)를 사용하면 JSON으로 인코딩된 객체를 다시 객체로 디코딩 할 수 있다.
```js
let value = JSON.parse(str, [reviver]);
```
### str
JSON 형식의 문자열

### reviver
모든  `(key, value)`  쌍을 대상으로 호출되는 function(key,value) 형태의 함수로 값을 변경할 수 있다.

```js
// 문자열로 변환된 배열
let numbers = '[0, 1, 2, 3]';

numbers = JSON.parse(numbers);

alert(numbers[1]); // 1
```
`JSON.parse`는 중첩 객체에도 사용할 수 있다.
```js
let userData = '{ "name": "John", "age": 35, "isAdmin": false, "friends": [0,1,2,3] }';

let user = JSON.parse(userData);

alert(user.friends[1]); // 1
```
디버깅을 목적으로 직접 JSON을 만들 때 흔히 하는 실수는 다음과 같다.
```js
let json = `{
  name: "John",                     // 실수 1: 프로퍼티 이름을 큰따옴표로 감싸지 않았다.
  "surname": 'Smith',               // 실수 2: 프로퍼티 값은 큰따옴표로 감싸야 하는데, 작은따옴표로 감쌌다.
  'isAdmin': false                  // 실수 3: 프로퍼티 키는 큰따옴표로 감싸야 하는데, 작은따옴표로 감쌌다.
  "birthday": new Date(2000, 2, 3), // 실수 4: "new"를 사용할 수 없다. 순수한 값(bare value)만 사용할 수 있다.
  "friends": [0,1,2,3]              // 이 프로퍼티는 괜찮다.
}`;
```
JSON은 주석을 지원하지 않는다. 주석을 추가하면 유효하지 않은 형식이 된다.

키를 큰따옴표로 감싸지 않아도 되고 주석도 지원해주는 JSON5라는 포맷도 있는데, 이 포맷은 자바스크립트 명세서에서 정의하지 않은 독자적인 라이브러리이다.

## reviver 사용하기
서버로부터 받은 JSON 문자열을 역 직렬화(deserialize)한 객체의 프로퍼티에 Date 내장 메소드를 사용하면 에러가 발생한다.
```js
let str = '{"title":"Conference","date":"2017-11-30T12:00:00.000Z"}';

let meetup = JSON.parse(str);

alert(meetup.date.getDate()); // 에러가 발생한다.
```
`meetup.date`의 값은 `Date` 객체가 아니고 문자열이기 때문에 발생한 에러이다.

이 문제를 해결하기 위해서는 `JSON.parse`의 두 번째 인수 `reviver`를 사용하면 된다. 나머지 값은 그대로 두고 `date`만 `Date` 객체를 반환하도록 함수를 구현한다.
```js
let str = '{"title":"Conference","date":"2017-11-30T12:00:00.000Z"}';

let meetup = JSON.parse(str, function(key, value) {
  if (key == 'date') return new Date(value);
  return value;
});

alert(meetup.date.getDate()); // 이제 문제없이 동작한다.
```
중첩 객체에도 똑같이 적용할 수 있다.
```js
let schedule = `{
  "meetups": [
    {"title":"Conference","date":"2017-11-30T12:00:00.000Z"},
    {"title":"Birthday","date":"2017-04-18T12:00:00.000Z"}
  ]
}`;

schedule = JSON.parse(schedule, function(key, value) {
  if (key == 'date') return new Date(value);
  return value;
});

alert( schedule.meetups[1].date.getDate() ); // 잘 동작한다.
```
