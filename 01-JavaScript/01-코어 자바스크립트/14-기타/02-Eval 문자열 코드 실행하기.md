# Eval: 문자열 코드 실행하기
내장 함수 `eval`을 사용하면 문자열 형태의 코드를 실행할 수 있다.

문법:
```js
let result = eval(code);
```
길이가 긴 문자열도 코드가 될 수 있는데, 여기에는 줄 바꿈, 함수 선언, 변수 등이 포함될 수 있다.

마지막 구문의 결과가  `eval`의 결과가 된다.

예시:
```js run
let value = eval('1+1');
alert(value); // 2
```

```js run
let value = eval('let i = 0; ++i');
alert(value); // 1
```
`eval`로 둘러싼 코드는 현재 렉시컬 환경에서 실행되므로 외부 변수에 접근할 수 있다.
```js
let a = 1;

function f() {
  let a = 2;

  eval('alert(a)'); // 2
}

f();
```
```js
let x = 5;
eval('x = 10');
alert(x); // 10, 변경된 값이다.
```
엄격 모드에서 `eval`은 자체 렉시컬 환경을 갖는다. 따라서 eval 내부에 선언된 함수와 변수는 외부에서 읽을 수 없다.
```js
'use strict';

eval('let x = 5; function f() {}');

alert(typeof x); // undefined (없는 변수이다)
// 함수 f도 읽을 수 없다.
```

## ‘eval’ 사용하기
`eval`은 모던 프로그래밍에서 잘 사용되지 않는다.

eval을 사용할 때는 외부 변수에 접근 시 부작용이 발생한다는 점을 유의해야 한다. 부작용을 막기 위해 다음의 두 가지 방법을 이용할 수 있다.
1. eval로 감싼 코드에서 외부 변수를 사용하지 않는다면  `eval`  대신  `window.eval(...)`을 호출한다.

    이렇게 하면 eval 내의 코드가 전역 스코프에서 실행된다.
    ```js
	let x = 1;
	{
	  let x = 5;
	  window.eval('alert(x)'); // 1 (전역 변수이다)
	}
	```

2. eval로 감싼 코드에서 지역 변수를 사용한다면,  `eval`이 아닌  `new Function`에 문자열로 된 코드를 전달한다.
    ```js run
	let f = new Function('a', 'alert(a)');

	f(5); // 5
	```
	`new Function` 문법(자세한 내용은 [new Function 문법](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5/07-new%20Function%20%EB%AC%B8%EB%B2%95.md#new-function-%EB%AC%B8%EB%B2%95) 챕터에서 확인)은 인수로 받은 문자열을 기반으로 전역 스코프에 새로운 함수를 만들어준다. 따라서 지역 변수에 접근할 수 없다.

## 요약
`eval(code)`을 호출하면 문자열 형태의  `code`가 실행되며 마지막 구문의 결과가 반환된다.
-   모던 자바스크립트에는 `eval`을 대체할 수 있는 문법이 많기 때문에, 모던 자바스크립트를 사용하는 코드에서는 `eval`을 거의 사용하지 않는다.
-   `eval`을 이용해 만든 코드는 외부 지역 변수에 접근할 수 있는데, 이는 좋지 않은 방법이다.
-   전역 스코프에서  `eval`을 사용하지 말고,  `window.eval(code)`을 이용한다.
-   외부 스코프에 있는 데이터가 필요하다면  `new Function`의 인수에 코드를 전달해 사용한다.
