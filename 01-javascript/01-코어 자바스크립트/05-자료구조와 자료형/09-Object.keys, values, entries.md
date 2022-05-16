# Object.keys, values, entries
`keys()`,  `values()`,  `entries()`를 사용할 수 있는 자료구조는 다음과 같다.
-   `Map`
-   `Set`
-   `Array`

일반 객체에도 순회 관련 메서드가 있지만,  `keys()`,  `values()`,  `entries()`와는 문법의 차이가 있다.

## Object.keys, values, entries
일반 객체에서는 다음과 같은 메서드를 사용할 수 있다.
-   [Object.keys(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)  – 객체의 키만 담은 배열을 반환합니다.
-   [Object.values(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/values)  – 객체의 값만 담은 배열을 반환합니다.
-   [Object.entries(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)  –  `[키, 값]`  쌍을 담은 배열을 반환합니다.

`Map`, `Set`, `Array` 전용 메서드와 일반 객체용 메서드의 차이를 (맵을 기준으로) 비교하면 다음과 같다.
|        |맵           |객체                          |
|--------|------------|------------------------------|
|호출 문법 |`map.keys()`|`Object.keys(obj)`(인수가 있음)|
|반환 값  |iterable 객체|'진짜' 배열                    |

첫 번째 차이는 `obj.keys()`가 아닌 `Object.keys(obj)`를 호출한다는 것이다. 이렇게 문법이 다른 이유는 유연성 때문이다.

두 번째 차이는 메서드 `Object.*`를 호출하면 iterable 객체가 아닌 객체의 한 종류인 배열을 반환한다는 것이다. ‘진짜’ 배열을 반환하는 이유는 하위 호환성 때문이다.

```js
let user = {
  name: "John",
  age: 30
};
```
위 객체에 대해 메서드를 사용한 결과는 다음과 같다.
-   `Object.keys(user) = ['name', 'age']`
-   `Object.values(user) = ['John', 30]`
-   `Object.entries(user) = [['name','John'], ['age',30]]`

아래 예시처럼 `Object.values`를 사용하면 프로퍼티 값을 대상으로 원하는 작업을 할 수 있다.
```js
let user = {
  name: 'Violet',
  age: 30
};

// 값을 순회합니다.
for (let value of Object.values(user)) {
  alert(value); // Violet과 30이 출력된다.
}
```

---
:warning: **Object.keys, values, entries는 심볼형 프로퍼티를 무시한다.**
`for...in` 반복문과 동일하게 Object.keys, Object.values, Object.entries는 키가 심볼형인 프로퍼티를 무시한다.

심볼형 키가 필요한 경우에는 심볼형 키만 배열 형태로 반환해주는 메서드인 [Object.getOwnPropertySymbols](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols)를 사용하면 된다. 또는 심볼형을 포함한 키 전체를 배열 형태로 반환하는 메서드인 [Reflect.ownKeys(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Reflect/ownKeys)를 사용할 수 있다.

---

## 객체 변환하기
객체에는  `map`,  `filter`  같은 배열 전용 메서드를 사용할 수 없다.

하지만  `Object.entries`와  `Object.fromEntries`를 순차적으로 적용하면 객체에도 배열 전용 메서드를 사용할 수 있다. 적용 방법은 다음과 같다.
1.  `Object.entries(obj)`를 사용해서 객체의 키-값 쌍이 요소인 배열을 얻는다.
2.  1.에서 만든 배열에  `map`  등의 배열 전용 메서드를 적용한다.
3.  2.에서 반환된 배열에  `Object.fromEntries(array)`를 적용해 배열을 다시 객체로 되돌린다.
```js
let prices = {
  banana: 1,
  orange: 2,
  meat: 4,
};

let doublePrices = Object.fromEntries(
  // 객체를 배열로 변환해서 배열 전용 메서드인 map을 적용하고 fromEntries를 사용해 배열을 다시 객체로 되돌린다.
  Object.entries(prices).map(([key, value]) => [key, value * 2])
);

alert(doublePrices.meat); // 8
```
