# 오래된 var
---
:information_source: **오래된 스크립트를 읽는 데 도움을 주는 챕터이다.**

이번 주제에서는 작성된 지 오래된 스크립트를 읽는 데 도움을 줄 만한 내용을 다룬다.

새로운 코드를 작성할 때는 이 방법을 쓰면 안 된다.

---
`var`로 선언한 변수는 `let`으로 선언한 변수와 유사하다. 대부분의 경우에는 `let`을 `var`로, `var`를 `let`으로 바꿔도 큰 문제 없이 동작하지만 몇 가지 차이가 존재한다.

## var는 블록 스코프가 없다
`var`로 선언한 변수의 스코프는 함수 스코프이거나 전역 스코프이다. 블록 기준으로 스코프가 생기지 않기 때문에 블록 밖에서 접근 가능하다.
```js
if (true) {
  var test = true; // 'let' 대신 'var'를 사용했다.
}

alert(test); // true(if 문이 끝났어도 변수에 여전히 접근할 수 있다)
```
코드 블록이 함수 안에 있다면, `var`는 함수 레벨 변수가 된다.
```js
function sayHi() {
  if (true) {
    var phrase = 'Hello';
  }

  alert(phrase); // 제대로 출력된다.
}

sayHi();
alert(phrase); // Error: phrase is not defined
```
`var`는 `if`, `for` 등의 코드 블록을 관통한다. 아주 오래전의 자바스크립트에서는 블록 수준 렉시컬 환경이 만들어지지 않았기 때문이다. `var`는 구식 자바스크립트의 잔재이다.

## var는 재선언을 허용한다
`let`을 이용해 동일한 스코프에서 재선언을 하면 에러가 발생한다.
```js
let user;
let user; // SyntaxError: 'user' has already been declared
```
`var`는 몇 번이고 재선언이 가능하다.
```js
var user = 'Pete';

var user = 'John'; // 이 'var'는 아무 기능도 하지 않는다.
// 에러가 발생하지 않는다.

alert(user); // John
```

## var는 선언하기 전에 사용이 가능하다
`var` 선언은 함수가 시작될 때 처리된다. 전역에서 선언한 변수라면 스크립트가 시작될 때 처리된다.

아래 두 예제는 동일하게 동작한다.
```js
function sayHi() {
  phrase = 'Hello';

  alert(phrase);

  var phrase;
}
sayHi();
```
```js
function sayHi() {
  var phrase;

  phrase = 'Hello';

  alert(phrase);
}
sayHi();
```
코드 블록은 무시되기 때문에 아래 코드 역시 동일하게 동작한다.
```js
function sayHi() {
  phrase = 'Hello';

  if (false) { // 이 코드 블록 안은 절대 실행되지 않지만, 이는 호이스팅에 영향을 주지 않는다.
    var phrase;
  }

  alert(phrase);
}
sayHi();
```
이렇게 변수가 끌어올려 지는 현상을 <strong>호이스팅(hoisting)</strong>이라고 부른다. `var`로 선언한 모든 변수는 함수의 최상위로 ‘끌어 올려지기(hoisted)’ 때문이다.

**선언은 호이스팅 되지만 할당은 호이스팅 되지 않는다.**

따라서 아래 두 코드는 동일하게 동작한다.
```js
function sayHi() {
  alert(phrase);  

  var phrase = 'Hello';
}

sayHi(); // undefined
```
```js
function sayHi() {
  var phrase; // 선언은 함수 시작 시 처리된다.

  alert(phrase);

  phrase = 'Hello'; // 할당은 실행 흐름이 해당 코드에 도달했을 때 처리된다.
}

sayHi(); // undefined
```

### 즉시 실행 함수 표현식
`let`, `const`가 없던 과거에는 `var`가 블록 레벨 스코프를 가질 수 있게 여러가지 방안을 고려했다. 이때 만들어진 것이 '즉시 실행 함수 표현식(immediately-invoked function expressions)'이다. 즉시 실행 함수 표현식은 `IIFE`라고 부르기도 한다.
```js
(function() {

  let message = 'Hello';

  alert(message); // Hello

})();
```
함수 표현식이 만들어지고 바로 호출되면서, 해당 함수가 바로 실행되었다. 이 함수는 자신만의 변수를 갖는다.

괄호를 사용하는 방법 말고도 자바스크립트가 함수 표현식이라고 인식하게 해주는 다른 방법들이 있다.
```js
// IIFE를 만드는 방법

(function() {
  alert('함수를 괄호로 둘러싸기');
})();

(function() {
  alert('전체를 괄호로 둘러싸기');
}());

!function() {
  alert('표현식 앞에 비트 NOT 연산자 붙이기');
}();

+function() {
  alert('표현식 앞에 단항 덧셈 연산자 붙이기');
}();
```
