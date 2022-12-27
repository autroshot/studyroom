# Proxy와 Reflect
`Proxy`는 특정 객체를 감싸 프로퍼티 읽기, 쓰기와 같은 객체에 가해지는 작업을 중간에서 가로채는 객체이다. 가로챈 작업은 `Proxy` 자체에서 처리되기도 하고, 원래 객체가 처리하도록 그대로 전달되기도 한다.

## Proxy
문법:
```js
let proxy = new Proxy(target, handler)
```
- `target` -- 감싸게 될 객체로, 함수를 포함한 모든 객체가 가능하다.
- `handler` -- 동작을 가로채는 메서드인 '트랩(trap)'이 담긴 객체로, 여기서 프락시를 설정한다.

`proxy`에 작업이 가해지고, `handler`에 작업과 상응하는 트랩이 있으면 트랩이 실행되어 프락시가 이 작업을 처리할 기회를 얻게 된다. 트랩이 없으면 `target`에 작업이 직접 수행된다.

다음은 트랩이 없는 프락시를 사용한 예시이다.
```js run
let target = {};
let proxy = new Proxy(target, {}); // 빈 핸들러

proxy.test = 5; // 프락시에 값을 쓴다. - (1)
alert(target.test); // 5, target에 새로운 프로퍼티가 생겼다.

alert(proxy.test); // 5, 프락시를 사용해 값을 읽을 수도 있다. - (2)

for(let key in proxy) alert(key); // test, 반복도 잘 동작한다. - (3)
```
위 예시의 프락시에는 트랩이 없기 때문에 `proxy`에 가해지는 모든 작업은 `target`에 전달된다.
1. `proxy.test =`를 이용해 값을 쓰면 `target`에 새로운 값이 설정된다.
2. `proxy.test`를 이용해 값을 읽으면 `target`에서 값을 읽어온다.
3. `proxy`를 대상으로 반복 작업을 하면 `target`에 저장된 값이 반환된다.

트랩이 없으면 `proxy`는 `target`을 감싸는 '투명한 래퍼'가 된다.

`Proxy`는 일반 객체와는 다른 행동 양상을 보이는 '특수 객체(exotic object)'이다. 프로퍼티가 없다.

객체에 어떤 작업을 할 때는 자바스크립트 명세서에 정의된 '내부 메서드(internal method)'가 깊숙한 곳에서 관여한다. 프로퍼티를 읽을 때는 `[[Get]]`이라는 내부 메서드가, 프로퍼티에 쓸 때는 `[[Set]]`이라는 내부 메서드가 관여한다. 이런 내부 메서드들은 명세서에만 정의된 메서드이기 때문에 개발자가 코드를 사용해 호출할 수는 없다.

프락시의 트랩은 내부 메서드의 호출을 가로챈다. 프락시가 가로채는 내부 메서드 리스트는 아래 표에서 확인할 수 있다.

모든 내부 메서드에는 대응하는 트랩이 있다. `new Proxy`의 `handler`에 매개변수로 추가할 수 있는 메서드 이름은 아래 표의 ‘핸들러 메서드’ 열에서 확인할 수 있다.
| 내부 메서드 | 핸들러 메서드 | 작동 시점 |
|-----------------|----------------|-------------|
| `[[Get]]` | `get` | 프로퍼티를 읽을 때 |
| `[[Set]]` | `set` | 프로퍼티에 쓸 때 |
| `[[HasProperty]]` | `has` | `in` 연산자가 동작할 때 |
| `[[Delete]]` | `deleteProperty` | `delete` 연산자가 동작할 때 |
| `[[Call]]` | `apply` | 함수를 호출할 때 |
| `[[Construct]]` | `construct` | `new` 연산자가 동작할 때 |
| `[[GetPrototypeOf]]` | `getPrototypeOf` | [Object.getPrototypeOf](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf) |
| `[[SetPrototypeOf]]` | `setPrototypeOf` | [Object.setPrototypeOf](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf) |
| `[[IsExtensible]]` | `isExtensible` | [Object.isExtensible](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/isExtensible) |
| `[[PreventExtensions]]` | `preventExtensions` | [Object.preventExtensions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/preventExtensions) |
| `[[DefineOwnProperty]]` | `defineProperty` | [Object.defineProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty), [Object.defineProperties](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperties) |
| `[[GetOwnProperty]]` | `getOwnPropertyDescriptor` | [Object.getOwnPropertyDescriptor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor), `for..in`, `Object.keys/values/entries` |
| `[[OwnPropertyKeys]]` | `ownKeys` | [Object.getOwnPropertyNames](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames), [Object.getOwnPropertySymbols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols), `for..in`, `Object/keys/values/entries` |

---
:warning: **규칙**

내부 메서드나 트랩을 쓸 때는 몇 가지 규칙을 지켜야 한다. 대부분의 규칙은 반환 값과 관련되어 있다.
- 값을 쓰는 것이 성공적으로 처리되었으면 `[[Set]]`은 반드시 `true`를 반환해야 합니다. 그러지 않은 경우는 `false`를 반환해야 한다.
- 값을 지우는 것이 성공적으로 처리되었으면 `[[Delete]]`는 반드시 `true`를 반환해야 한다. 그러지 않은 경우는 `false`를 반환해야 한다.
- 기타 등등(아래 예시에서 살펴볼 것이다)

이 외에 다른 조건도 있다.
- 프락시 객체를 대상으로 `[[GetPrototypeOf]]`가 적용되면 프락시 객체의 타깃 객체에 `[[GetPrototypeOf]]`를 적용한 것과 동일한 값이 반환되어야 한다. 프락시의 프로토타입을 읽는 것은 타깃 객체의 프로토타입을 읽는 것과 동일해야 한다. 

이와 같은 규칙은 자바스크립트가 일관된 동작을 하고 잘못된 동작이 있으면 이를 고쳐주는 역할을 합니다. 규칙 목록은 [명세서](https://tc39.es/ecma262/#sec-proxy-object-internal-methods-and-internal-slots)에서 확인할 수 있습니다. 이 규칙을 어길 일은 거의 없다.

---

## get 트랩으로 프로퍼티 기본값 설정하기
가장 흔히 볼 수 있는 트랩은 프로퍼티를 읽거나 쓸 때 사용되는 트랩이다.

프로퍼티 읽기를 가로채려면 `handler`에 `get(target, property, receiver)` 메서드가 있어야 한다.

`get`메서드는 프로퍼티를 읽으려고 할 때 작동한다. 인수는 다음과 같다.
- `target` -- 동작을 전달할 객체로 `new Proxy`의 첫 번째 인자이다.
- `property` -- 프로퍼티 이름이다.
- `receiver` -- 타깃 프로퍼티가 getter라면 `receiver`는 getter가 호출될 때의 `this` 이다. 보통은 `proxy` 객체 자신이 `this`가 된다. 프락시 객체를 상속받은 객체가 있다면 해당 객체가 `this`가 되기도 한다. 더 자세한 내용은 나중에 다룰 것이다.

`get`을 활용해 객체에 기본값을 설정해 보겠다.

존재하지 않는 요소를 읽으려고 하면 배열은 원래 `undefined`을 반환하는데, 예시에서는 배열(객체)을 프락시로 감싸서 존재하지 않는 요소(프로퍼티)를 읽으려고 할 때 `0`이 반환되도록 해 보겠다.
```js
let numbers = [0, 1, 2];

numbers = new Proxy(numbers, {
  get(target, prop) {
    if (prop in target) {
      return target[prop];
    } else {
      return 0; // 기본값
    }
  }
});

alert(numbers[1]); // 1
alert(numbers[123]); // 0 (해당하는 요소가 배열에 없으므로 0이 반환된다)
```
`Proxy`를 사용하면 기본 값 설정 로직을 원하는 대로 구현할 수 있다.

이번에는 구절과 번역문이 저장되어있는 사전이 있다고 가정해 보겠다. 사전에 없는 구절에 접근하면 `undefined` 아닌 구절 그대로를 반환하게 만들어 보겠다.
```js
let dictionary = {
  'Hello': '안녕하세요',
  'Bye': '안녕히 가세요'
};

dictionary = new Proxy(dictionary, {
  get(target, phrase) { // 프로퍼티를 읽기를 가로챈다.
    if (phrase in target) { // 조건: 사전에 구절이 있는 경우
      return target[phrase]; // 번역문을 반환한다.
    } else {
      // 구절이 없는 경우에는 구절 그대로를 반환한다.
      return phrase;
    }
  }
});

// 사전에 없는 구절을 입력하면 입력값이 그대로 반환된다.
alert( dictionary['Hello'] ); // 안녕하세요
alert( dictionary['Welcome to Proxy']); // Welcome to Proxy (입력값이 그대로 출력된다)
```
---
:information_source: **주의**

프락시 객체가 변수를 어떻게 덮어쓰고 있는지 주목해야 한다.
```js
dictionary = new Proxy(dictionary, ...);
```
타깃 객체의 위치와 상관없이 **프락시 객체는 타깃 객체를 덮어써야만 한다.** 객체를 프락시로 감싼 이후에는 절대로 타깃 객체를 참조하는 코드가 없어야 한다. 그러지 않으면 엉망이 될 확률이 아주 높아진다.

---

## set 트랩으로 프로퍼티 값 검증하기
숫자만 저장할 수 있는 배열을 만들고 싶다면, 숫자형이 아닌 값을 추가하려고 하면 에러가 발생하게 만들면 될 것이다. 프로퍼티에 값을 쓰려고 할 때 이를 가로채는 `set` 트랩을 사용해 이를 구현해 보겠다.

`set` 메서드의 인수는 다음과 같은 역할을 한다.

`set(target, property, value, receiver)`:

- `target` -- 동작을 전달할 객체로 `new Proxy`의 첫 번째 인자이다.
- `property` -- 프로퍼티 이름이다.
- `value` -- 프로퍼티 값이다.
- `receiver` -- `get` 트랩과 유사하게 동작하는 객체로, setter 프로퍼티에만 관여한다.

우리가 구현해야 할 `set` 트랩은 숫자형 값을 설정하려 할 때만 `true`를, 그러지 않은 경우에는(`TypeError`가 트리거되고) `false`를 반환하도록 해야 한다.    

`set` 트랩을 사용해 배열에 추가하려는 값이 숫자형인지 검증해 보겠다.
```js
let numbers = [];

numbers = new Proxy(numbers, {
  set(target, prop, val) { // 프로퍼티에 값을 쓰는 동작을 가로챈다.
    if (typeof val == 'number') {
      target[prop] = val;
      return true;
    } else {
      return false;
    }
  }
});

numbers.push(1); // 추가가 성공했다.
numbers.push(2); // 추가가 성공했다.
alert("Length is: " + numbers.length); // 2

numbers.push('test'); // Error: 'set' on proxy

alert('윗줄에서 에러가 발생했기 때문에 이 줄은 절대 실행되지 않습니다.');
```
배열 관련 기능들은 여전히 사용할 수 있다는 점을 주목해야 한다. `push` 메서드와 `length` 프로퍼티가 잘 작동한다. 프락시를 사용해도 기존에 있던 기능은 절대로 손상되지 않는다.

`push`나 `unshift` 같이 배열에 값을 추가해주는 메서드들은 내부에서 `[[Set]]`을 사용하고 있기 때문에 메서드를 오버라이드 하지 않아도 프락시가 동작을 가로채고 값을 검증해준다.

---
:warning: **`true`를 잊지 말고 반환해줘야 한다.**

위에서 언급했듯이 값을 쓰는 것이 성공했을 때 반드시 `true`를 반환해줘야 한다.

`true`를 반환하지 않거나 falsy한 값을 반환하게 되면 `TypeError`가 발생한다.

---

## ownKeys와 getOwnPropertyDescriptor로 반복 작업하기
`Object.keys`, `for..in` 반복문을 비롯한 프로퍼티 순환 관련 메서드 대다수는 내부 메서드 `[[OwnPropertyKeys]]`를 사용해 프로퍼티 목록을 얻는다.

세부 동작 방식에는 다음과 같은 차이가 있다.
- `Object.getOwnPropertyNames(obj)` -- 심볼형이 아닌 키만 반환한다.
- `Object.getOwnPropertySymbols(obj)` -- 심볼형 키만 반환한다.
- `Object.keys/values()` -- `enumerable` 플래그가 `true`이면서 심볼형이 아닌 키나 심볼형이 아닌 키에 해당하는 값 전체를 반환한다. 프로퍼티 플래그에 관한 자세한 내용은 [프로퍼티 플래그와 설명자](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/07-%EA%B0%9D%EC%B2%B4%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%20%EC%84%A4%EC%A0%95/01-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%20%ED%94%8C%EB%9E%98%EA%B7%B8%EC%99%80%20%EC%84%A4%EB%AA%85%EC%9E%90.md#%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-%ED%94%8C%EB%9E%98%EA%B7%B8%EC%99%80-%EC%84%A4%EB%AA%85%EC%9E%90) 챕터에서 볼 수 있다.
- `for..in` 반복문 -- `enumerable` 플래그가 `true`인 심볼형이 아닌 키, 프로토타입 키를 순회한다.

다음 예시에서는 `ownKeys` 트랩을 사용해 `_`로 시작하는 프로퍼티는 `for..in` 반복문의 순환 대상에서 제외하도록 만들어 보겠다. `ownKeys`를 사용했기 때문에 `Object.keys`와 `Object.values`에도 동일한 로직이 적용되는 것을 확인할 수 있다.
```js run
let user = {
  name: 'John',
  age: 30,
  _password: '***'
};

user = new Proxy(user, {
  ownKeys(target) {
    return Object.keys(target).filter(key => !key.startsWith('_'));
  }
});

// "ownKeys" 트랩은 _password를 건너뛴다.
for(let key in user) alert(key); // name, age

// 아래 두 메서드에도 동일한 로직이 적용된다.
alert(Object.keys(user)); // name,age
alert(Object.values(user)); // John,30
```
객체 내에 존재하지 않는 키를 반환하려고 하면 `Object.keys`는 이 키를 제대로 보여주지 않는다.

```js run
let user = {};

user = new Proxy(user, {
  ownKeys(target) {
    return ['a', 'b', 'c'];
  }
});

alert(Object.keys(user)); // <빈 문자열>
```
`Object.keys`는 `enumerable` 플래그가 있는 프로퍼티만 반환하기 때문이다. 이를 확인하기 위해 `Object.keys`는 내부 메서드인 `[[GetOwnProperty]]`를 호출해 모든 프로퍼티의 설명자를 확인한다. 위 예시의 프로퍼티는 설명자가 하나도 없고 `enumerable` 플래그도 없으므로 순환 대상에서 제외된 것이다.

`Object.keys` 호출 시 프로퍼티를 반환하게 하려면 `enumerable` 플래그를 붙여줘 프로퍼티가 객체에 존재하도록 하거나 `[[GetOwnProperty]]`가 호출될 때 이를 중간에서 가로채서 설명자 `enumerable: true`를 반환하게 해주면 된다.  `getOwnPropertyDescriptor` 트랩이 바로 이때 사용된다.

예시:
```js
let user = {};

user = new Proxy(user, {
  ownKeys(target) { // 프로퍼티 리스트를 얻을 때 한 번만 호출된다.
    return ['a', 'b', 'c'];
  },

  getOwnPropertyDescriptor(target, prop) { // 모든 프로퍼티를 대상으로 호출된다.
    return {
      enumerable: true,
      configurable: true
      /* 이 외의 플래그도 반환할 수 있다. "value:..."도 가능하다. */
    };
  }

});

alert(Object.keys(user)); // a, b, c
```
객체에 프로퍼티가 없을 때는 `[[GetOwnProperty]]`을 가로채면 된다는 점을 주목해야 한다.

## deleteProperty와 여러 트랩을 사용해 프로퍼티 보호하기
`_`(밑줄)이 앞에 붙은 프로퍼티나 메서드는 내부용으로만 쓰는 것은 널리 알려진 관습이다. 이것들은 객체 바깥에서 접근하면 안 된다. 

그런데 기술적으로는 가능하다.
```js
let user = {
  name: 'John',
  _password: '비밀'
};

alert(user._password); // 비밀  
```
프락시를 사용해 `_`로 시작하는 프로퍼티에 접근하지 못하도록 막아 보겠다.

원하는 기능을 구현하려면 다음과 같은 트랩이 필요하다.
- `get` -- 프로퍼티를 읽으려고 하면 에러를 던져준다.
- `set` -- 프로퍼티에 쓰려고 하면 에러를 던져준다.
- `deleteProperty` -- 프로퍼티를 지우려고 하면 에러를 던져준다.
- `ownKeys` -- `for..in`이나 `Object.keys`같은 프로퍼티 순환 메서드를 사용할 때 `_`로 시작하는 메서드는 제외한다.

구현 결과는 다음과 같다.
```js
let user = {
  name: 'John',
  _password: '***'
};

user = new Proxy(user, {
  get(target, prop) {
    if (prop.startsWith('_')) {
      throw new Error('접근이 제한되어있습니다.');
    }
    let value = target[prop];
    return (typeof value === 'function') ? value.bind(target) : value;
  },
  set(target, prop, val) { // 프로퍼티 쓰기를 가로챈다.
    if (prop.startsWith('_')) {
      throw new Error('접근이 제한되어있습니다.');
    } else {
      target[prop] = val;
      return true;
    }
  },
  deleteProperty(target, prop) { // 프로퍼티 삭제를 가로챈다.
    if (prop.startsWith('_')) {
      throw new Error('접근이 제한되어있습니다.');
    } else {
      delete target[prop];
      return true;
    }
  },
  ownKeys(target) { // 프로퍼티 순회를 가로챈다.
    return Object.keys(target).filter(key => !key.startsWith('_'));
  }
});

// "get" 트랩이 _password 읽기를 막는다.
try {
  alert(user._password); // Error: 접근이 제한되어있습니다.
} catch(e) { alert(e.message); }

// "set" 트랩이 _password에 값을 쓰는것을 막는다.
try {
  user._password = "test"; // Error: 접근이 제한되어있습니다.
} catch(e) { alert(e.message); }

// "deleteProperty" 트랩이 _password 삭제를 막는다.
try {
  delete user._password; // Error: 접근이 제한되어있습니다.
} catch(e) { alert(e.message); }

// "ownKeys" 트랩이 순회 대상에서 _password를 제외시킨다.
for(let key in user) alert(key); // name
```
`get` 트랩 부분을 자세히 살펴보겠다.
```js
get(target, prop) {
  // ...
  let value = target[prop];
  return (typeof value === 'function') ? value.bind(target) : value; // (*)
}
```
함수인지 여부를 확인하여 `value.bind(target)`를 호출하고 있다.

이렇게 하는 이유는 `user.checkPassword()` 같은 객체 메서드의 `_password` 접근에 문제가 없어야 하기 때문이다.
```js
user = {
  // ...
  checkPassword(value) {
    // checkPassword(비밀번호 확인)는 _password를 읽을 수 있어야 한다.
    return value === this._password;
  }
}
```
`user.checkPassword()`를 호출하면 점 앞의 객체가 `this`가 되므로 프락시로 감싼 `user`에 접근하게 되는데, `this._password`는 `get` 트랩을 활성화하므로 에러가 던져진다.

`(*)`로 표시한 줄에서 객체 메서드의 컨텍스트를 원본 객체인 `target`에 바인딩시켜준 이유가 바로 여기에 있다. `checkPassword()`를 호출할 때는 언제든 트랩 없이 `target`이 `this`가 된다.

이 방법은 대부분 잘 작동하긴 하는데 메서드가 어딘가에서 프락시로 감싸지 않은 객체를 넘기게 되면 엉망이 되어버리기 때문에 이상적인 방법은 아니다. 기존 객체와 프락시로 감싼 객체가 어디에 있는지 파악할 수 없기 때문이다.

객체를 서로 다른 프락시로 여러 번 감싸거나, 프락시로 감싸지 않은 객체를 메서드에 전달하면 예상하지 못한 결과가 나올 수 있다.

따라서 이런 형태의 프락시는 어디서든 사용해서는 안 된다.

---
:information_source: **클래스와 private 프로퍼티**

모던 자바스크립트에서는 클래스 내 private 프로퍼티를 사용할 수 있다. private 프로퍼티는 프로퍼티 앞에 `#`을 붙이면 만들 수 있는데, 자세한 내용은 [private, protected 프로퍼티와 메서드](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/09-%ED%81%B4%EB%9E%98%EC%8A%A4/04-private%2C%20protected%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%EC%99%80%20%EB%A9%94%EC%84%9C%EB%93%9C.md#private-protected-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%EC%99%80-%EB%A9%94%EC%84%9C%EB%93%9C) 챕터에서 찾아볼 수 있다. private 프로퍼티를 사용하면 프락시 없이도 프로퍼티를 보호할 수 있다.

private 프로퍼티는 상속이 불가능하다는 단점이 있다.

---

## has 트랩으로 ‘범위’ 내 여부 확인하기
예시:
```js
let range = {
  start: 1,
  end: 10
};
```

`in` 연산자를 사용해 특정 숫자가 `range` 내에 있는지 확인해 보겠다.

`has` 트랩은 `in` 호출을 가로챈다. 사용법은 간단하다.

`has(target, property)`
- `target` -- `new Proxy`의 첫 번째 인자로 전달되는 타깃 객체이다.
- `property` -- 프로퍼티 이름이다.

예시:
```js run
let range = {
  start: 1,
  end: 10
};

range = new Proxy(range, {
  has(target, prop) {
    return prop >= target.start && prop <= target.end;
  }
});

alert(5 in range); // true
alert(50 in range); // false
```

## apply 트랩으로 함수 감싸기
함수 역시 프락시로 감쌀 수 있다.

`apply(target, thisArg, args)` 트랩은 프락시를 함수처럼 호출하려고 할 때 동작한다.
- `target` -- 타깃 객체이다. 자바스크립트에서 함수는 객체이다.
- `thisArg` -- `this`의 값이다.
- `args` -- 인수 목록이다.

[call/apply와 데코레이터, 포워딩](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5/09-call%C2%B7apply%EC%99%80%20%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0%2C%20%ED%8F%AC%EC%9B%8C%EB%94%A9.md#callapply%EC%99%80-%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0-%ED%8F%AC%EC%9B%8C%EB%94%A9) 챕터의 `delay(f, ms)` 데코레이터(decorator)를 예시로 사용하겠다.

해당 챕터에서는 프락시를 사용하지 않고 데코레이터를 구현했다. `delay(f, ms)`를 호출하면 함수가 반환되는데, 이 함수는 함수 `f`가 `ms`밀리초 후에 호출되게 해준다.  

함수를 기반으로 작성했던 데코레이터는 다음과 같다.
```js
function delay(f, ms) {
  // 지정한 시간이 흐른 다음에 f 호출을 전달해주는 래퍼 함수를 반환한다.
  return function() { // (*)
    setTimeout(() => f.apply(this, arguments), ms);
  };
}

function sayHi(user) {
  alert(`Hello, ${user}!`);
}

// 래퍼 함수로 감싼 다음에 sayHi를 호출하면 3초 후 함수가 호출된다.
sayHi = delay(sayHi, 3000);

sayHi('John'); // Hello, John! (3초 후)
```
이미 살펴봤듯이 이 데코레이터는 대부분의 경우 잘 동작한다. `(*)`로 표시 한곳의 래퍼 함수는 일정 시간 후 함수를 호출할 수 있게 해준다. 

그런데 래퍼 함수는 프로퍼티 읽기/쓰기 등의 연산은 전달해주지 못한다. 래퍼 함수로 감싸고 난 다음에는 기존 함수의 프로퍼티(`name`, `length` 등) 정보가 사라진다.
```js run
function delay(f, ms) {
  return function() {
    setTimeout(() => f.apply(this, arguments), ms);
  };
}

function sayHi(user) {
  alert(`Hello, ${user}!`);
}

alert(sayHi.length); // 1 (함수 정의부에서 명시한 인수의 개수이다)

sayHi = delay(sayHi, 3000);

alert(sayHi.length); // 0 (래퍼 함수 정의부에는 인수가 없다)
```
`Proxy` 객체는 `target` 객체에 모든 것을 전달해주므로 훨씬 강력하다.

래퍼 함수 대신 `Proxy`를 사용해 보겠다.
```js
function delay(f, ms) {
  return new Proxy(f, {
    apply(target, thisArg, args) {
      setTimeout(() => target.apply(thisArg, args), ms);
    }
  });
}

function sayHi(user) {
  alert(`Hello, ${user}!`);
}

sayHi = delay(sayHi, 3000);

alert(sayHi.length); // 1, 프락시는 "get length" 연산까지 타깃 객체에 전달해준다.

sayHi('John'); // Hello, John! (3초 후)
```
이번에는 호출뿐만 아니라 프락시에 가하는 모든 연산이 원본 함수에 전달된 것을 확인할 수 있다. 좀 더 성능이 좋은 래퍼를 갖게 되었다.

## Reflect
`Reflect`는 프락시 생성을 단순화하는 내장 객체이다.

이전에도 얘기했듯이 `[[Get]]`, `[[Set]]`은 내부 메서드이므로 직접 호출할 수 없다.

`Reflect` 객체는 이것을 어느 정도 가능하게 만들어준다. 객체의 메서드들은 내부 메서드를 감싸는 최소한의 래퍼이다.

다음은 연산별 대응되는 `Reflect` 호출이다.
| 연산 |  `Reflect` 호출 | 내부 메서드 |
|-----------------|----------------|-------------|
| `obj[prop]` | `Reflect.get(obj, prop)` | `[[Get]]` |
| `obj[prop] = value` | `Reflect.set(obj, prop, value)` | `[[Set]]` |
| `delete obj[prop]` | `Reflect.deleteProperty(obj, prop)` | `[[Delete]]` |
| `new F(value)` | `Reflect.construct(F, value)` | `[[Construct]]` |
| ... | ... | ... |

예시:
```js 
let user = {};

Reflect.set(user, 'name', 'John');

alert(user.name); // John
```
특히 `Reflect`를 사용하면 연산자(`new`, `delete`, ...)를 함수(`Reflect.construct`, `Reflect.deleteProperty`, ...)로 호출할 수 있다. 이것은 흥미로운 능력이지만, 더 중요한 특징이 있다.

프락시에 의해 트랩될 수 있는 모든 내부 메서드에 대해, Reflect에는 프락시 트랩과 동일한 이름과 인수를 가진 메서드가 존재한다. 따라서 `Reflect`를 사용해 작업을 원래 개체로 전달할 수 있다.

다음 예시에서는 `get`/`set` 트랩 모두 읽기/쓰기 작업을 객체에게 투명하게 전달한다.
```js
let user = {
  name: 'John',
};

user = new Proxy(user, {
  get(target, prop, receiver) {
    alert(`GET ${prop}`);
    return Reflect.get(target, prop, receiver); // (1)
  },
  set(target, prop, val, receiver) {
    alert(`SET ${prop}=${val}`);
    return Reflect.set(target, prop, val, receiver); // (2)
  }
});

let name = user.name; // "GET name"
user.name = 'Pete'; // "SET name=Pete"
```
- `Reflect.get`은 객체의 프로퍼티를 읽는다.
- `Reflect.set`은 객체의 프로퍼티를 쓰고 성공하면 `true`를, 실패하면 `false`를 반환한다.

매우 간단하다. 트랩이 호출을 객체로 전달하려면 매개변수가 동일한 `Reflect.<method>`를 호출하기만 하면 된다.

대부분의 경우에는 `Reflect` 없이도 동일한 작업을 할 수 있다. 예를 들어 프로퍼티를 읽는 `Reflect.get(target, prop, receiver)`은 `target[prop]`로 대체할 수 있다. 하지만 미묘하지만 중요한 차이가 있다.

### getter를 프락시하기
`Reflect.get`이 더 좋은 이유를 설명해주는 예시를 살펴보겠다. 또한 `get/set`에 세 번째 인수 `receiver`가 왜 있는지도 알아보겠다.

예시의 객체에는 `_name` 프로퍼티와 이 프로퍼티의 getter가 있다. 그리고 객체를 프락시로 감쌌다.
```js
let user = {
  _name: 'Guest',
  get name() {
    return this._name;
  }
};

let userProxy = new Proxy(user, {
  get(target, prop, receiver) {
    return target[prop];
  }
});

alert(userProxy.name); // Guest
```
이곳의 `get` 트랩은 '투명'하다. 원래 프로퍼티를 반환할뿐 다른 작업은 수행하지 않는다.

모든 것이 괜찮아 보인다. 하지만 예시를 좀 더 복잡하게 만들어 보겠다.

`user`을 상속받은 `admin`에서 의도하지 않은 결과가 나온다.
```js
let user = {
  _name: 'Guest',
  get name() {
    return this._name;
  }
};

let userProxy = new Proxy(user, {
  get(target, prop, receiver) {
    return target[prop]; // (*) target = user
  }
});

let admin = {
  __proto__: userProxy,
  _name: 'Admin'
};

// 예상된 결과는 `Admin`이다.
alert(admin.name); // Guest
```
`admin.name`은 `Guest`가 아니라 `Admin`을 반환해야 한다.

문제는 `(*)` 줄의 프락시에 있다.
1. `admin.name`을 읽을 때, `admin` 객체에는 해당 프로퍼티가 없으므로 검색은 프로토타입으로 향한다.
2. 프로토타입은 `userProxy`이다.
3. 프락시에서 `name`프로퍼티를 읽을 때, `get` 트랩이 작동해서 원래 객체가 아닌 `(*)` 줄의 `target[prop]`을 반환한다.

    `target[prop]`을 호출하면, `prop`이 getter이므로 `this=target`인 컨텍스트에서 코드가 실행된다. 그 결과 `this._name`은 원래 객체인 `target`가 아닌 `user`의 이름이 된다.

이 상황을 해결하기 위해서는 `get` 트랩의 세 번째 인수인 `receiver`가 필요하다. 이 인수를 이용하면 getter에게 정확한 `this`를 전달할 수 있다.
```js
let user = {
  _name: 'Guest',
  get name() {
    return this._name;
  }
};

let userProxy = new Proxy(user, {
  get(target, prop, receiver) { // receiver = admin
    return Reflect.get(target, prop, receiver); // (*)
  }
});

let admin = {
  __proto__: userProxy,
  _name: 'Admin'
};

alert(admin.name); // Admin
```
이제 올바른 `this`(`admin`)에 대한 참조를 가진 `receiver`가 `(*)` 줄의 `Reflect.get`을 이용해 getter로 전달된다.

다음과 같이 트랩을 더 짧게 만들 수 있다.
```js
get(target, prop, receiver) {
  return Reflect.get(...arguments);
}
```
`Reflect` 호출은 트랩과 정확히 동일한 방식으로 이름이 붙여지고 동일한 인수를 지닌다. `Reflect`는 특별히 이런 방식으로 설계되었다.

따라서 `return Reflect...`는 별다른 고민 없이 안전하게 사용할 수 있으며 뭔가를 실수로 빠뜨리는 것도 방지해준다.

## 프락시의 한계
프락시는 가장 낮은 수준에서 기존 객체의 동작을 변경하거나 조정할 수 있는 고유한 방법을 제공한다. 그러나 완벽한 것은 아니며 한계가 존재한다.

### 내장 객체: 내부 슬롯
맵, 셋, `Date`, 프라미스 등과 같은 많은 내장 객체는 이른바 <strong>내부 슬롯(internal slots)</strong>을 사용한다.

내부 슬롯은 프로퍼티와 유사하지만 내부 사양 전용으로 예약되어 있다. 예를 들어 맵은 내부 슬롯 `[[MapData]]`에 데이터를 저장한다. 내장 메서드는 데이터에 접근할 때, 내부 메서드 `[[Get]]/[[Set]]`를 통하지 않고 직접 접근한다. 따라서 프락시는 이것을 가로챌 수 없다.

그래서 내장 객체가 프락시된 후에는 내부 슬롯이 없어지므로 내장 메서드가 동작하지 않는 문제가 발생한다.

예시:
```js
let map = new Map();

let proxy = new Proxy(map, {});

proxy.set('test', 1); // Error
```
내부적으로 맵은 모든 데이터를 `[[MapData]]` 내부 슬롯에 저장한다. 프락시에는 이런 슬롯이 없다. 내장 메서드 `Map.prototype.set`은 내부 프로퍼티 `this.[[MapData]]`에 접근하려 한다. 하지만 `this=proxy`이고 `proxy`에서는 `[[MapData]]`을 찾을 수 없으므로 접근에 실패한다.

다행히 이 문제를 해결할 수 있는 방법이 있다.
```js
let map = new Map();

let proxy = new Proxy(map, {
  get(target, prop, receiver) {
    let value = Reflect.get(...arguments);
    return typeof value == 'function' ? value.bind(target) : value;
  }
});

proxy.set('test', 1);
alert(proxy.get('test')); // 1 (에러 없이 작동한다)
```
이제 `get` 트랩은 `map.set` 같은 함수 프로퍼티를 `target` 객체(맵) 자체에 바인딩하기 때문에 잘 작동한다.

이전 예시와 달리 `proxy.set(...)` 안의 `this`의 값은 프락시가 아니라 원래 맵이다. 따라서 `set`의 내부 구현이 `this.[[MapData]]`의 접근에 성공하는 것이다.

---
:information_source: **배열에는 내부 슬롯이 없다.**

주목할 만한 예외로, 내장 배열은 내부 슬롯을 사용하지 않는다. 따라서 배열을 프락시할 때는 이런 문제가 없다.

---

### private 필드
private 클래스 필드에서도 비슷한 일이 일어난다.

예를 들어, 프락시 후에는 `getName()` 메서드가 private 프로퍼티인 `#name`에 접근하지 못 한다.
```js
class User {
  #name = 'Guest';

  getName() {
    return this.#name;
  }
}

let user = new User();

user = new Proxy(user, {});

alert(user.getName()); // Error
```
그 이유는 private 필드가 내부 슬롯을 이용해서 구현되기 때문이다. 자바스크립트는 private 필드에 접근할 때 `[[Get]]/[[Set]]`을 사용하지 않는다.

`getName()`을 호출할 때 `this`의 값은 프락시된 `user`로, 여기에는 private 필드의 내부 슬롯이 없다.

여기서도 동일하게, 메서드를 바인딩하는 해결법을 사용하면 된다.
```js run
class User {
  #name = 'Guest';

  getName() {
    return this.#name;
  }
}

let user = new User();

user = new Proxy(user, {
  get(target, prop, receiver) {
    let value = Reflect.get(...arguments);
    return typeof value == 'function' ? value.bind(target) : value;
  }
});

alert(user.getName()); // Guest
```
앞서 설명했듯이 이 방법은 단점이 있다. 원래 객체를 메서드에 노출시켜, 잠재적으로는 객체가 더 전달되도록 허용하고 다른 프락시 기능을 손상시킬 수 있다.

### Proxy != target
프락시와 원래 객체는 당연히 서로 다른 객체이다.

따라서 원래 객체를 키로 사용한 다음 프락시를 사용하면 프락시를 찾을 수 없게 된다.
```js run
let allUsers = new Set();

class User {
  constructor(name) {
    this.name = name;
    allUsers.add(this);
  }
}

let user = new User('John');

alert(allUsers.has(user)); // true

user = new Proxy(user, {});

alert(allUsers.has(user)); // false
```
프락시는 다른 객체이므로 프락시 후에는 `allUsers` 셋에서 `user`를 찾을 수 없게 된다.

---
:warning: **프락시는 일치 연산자 `===`를 가로챌 수 없다.**

프락시는 `new`(`construct`), in(`has`), `delete`(`deleteProperty`)를 비롯한 많은 연산자를 가로챌 수 있다.

하지만 일치 연산자를 가로챌 수 있는 방법은 없다. 객체는 자기 자신과만 유일하게 일치한다.

따라서 일치를 비교하는 모든 연산과 내장 클래스는 객체와 프락시를 구분한다. 여기에는 투명한 대체품이 없다.

---

## 취소 가능한 프락시
취소 가능한 프락시(revocable proxy)는 비활성화 할 수 있는 프락시다.

어떤 리소스가 있으며 이것에 대한 접근 권한을 아무 때나 종료하고 싶다고 가정해 보겠다.

트랩 없이 취소 가능한 프락시로 해당 리소스를 감싸면 된다. 이 프락시는 연산을 객체로 전달하고, 언제든지 비활성화 할 수 있다.

문법:
```js
let { proxy, revoke } = Proxy.revocable(target, handler)
```
이 호출은 프락시와 비활성화 할 수 있는 취소 함수를 반환한다.

예시:
```js
let object = {
  data: 'Valuable data'
};

let { proxy, revoke } = Proxy.revocable(object, {});

// 객체 대신 프락시를 전달한다.
alert(proxy.data); // Valuable data

revoke();

// 프락시는 더 이상 작동하지 않는다(취소되었다).
alert(proxy.data); // Error
```
`revoke()` 호출은 `target` 객체에 대한 모든 내부 참조를 제거한다. 따라서 `target` 객체는 가비지 컬렉션에 의해 삭제될 수 있다.

`revoke`를 위크맵에 저장하면, 나중에 프락시 객체로 쉽게 찾을 수 있다.
```js
let revokes = new WeakMap();

let object = {
  data: 'Valuable data'
};

let { proxy, revoke } = Proxy.revocable(object, {});

revokes.set(proxy, revoke);

revoke = revokes.get(proxy);
revoke();

alert(proxy.data); // Error (취소되었다)
```
이런 방식의 장점은 `revoke`를 계속 가지고 다닐 필요가 없다는 점이다. 필요할 때 맵에서 프락시로 얻을 수 있다.

여기서는 가비지 컬렉션을 막지 않기 위해 맵 대신 위크맵을 사용했다. 프락시 객체가 도달 가능하지 않게 되면(변수가 프락시 객체를 참조하지 않게 되면), 위크맵은 더 이상 필요하지 않은 `revoke`와 프락시 객체를 메모리에서 삭제될 수 있게 만든다.

## 참고
- 명세서:  [Proxy](https://tc39.es/ecma262/#sec-proxy-object-internal-methods-and-internal-slots)
- MDN:  [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

## 요약
프락시는 객체를 감싸는 랩퍼로, 객체에 전달되는 작업의 일부를 선택적으로 가로챈다.

프락시는 클래스와 함수를 포함한 모든 종류의 객체를 감쌀 수 있다.

문법:
```js
let proxy = new Proxy(target, {
  /* 트랩들 */
});
```
프락시로 감싸고 나면, 어디서든 `target` 대신 프락시를 사용해야 한다. 프락시는 자체 프로퍼티나 메서드를 가지고 있지 않다. 트랩은 특정 작업만 가로채고, 나머지 작업은 `target` 객체에 그대로 전달된다.

트랩의 종류는 다음과 같다.
- 읽기(`get`), 쓰기(`set`), 프로퍼티(존재하지 않는 것도 포함) 삭제하기(`deleteProperty`)
- 함수 호출하기(`apply`)
- `new` 연산자(`construct`)
- 이외의 여러 작업들(챕터 초반에서 확인 가능)

트랩을 이용해 가상의 프로퍼티와 메서드를 만들거나, 기본값 구현, 객체 보이기, 함수 데코레이터를 비롯한 다양한 작업을 할 수 있다.

객체에 다양한 기능을 추가하기 위해 서로 다른 프락시로 여러 번 감싸는 것도 가능하다.

`Reflect` API는 프락시를 보완하도록 설계되었다. 모든 프락시 트랩에는 대응되는`Reflect` 호출이 존재한다. 인수도 동일하다. 호출을 `target` 객체에 넘겨주기 위해 `Reflect` 호출을 사용해야 한다.

프락시에는 다음과 같은 한계가 존재한다.
- 내장 객체에는 '내부 슬롯'이 있으며, 내부 슬롯에 대한 접근은 프락시가 불가능하다.
- private 클래스 필드도 내부적으로 슬롯을 사용하여 구현되므로 마찬가지다. 따라서 프락시된 메서드 호출은 private 클래스 필드에 접근하려면 `this`가 `target` 객체여야 한다.
- 객체 일치 연산자 `===`는 가로챌 수 없다.
- 엔진에 따라 다르지만 일반적으로 가장 단순한 프락시를 사용해도 프로퍼티에 접근하는 것은 몇 배 더 오래 걸린다. 그러나 실제로는 일부 병목 현상 객체에서만 이 느린 속도가 문제가 된다.
