# 프로토타입 메서드와 __proto__가 없는 객체
`__proto__`는 브라우저를 대상으로 개발하고 있다면 다소 구식이기 때문에 더는 사용하지 않는 것이 좋다. 표준에도 관련 내용이 명시되어 있다.

대신 다음의 모던한 메서드를 사용하는 것이 좋다.
-   [Object.create(proto, [descriptors])](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/create) - `[[Prototype]]`이  `proto`를 참조하는 빈 객체를 만든다. 이때 프로퍼티 설명자를 추가로 넘길 수 있다.
-   [Object.getPrototypeOf(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf) - `obj`의  `[[Prototype]]`을 반환한다.
-   [Object.setPrototypeOf(obj, proto)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf) - `obj`의  `[[Prototype]]`이  `proto`가 되도록 설정한다.

```js
let animal = {
  eats: true
};

// 프로토타입이 animal인 새로운 객체를 생성한다.
let rabbit = Object.create(animal);

alert(rabbit.eats); // true

alert(Object.getPrototypeOf(rabbit) === animal); // true

Object.setPrototypeOf(rabbit, {}); // rabbit의 프로토타입을 {}으로 바꾼다.
```

`Object.create`에는 프로퍼티 설명자를 선택적으로 전달할 수 있다. 설명자는 [프로퍼티 플래그와 설명자](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/07-%EA%B0%9D%EC%B2%B4%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%20%EC%84%A4%EC%A0%95.md#1-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-%ED%94%8C%EB%9E%98%EA%B7%B8%EC%99%80-%EC%84%A4%EB%AA%85%EC%9E%90) 챕터에서 배운 것과 같은 형태로 사용하면 된다.

`Object.create`를 사용하면 `for..in`을 사용하는 것보다 더 효과적으로 객체의 얕은 복사본(shallow-copy)을 만들 수 있다.
```js
let clone = Object.create(Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj));
```
`Object.create`를 호출하면 `obj`의 모든 프로퍼티를 포함한 완벽한 사본이 만들어진다, 사본에는 열거 가능한 프로퍼티와 불가능한 프로퍼티, 데이터 프로퍼티, getter, setter 등 모든 프로퍼티가 복제된다. `[[Prototype]]`도 복제된다.

---
:warning: **속도가 중요하다면 기존 객체의 `[[Prototype]]`을 변경하지 말아야 한다.**

원한다면 언제나 `[[Prototype]]`을 얻거나 설정할 수 있다. 하지만 대개는 객체를 생성할 때에만 `[[Prototype]]`을 설정하고 이후에는 수정하지 않는다.

자바스크립트 엔진은 `[[Prototype]]`을 변경하지 않는 방식에 최적화되어 있다. 프로토타입을 즉석에서 바꾸는 연산은 객체 프로퍼티 접근 작업의 최적화를 망치기 때문에 성능에 나쁜 영향을 미친다.

---

## 아주 단순한 객체
알다시피 객체는 키-값 쌍을 저장하는 연관 배열로도 사용할 수 있다.

그런데 커스텀 사전을 만드는 것과 같이 사용자가 직접 입력한 키를 가지고 객체를 만들다 보면 사소한 결함이 발견된다. 다른 문자열은 괜찮지만 `"__proto__"`라는 문자열은 키로 사용할 수 없다.
```js
let obj = {};

let key = prompt('입력하고자 하는 key는 무엇인가요?', '__proto__');
obj[key] = '...값...';

alert(obj[key]); // '...값...'이 아닌 [object Object]가 출력됩니다.
```
`__proto__`는 항상 객체이거나 `null`이어야 한다. 문자열은 프로토타입이 될 수 없다.

예시에서는 이 버그가 그리 치명적이지 않다. 그런데 할당 값이 객체일 때는 프로토타입이 바뀔 수 있다는 치명적인 버그가 발생할 수 있다. 프로토타입이 바뀌면 예상치 못한 일이 발생할 수 있기 때문이다.

개발자들은 대개 프로토타입이 중간에 바뀌는 시나리오는 배제하고 개발을 진행한다. 이런 고정관념 때문에 프로토타입이 중간에 바뀌면서 발생한 버그는 그 원인을 쉽게 찾지 못한다. 서버 사이드에서 자바스크립트를 사용 할 때는 이런 버그가 취약점이 되기도 한다.

이 문제를 해결하기 위해서는 객체 대신 맵을 사용하면 된다. 또는 객체를 사용하면서 다음 방법을 이용할 수 있다.

이전에 배웠듯이 `__proto__`는 객체의 프로퍼티가 아니라 `Object.prototype`의 접근자 프로퍼티이다.

![object-prototype-2](https://user-images.githubusercontent.com/95019875/166463100-b45f5275-ac78-4581-abb4-1b311a48ec0b.svg)

간단한 트릭을 사용해서 객체가 연관 배열의 역할을 다 하도록 만들 수 있다.
```js
let obj = Object.create(null);

let key = prompt('입력하고자 하는 key는 무엇인가요?', '__proto__');
obj[key] = '...값...';

alert(obj[key]); // '...값...'이 제대로 출력된다.
```
`Object.create(null)`을 사용해 프로토타입이 없는 빈 객체를 만들었다. 이 객체는 `__proto__` getter와 setter를 상속받지 않는다.

이제 `__proto__`는 평범한 데이터 프로퍼티처럼 처리되므로 문제 없이 예시가 잘 동작하게 된다.

이렇게 프로토타입이 없는 빈 객체는 ‘아주 단순한(very plain)’ 혹은 ‘순수 사전식(pure dictionary)’ 객체라고 부른다. 일반 객체  `{...}`  보다 훨씬 단순하다. 참고로 아주 단순한 객체는 내장 메서드가 없다는 단점이 있다. `toString`같은 메서드를 사용할 수 없다.

하지만 객체를 연관 배열로 쓸 때는 이런 단점이 문제가 되진 않는다.

객체 관련 메서드 대부분은 `Object.keys(obj)` 같이 `Object.something(...)` 형태를 가진다. 이 메서드는 프로토타입에 있는 것이 아니기 때문에 '아주 단순한 객체’에도 사용할 수 있다.
```js
let chineseDictionary = Object.create(null);
chineseDictionary.hello = '你好';
chineseDictionary.bye = '再见';

alert(Object.keys(chineseDictionary)); // hello,bye
```

관련된 메서드는 다음과 같다.
-   [Object.keys(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)  /  [Object.values(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/values)  /  [Object.entries(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)  –  `obj`  내 열거 가능한 프로퍼티 키, 값, 키-값 쌍을 담은 배열을 반환한다.
-   [Object.getOwnPropertySymbols(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols)  –  `obj`  내 심볼형 키를 담은 배열을 반환한다.
-   [Object.getOwnPropertyNames(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames)  –  `obj`  내 문자형 키를 담은 배열을 반환한다.
-   [Reflect.ownKeys(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Reflect/ownKeys)  –  `obj`내 키 전체를 담은 배열을 반환한다.
-   [obj.hasOwnProperty(key)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty)  – 상속받지 않고  `obj`  자체에 구현된 키 중 이름이  `key`인 것이 있으면  `true`를 반환한다.

`Object.keys`를 비롯하여 객체의 프로퍼티를 반환하는 메서드들은 객체가 ‘직접 소유한’ 프로퍼티만 반환한다. 상속 프로퍼티는 `for..in`을 사용해 얻을 수 있다.
