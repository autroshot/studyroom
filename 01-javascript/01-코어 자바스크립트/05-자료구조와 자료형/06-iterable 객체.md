# iterable 객체
<strong>반복 가능한(iterable, 이터러블)</strong> 객체는 배열을 일반화한 객체이다. 이터러블을 사용하면 어떤 객체에든 `for..of` 반복문을 적용할 수 있다.

배열과 문자열은 대표적인 이터러블이다.

## Symbol.iterator
어떤 객체를 이터러블로 만들려면(`for..of`가 동작하도록 하려면) `Symbol.iterator`(특수 내장 심볼)라는 메서드를 추가해 아래와 같은 과정이 가능하도록 해야 한다.
1.  `for..of`가 시작되면 `Symbol.iterator`를 호출된다(`Symbol.iterator`가 없으면 에러가 발생한다).  `Symbol.iterator`는 반드시 이터레이터(iterator, 메서드  `next`가 있는 객체)를 반환해야 한다.
2.  이후 `for..of`는  반환된 객체(이터레이터)만을 대상으로 동작한다.
3.  `for..of`에 다음 값이 필요하면, `for..of`는 이터레이터의 `next()`메서드를 호출한다.
4.  `next()`의 반환 값은  `{ done: Boolean, value: any }`와 같은 형태이어야 한다.  `done=true`는 반복이 종료되었음을 의미한다. `done=false`일 때는 `value`에 다음 값이 저장된다.

이터러블 객체의 핵심은 '관심사의 분리(Separation of concern, SoC)'에 있다. 이터레이터 객체와 반복 대상인 객체의 분리는 다음과 같이 구현한다.
-   `range`에는 메서드  `next()`가 없다.
-   대신  `range[Symbol.iterator]()`를 호출해서 만든 ‘이터레이터’ 객체와 이 객체의 메서드  `next()`에서 반복에 사용될 값을 만들어낸다.
```js
let range = {
  from: 1,
  to: 5,

  [Symbol.iterator]() {
    this.current = this.from;
    return this;
  },

  next() {
    if (this.current <= this.to) {
      return { done: false, value: this.current++ };
    } else {
      return { done: true };
    }
  }
};

for (let num of range) {
  alert(num); // 1, then 2, 3, 4, 5
}
```

---
:information_source: **무한개의 이터레이터**

`range`에서  `range.to`에  `Infinity`를 할당하면  `range`가 무한대가 된다. 무수히 많은 의사 난수(pseudorandom numbers)를 생성하는 이터러블 객체를 만드는 것도 가능하다. 이 방법이 유용하게 쓰이는 경우도 있다.

`next`에는 제약사항이 없다.  `next`가 값을 계속 반환하는 것은 정상적인 동작이다.

물론 위와 같은 이터러블에  `for...of`  반복문을 사용하면 끝이 없을 것이다. 그렇다 하더라도  `break`를 사용하면 언제든지 반복을 멈출 수 있다.

---

## 문자열은 이터러블이다
배열과 문자열은 가장 광범위하게 쓰이는 내장 이터러블이다. `for...of`는 문자열의 각 글자를 순회한다.
```js
for (let char of 'test') {
  // 글자 하나당 한 번 실행된다(4회 호출).
  alert(char); // t, e, s, t가 차례대로 출력된다.
}
```
서로게이트 쌍(surrogate pair)에도 잘 동작한다.
```js
let str = '𝒳😂';
for (let char of str) {
  alert(char); // 𝒳와 😂가 차례대로 출력된다.
}
```

## 이터레이터를 명시적으로 호출하기
```js
let str = 'Hello';

// for...of를 사용한 것과 동일한 작업을 한다.
// for (let char of str) alert(char);

let iterator = str[Symbol.iterator]();

while (true) {
  let result = iterator.next();
  if (result.done) break;
  alert(result.value); // 글자가 하나씩 출력된다.
}
```
이터레이터를 명시적으로 호출하는 경우는 거의 없지만, 이 방법을 사용하면 `for..of`를 사용하는 것보다 반복 과정을 더 잘 통제할 수 있다는 장점이 있다. 반복을 시작했다가 잠시 멈추고 다른 작업을 하다가, 다시 반복을 시작하는 것과 같이 반복 과정을 여러 개로 나누는 것이 가능하다.

## 이터러블과 유사 배열
비슷해 보이지만 아주 다른 용어 두 가지가 있다.
-   이터러블(iterable): `Symbol.iterator`가 구현된 객체로 `for..of`를 사용할 수 있다.
-   유사 배열(array-like): 인덱스와 `length` 프로퍼티가 있어서 배열처럼 보이는 객체이다.

브라우저나 등의 호스트 환경에서 자바스크립트를 사용해 문제를 해결할 때 이터러블 객체, 유사 배열 객체, 혹은 둘 다인 객체를 만날 수 있다. 이터러블 객체(`for..of` 를 사용할 수 있음)이면서 유사배열 객체(숫자 인덱스와 `length` 프로퍼티가 있음)인 문자열이 대표적인 예이다.

이터러블 객체라고 해서 유사 배열 객체는 아니다. 유사 배열 객체라고 해서 이터러블 객체인 것도 아니다. 위 예시의 `range`는 이터러블 객체이지만 인덱스도 없고 `length` 프로퍼티도 없으므로 유사 배열 객체가 아니다. 아래 예시의 객체는 유사 배열 객체이지만 이터러블 객체가 아니다.
```js
let arrayLike = { // 인덱스와 length프로퍼티가 있으므로 유사 배열이다.
  0: 'Hello',
  1: 'World',
  length: 2
};

// Symbol.iterator가 없으므로 에러가 발생한다.
for (let item of arrayLike) {}
```
이터러블과 유사 배열은 보통 배열이 아니기 때문에  `push`, `pop` 등의 메서드를 지원하지 않는다. 이터러블과 유사 배열에 배열 메서드를 적용하려면 `Array.from`을 이용하면 된다.

## Array.from
범용 메서드 [Array.from](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/from)는 이터러블이나 유사 배열을 받아 ‘진짜’ 배열을 만들어준다. 이 과정을 거치면 이터러블이나 유사 배열에 배열 메서드를 사용할 수 있다.
```js
let arrayLike = {
  0: 'Hello',
  1: 'World',
  length: 2
};

let arr = Array.from(arrayLike);
alert(arr.pop()); // World, 메서드가 제대로 동작한다.
```
`Array.from`은 인수로 받은 객체가 이터러블이나 유사 배열인지 확인한다. 넘겨 받은 인수가 이터러블이나 유사 배열인 경우, 새로운 배열을 만들고 객체의 모든 요소를 새롭게 만든 배열로 복사한다.

`Array.from`에 ‘매핑(mapping)’ 함수를 선택적으로 넘겨줄 수 있다. `mapFn`을 두 번째 인수로 넘겨주면 각 요소를 대상으로 `mapFn`을 적용할 수 있다. 새로운 배열에는 `mapFn`을 적용하고 반환된 값이 추가된다. 세 번째 인수 `thisArg`는 각 요소의 `this`를 지정할 수 있다.
```js
Array.from(obj, [mapFn], [thisArg])
```
```js
// range는 챕터 위쪽 예시에서 그대로 가져왔다고 가정한다.

// 각 숫자를 제곱한다.
let arr = Array.from(range, num => num * num);

alert(arr); // 1,4,9,16,25
```
다음 예시에서는 `Array.from`를 사용해 문자열을 배열로 만들어보았다.
```js
let str = '𝒳😂';

// str를 분해해서 글자가 담긴 배열로 만들었다.
let chars = Array.from(str);

alert(chars[0]); // 𝒳
alert(chars[1]); // 😂
alert(chars.length); // 2
```
`Array.from`은 `str.split`과 달리, 문자열 자체가 가진 이터러블 속성을 이용해 동작한다. 따라서 `for...of`처럼 서로게이트 쌍에도 제대로 적용된다.

`Array.from`을 사용하면 서로게이트 쌍을 처리할 수 있는 `slice`를 직접 구현할 수도 있다.
```js
function slice(str, start, end) {
  return Array.from(str).slice(start, end).join('');
}

let str = '𝒳😂𩷶';

alert(slice(str, 1, 3)); // 😂𩷶

// 내장 순수 메서드는 서로게이트 쌍을 지원하지 않는다.
alert(str.slice(1, 3)); // 쓰레기값(영역이 다른 특수 값)이 출력된다.
```
