# 메서드와 this
자바스크립트에서는 객체의 프로퍼티에 함수를 할당해 객체에게 행동할 수 있는 능력을 부여해준다.
## 메서드 만들기
객체 프로퍼티에 할당된 함수를 <strong>메서드(method)</strong>라고 부른다.
```js
let user = {
  name: 'John',
  age: 30
};

user.sayHi = function() {
  alert('안녕하세요!');
};

user.sayHi(); // '안녕하세요!'가 출력된다. user의 메서드를 호출했다.
```
이미 정의된 함수로 메서드를 만드는 것도 가능하다.
```js
let user = {
  // ...
};

// 함수 선언
function sayHi() {
  alert('안녕하세요!');
};

// 선언된 함수를 메서드로 등록한다.
user.sayHi = sayHi;

user.sayHi(); // 안녕하세요!
```

---
:information_source: **객체 지향 프로그래밍**

객체를 사용하여 개체를 표현하는 방식을 객체 지향 프로그래밍(object-oriented programming, OOP)이라 부른다.

---

### 메서드 단축 구문
객체 리터럴 안에서 메서드를 선언할 때 사용할 수 있는 단축 문법이다. `: function`을 생략할 수 있다.
```js
user = {
  sayHi: function() {
    alert('Hello');
  }
};

user = {
  sayHi() { // "sayHi: function()"과 동일하다.
    alert('Hello');
  }
};
```
일반적인 방법과 단축 구문을 사용한 방법이 완전히 동일하진 않다. 객체 상속과 관련된 미묘한 차이가 존재하는데 지금으로서는 이 차이가 중요하지 않다.

## 메서드와 this
**메서드 내부에서  `this`  키워드를 사용하면 객체에 접근할 수 있다.** 정확히는 메서드를 호출할 때 사용된 객체에 접근한다.
```js
let user = {
  name: 'John',
  age: 30,

  sayHi() {
    // 'this'는 '현재 객체'를 나타낸다.
    alert(this.name);
  }
};

user.sayHi(); // John
```
`this`를 사용하지 않고 외부 변수 `user`를 참조해 객체에 접근하는 것도 가능하지만 문제가 발생할 수 있다.

## 자유로운 this
자바스크립트에서는 모든 함수에서 `this`를 사용할 수 있다.

`this` 값은 런타임에 결정된다. 동일한 함수라도 다른 객체에서 호출했다면 'this’가 참조하는 값이 달라진다.
```js
let user = { name: 'John' };
let admin = { name: 'Admin' };

function sayHi() {
  alert(this.name);
}

// 별개의 객체에서 동일한 함수를 사용한다.
user.f = sayHi;
admin.f = sayHi;

// 'this'는 '점(.) 앞의' 객체를 참조하기 때문에 this 값이 다르다.
user.f(); // John(this == user)
admin.f(); // Admin(this == admin)

admin['f'](); // Admin(점과 대괄호는 동일하게 동작한다.)
```

---
:information_source: **객체 없이 호출하기:** `this == undefined`
```js
function sayHi() {
  alert(this);
}

sayHi(); // undefined
```
위와 같은 코드를 엄격 모드에서 실행하면, `this`에는 `undefined`가 할당된다. 엄격 모드가 아닐 때는 `this`가 전역 객체를 참조하는데, 브라우저 환경에서는 `window`라는 전역 객체를 참조한다. 이런 동작 차이는 `'use strict'`가 도입된 배경이기도 하다.

---

## this가 없는 화살표 함수
화살표 함수는 일반 함수와는 달리 ‘고유한’ `this`를 가지지 않는다. 화살표 함수에서 `this`를 참조하면, 화살표 함수가 아닌 ‘평범한’ 외부 함수에서 `this` 값을 가져온다.
```js
let user = {
  firstName: '보라',
  sayHi() {
    // 함수 `arrow()`의 `this`는 외부 함수 `user.sayHi()`의 `this`이다.
    let arrow = () => alert(this.firstName);
    arrow();
  }
};

user.sayHi(); // 보라
```
별개의 `this`가 만들어지는 것을 원하지 않고 외부 컨텍스트에 있는 `this`를 이용하고 싶은 경우에는 화살표 함수가 유용하다. 이에 대한 자세한 내용은 별도의 챕터, [화살표 함수 다시 살펴보기](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5.md#11-%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98-%EB%8B%A4%EC%8B%9C-%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0)에서 확인할 수 있다.
