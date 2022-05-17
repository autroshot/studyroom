# 'instanceof'로 클래스 확인하기
## instanceof 연산자
문법:
```js
obj instanceof Class
```
`obj`가 `Class`에 속하거나 `Class`를 상속받는 클래스에 속하면 `true`가 반환된다.
```js
class Rabbit {}
let rabbit = new Rabbit();

alert(rabbit instanceof Rabbit); // true
```
`instanceof`는 생성자 함수에서도 사용할 수 있다.
```js
// 클래스가 아닌 생성자 함수이다.
function Rabbit() {}

alert(new Rabbit() instanceof Rabbit); // true
```
`Array` 같은 내장 클래스에도 사용할 수 있다.
```js
let arr = [1, 2, 3];
alert(arr instanceof Array); // true
alert(arr instanceof Object); // true (`Array`는 프로토타입 기반으로 `Object`를 상속받는다)
```
정적 메서드 `Symbol.hasInstance`을 사용하면 직접 확인 로직을 설정할 수 있다.

클래스에 정적 메서드 `Symbol.hasInstance`가 구현되어 있으면, `obj instanceof Class`문이 실행될 때, `Class[Symbol.hasInstance](obj)`가 호출된다. 호출 결과는 `true`나 `false`여야 한다.
```js
// canEat 프로퍼티가 있으면 animal이라고 판단할 수 있도록
// instanceOf의 로직을 직접 설정한다.
class Animal {
  static [Symbol.hasInstance](obj) {
    if (obj.canEat) return true;
  }
}

let obj = { canEat: true };

alert(obj instanceof Animal); // true, Animal[Symbol.hasInstance](obj)가 호출된다.
```
대부분의 클래스에는 `Symbol.hasInstance`가 구현되어 있지 않다. 이럴 때는 일반적인 로직이 사용된다. `obj instanceOf Class`는 `Class.prototype`이 `obj` 프로토타입 체인 상의 프로토타입 중 하나와 일치하는지 확인한다.
```js
obj.__proto__ === Class.prototype?
obj.__proto__.__proto__ === Class.prototype?
obj.__proto__.__proto__.__proto__ === Class.prototype?
...
// 이 중 하나라도 true이면 true를 반환한다.
```
한편, `objA`가 `objB`의 프로토타입 체인 상의 어딘가에 있으면 `true`를 반환해주는 메서드, [objA.isPrototypeOf(objB)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/object/isPrototypeOf)도 있다. `obj instanceof Class`는 `Class.prototype.isPrototypeOf(obj)`와 동일하다.

`isPrototypeOf`는 `Class` 생성자를 제외하고 포함 여부를 검사하는 점이 조금 특이하다. 프로토타입 체인과 `Class.prototype`만 고려한다.

`isPrototypeOf`의 이런 특징 때문에 다음과 같이 객체 생성 후 `prototype` 프로퍼티가 변경되는 경우 `false`를 반환한다.
```js
function Rabbit() {}
let rabbit = new Rabbit();

// 프로토타입이 변경되었다.
Rabbit.prototype = {};

// 더 이상 Rabbit이 아니다.
alert(rabbit instanceof Rabbit); // false
```

## 보너스: 타입 확인을 위한 Object.prototype.toString
일반 객체를 문자열로 변화하면 `[object Object]`가 된다.
```js
let obj = {};

alert(obj); // [object Object]
alert(obj.toString()); // 같은 결과가 출력된다.
```
이렇게 `[object Object]`가 되는 이유는 `toString`의 구현방식 때문이다. 그런데 `toString`에는 `toString`을 더 강력하게 만들어주는 기능이 숨겨져 있다. `toString`의 숨겨진 기능을 사용하면 확장 `typeof`, `instanceof`의 대안을 만들 수 있다.

객체에서 내장 `toString`을 추출하는 게 가능하다. 이렇게 추출한 메서드는 모든 값을 대상으로 실행할 수 있다. 호출 결과는 값에 따라 달라진다.
-   숫자형 –  `[object Number]`
-   불린형 –  `[object Boolean]`
-   `null`  –  `[object Null]`
-   `undefined`  –  `[object Undefined]`
-   배열 –  `[object Array]`
-   그외 – 커스터마이징 가능

```js
// 편의를 위해 toString 메서드를 변수에 복사한다.
let objectToString = Object.prototype.toString;

let arr = [];

alert(objectToString.call(arr)); // [object Array]
```
[call/apply와 데코레이터, 포워딩](https://github.com/autroshot/studyroom/tree/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8#9-callapply%EC%99%80-%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0-%ED%8F%AC%EC%9B%8C%EB%94%A9) 챕터에서 배운 `call`을 사용해 컨텍스트를 `this=arr`로 설정하고 함수 `objectToString`를 실행하였다.

`toString` 알고리즘은 내부적으로 `this`를 검사하고 상응하는 결과를 반환한다.
```js
let s = Object.prototype.toString;

alert(s.call(123)); // [object Number]
alert(s.call(null)); // [object Null]
alert(s.call(alert)); // [object Function]
```

### Symbol.toStringTag
특수 객체 프로퍼티 `Symbol.toStringTag`를 사용하면 `toString`의 동작을 커스터마이징 할 수 있다.
```js run
let user = {
  [Symbol.toStringTag]: 'User'
};

alert({}.toString.call(user)); // [object User]
```
대부분의 호스트 환경은 자체 객체에 이와 유사한 프로퍼티를 구현해 놓고 있다.
```js
// 특정 호스트 환경의 객체와 클래스에 구현된 toStringTag이다.
alert(window[Symbol.toStringTag]); // Window
alert(XMLHttpRequest.prototype[Symbol.toStringTag]); // XMLHttpRequest

alert({}.toString.call(window)); // [object Window]
alert({}.toString.call(new XMLHttpRequest())); // [object XMLHttpRequest]
```
실행 결과에서 보듯이 호스트 환경 고유 객체의 `Symbol.toStringTag` 값은 `[object ...]`로 쌓여진 값과 동일하다.

내장 객체의 타입 확인을 넘어서 타입을 문자열 형태로 받고 싶다면 `instanceof` 대신, `{}.toString.call`을 사용할 수 있다.

## 요약
|               | 동작 대상      |  반환값      |
|---------------|-------------|---------------|
| `typeof`      | 원시형  |  문자열       |
| `{}.toString` | 원시형, 내장 객체, `Symbol.toStringTag`가 있는 객체   |       문자열 |
| `instanceof`  | 객체     |  true나 false   |

`{}.toString`은 `typeof`보다 기능이 더 많다.

`instanceof` 연산자는 계층 구조를 가진 클래스를 다룰 때나 클래스의 상속 여부를 확인하고자 할 때 그 진가를 발휘한다.
