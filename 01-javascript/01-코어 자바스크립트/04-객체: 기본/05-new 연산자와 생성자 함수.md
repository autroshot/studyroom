# new 연산자와 생성자 함수
`'new'` 연산자와 생성자 함수를 사용하면 **유사한 객체 여러 개**를 쉽게 만들 수 있다.
## 생성자 함수
생성자 함수(constructor function)와 일반 함수에 기술적인 차이는 없다. 다만 생성자 함수는 아래 두 관례를 따른다.
1.  함수 이름의 첫 글자는 대문자로 시작한다.
2.  반드시  `'new'`  연산자를 붙여 실행한다.
```js
function User(name) {
  this.name = name;
  this.isAdmin = false;
}

let user = new User('보라');

alert(user.name); // 보라
alert(user.isAdmin); // false
```
다음과 같은 알고리즘이 실행된다.
```js
function User(name) {
  // this = {};  (빈 객체가 암시적으로 만들어진다.)

  // 새로운 프로퍼티를 this에 추가함
  this.name = name;
  this.isAdmin = false;

  // return this;  (this가 암시적으로 반환된다.)
```
이제 `let user = new User("보라")`는 아래 코드와 동일하게 동작한다.
```js
let user = {
  name: '보라',
  isAdmin: false
};
```
모든 함수는 생성자 함수가 될 수 있다는 점을 기억해야 한다. `new`를 붙이면 어떤 함수라도 위에 언급된 알고리즘이 실행된다.

---
:information_source: **new function() { … }**

재사용할 필요가 없는 복잡한 객체를 만들 때 익명 생성자 함수를 사용하면 된다.
```js
let user = new function() {
  this.name = 'John';
  this.isAdmin = false;

  // 사용자 객체를 만들기 위한 여러 코드, 지역 변수, 복잡한 로직, 구문 등의
  // 다양한 코드가 이곳에 들어간다.
};
```
---

## new.target과 생성자 함수
:information_source: **심화 학습**

`new.target` 프로퍼티를 사용하면 함수가 `new`와 함께 호출되었는지 여부를 알 수 있다.
```js
function User() {
  alert(new.target);
}

// 'new' 없이 호출한 경우에는 undefined를 반환한다.
User(); // undefined

// 'new'를 붙여 호출한 경우에는 함수 자체를 반환한다.
new User(); // function User { ... }
```
이를 활용해 일반적인 방법으로 함수를 호출해도 `new`를 붙여 호출한 것과 같이 동작하게 만들 수 있다.
```js
function User(name) {
  if (!new.target) { // new 없이 호출해도
    return new User(name); // new를 붙여준다.
  }

  this.name = name;
}

let bora = User('보라'); // 'new User'를 쓴 것처럼 바꿔준다.
alert(bora.name); // 보라
```
이 방식은 주로 라이브러리에서 사용하고 우리가 실제 코드를 작성할 때는 사용할 일이 거의 없다.

## 생성자와 return문
생성자 함수에는 보통 `return` 문이 없지만 `return`문이 있다면 다음과 같은 규칙이 적용된다.
1. 객체를  `return`  한다면  `this`  대신 객체가 반환된다.
```js
function BigUser() {
  this.name = '원숭이';
  
  return { name: '고릴라' };  // <-- this가 아닌 새로운 객체를 반환함
}

alert(new BigUser().name);  // 고릴라
```
2. 원시형을  `return`  한다면  `return`문이 무시된다.
```js
function SmallUser() {
  this.name = '원숭이';

  return; // <-- this를 반환함
}

alert(new SmallUser().name);  // 원숭이
```
`return`문이 있는 생성자 함수는 거의 없다. 위 예제들은 특이 케이스이다.

---
:information_source: **괄호 생략하기**

인수가 없는 생성자 함수는 괄호를 생략해 호출할 수 있지만 '좋은 스타일'은 아니다.
```js
let user = new User();
// 아래 코드는 위 코드와 똑같이 동작한다.
let user = new User;
```
---

## 생성자 내 메서드
생성자 함수를 사용하면 매개변수를 이용해 객체 내부를 자유롭게 구성할 수 있다.

다음과 같이 메서드를 더해주는 것도 가능하다.
```js
function User(name) {
  this.name = name;

  this.sayHi = function() {
    alert('제 이름은 ' + this.name + '입니다.');
  };
}

let bora = new User('이보라');

bora.sayHi(); // 제 이름은 이보라입니다.

/*
bora = {
   name: '이보라',
   sayHi: function() { ... }
}
*/
```
[class]() 문법을 사용하면 생성자 함수를 사용하는 것과 마찬가지로 복잡한 객체를 만들 수 있다.
