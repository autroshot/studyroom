# 함수의 prototype 프로퍼티
이번 챕터에서는 **생성자 함수**를 사용해 객체를 만든 경우에 프로토타입이 어떻게 동작하는지에 대해 알아볼 것이다.

생성자 함수로 만든 객체는 생성자 함수의 프로토타입 정보를 사용해 [[Prototype]]을 설정한다는 차이가 있다.

---
:information_source: **주의**

과거에는 프로토타입에 직접 접근할 수 있는 방법이 생성자 함수의 `prototype` 프로퍼티를 이용하는 방법뿐이었다. 많은 스크립트가 아직 이 방법을 사용하고 있다.

---
생성자 함수(`F`)의 프로토타입을 의미하는 `F.prototype`에서 `prototype`은 `F`에 정의된 일반 프로퍼티이다. 이것은 바로 앞 챕터에서 배운 '프로토타입'과 이름만 같은, 우리가 익히 알고 있는 일반적인 프로퍼티이다.
```js
let animal = {
  eats: true
};

function Rabbit(name) {
  this.name = name;
}

Rabbit.prototype = animal;

let rabbit = new Rabbit('흰 토끼'); //  rabbit.__proto__ == animal

alert(rabbit.eats); // true
```
`Rabbit.prototype = animal`은 `new Rabbit`을 호출해 만든 새로운 객체의 `[[Prototype]]`을 `animal`로 설정하라는 것을 의미한다.

![proto-constructor-animal-rabbit](https://user-images.githubusercontent.com/95019875/166429102-8bad2808-6d63-481c-8d3e-fe0ff2328320.svg)

여기서 가로 화살표는 일반 프로퍼티인 `prototype`을, 세로 화살표는 `[[Prototype]]`을 나타낸다. 세로 화살표는 `rabbit`이 `animal`을 상속받았다는 것을 의미한다.

---
:information_source: **`F.prototype`은 `new F`를 호출할 때만 사용된다.**

`F.prototype` 프로퍼티는 `new F`를 호출할 때만 사용되며 새롭게 만들어지는 객체의 `[[Prototype]]`을 지정해 준다.

`F.prototype` 프로퍼티가 바뀌어도 이전에 만들어졌던 객체의 `[[Prototype]]`은 그대로 유지된다.

---
:information_source: **일반 객체에 `prototype` 프로퍼티를 추가하면 아무 일도 일어나지 않는다.**

```js
let user = {
  name: 'John',
  prototype: 'Bla-bla' // 마술은 일어나지 않는다.
};
```
---

## 함수의 디폴트 프로퍼티 prototype과 constructor 프로퍼티
별도로 지정하지 않아도 모든 함수는 기본적으로 `prototype` 프로퍼티를 갖는다.

디폴트(default) 프로퍼티 `prototype`은 `constructor` 프로퍼티 하나만 있는 객체를 가리키는데, 이 `constructor` 프로퍼티는 함수 자신을 가리킨다.
```js
function Rabbit() {}

/* 디폴트 prototype
Rabbit.prototype = { constructor: Rabbit };
*/
```

![function-prototype-constructor](https://user-images.githubusercontent.com/95019875/166429118-665abb78-435c-42d9-944c-bafad438c5a2.svg)

특별한 조작을 가하지 않았다면 `new Rabbit`을 실행해 만든 토끼 객체 모두에서 `constructor` 프로퍼티를 사용할 수 있는데, 이때 `[[Prototype]]`을 거친다.
```js
function Rabbit() {}
// 디폴트 prototype:
// Rabbit.prototype = { constructor: Rabbit }

let rabbit = new Rabbit(); // { constructor: Rabbit }을 상속받았다.

alert(rabbit.constructor == Rabbit); // true ([[Prototype]]을 거쳐 접근한다)
```

![rabbit-prototype-constructor](https://user-images.githubusercontent.com/95019875/166429150-6c3ce28d-a191-4b83-ad18-1f2ac19a6fbc.svg)

`constructor` 프로퍼티는 다음과 같이 기존에 있던 객체의 `constructor`를 사용해 새로운 객체를 만들때 사용할 수 있다.
```js
function Rabbit(name) {
  this.name = name;
  alert(name);
}

let rabbit = new Rabbit('흰 토끼');

let rabbit2 = new rabbit.constructor('검정 토끼');
```
이 방법은 객체가 있는데 이 객체를 만들 때 어떤 생성자가 사용되었는지 알 수 없는 경우(객체가 서드 파티 라이브러리에서 온 경우 등)에 유용하다.

**자바스크립트는 알맞은  `constructor`  값을 보장하지 않는다**는 것을 유의해야 한다.

함수에 기본으로 `prototype`이 설정될 뿐, `constructor`와 관련해서 벌어지는 모든 일은 전적으로 개발자에게 달려있다.

다음 예시에서는 `prototype` 프로퍼티 값을 다른 객체로 바꿨더니 new를 사용해 만든 객체에 `constructor`가 없는 것을 확인할 수 있다.
```js
function Rabbit() {}
Rabbit.prototype = {
  jumps: true
};

let rabbit = new Rabbit();
alert(rabbit.constructor === Rabbit); // false
```
이런 상황을 방지하고 `constructor`의 기본 성질을 제대로 활용하려면, `prototype` 전체를 덮어쓰지 말고 디폴트 `prototype`에 원하는 프로퍼티를 추가, 제거해야 한다.
```js
function Rabbit() {}

// Rabbit.prototype 전체를 덮어쓰지 말고
// 원하는 프로퍼티가 있으면 그냥 추가한다.
Rabbit.prototype.jumps = true
// 이렇게 하면 디폴트 프로퍼티 Rabbit.prototype.constructor가 유지된다.
```
실수로 `prototype`을 덮어씌었더라도 `constructor` 프로퍼티를 다시 만들어주면 `constructor`를 문제 없이 사용할 수 있다.
```js
Rabbit.prototype = {
  jumps: true,
  constructor: Rabbit
};

// 수동으로 constructor를 추가해 주었기 때문에 우리가 알고 있던 constructor의 특징을 그대로 사용할 수 있다.
```
