# new Function 문법
함수를 만들 수 있는 방법이 함수 표현식과 함수 선언문 이외에 하나 더 존재한다. 잘 사용하는 방법은 아니지만, 이 방법 외에는 대안이 없을 때 사용한다.

## 문법
```js
let func = new Function([arg1, arg2, ...argN], functionBody);
```
함수는 인수 `arg1...argN`과 함수 본문 `functionBody`로 구성된다.

이 문법을 사용해 다음과 같이 함수를 만들 수 있다.
```js
// 인수가 두 개인 함수이다.
let sum = new Function('a', 'b', 'return a + b');

alert(sum(1, 2)); // 3
```
```js
// 인수가 없는 함수이다.
let sayHi = new Function('alert("Hello")');

sayHi(); // Hello
```
```js
// 인수를 쉼표로 구분해 전달할 수 있다.
let sum = new Function('a,b', 'return a + b');

alert(sum(1, 2)); // 3
```
기존에 사용하던 방법과 `new Function`을 사용해 함수를 만드는 방법의 가장 큰 차이는 런타임에 받은 문자열을 사용해 함수를 만들 수 있다는 것이다.
```js
let str = ...서버에서 동적으로 전달받은 문자열(코드 형태)...

let func = new Function(str);
func();
```
서버에서 코드를 받거나 템플릿을 사용해 함수를 동적으로 컴파일해야 하는 경우, 복잡한 웹 애플리케이션을 구현할 때와 같이 아주 특별한 경우에 `new Function`을 사용할 수 있다.

## 클로저
함수는 특별한 프로퍼티 `[[Environment]]`에 저장된 정보를 이용해 자기 자신이 태어난 곳을 기억한다.

그런데 `new Function`을 이용해 함수를 만들면 함수의 `[[Environment]]` 프로퍼티가 현재 렉시컬 환경이 아닌 전역 렉시컬 환경을 참조하게 된다. 따라서 `new Function`을 이용해 만든 함수는 외부 변수에 접근할 수 없고, 오직 전역 변수에만 접근할 수 있다.
```js
function getFunc() {
  let value = 'test';

  let func = new Function('alert(value)');

  return func;
}

getFunc()(); // ReferenceError: value is not defined
```
`new Function`으로 만든 함수에 무언가를 넘겨주고 싶다면 인수를 사용한다.
