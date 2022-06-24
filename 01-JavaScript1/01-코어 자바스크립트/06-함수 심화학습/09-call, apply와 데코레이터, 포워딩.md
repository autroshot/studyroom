# call/apply와 데코레이터, 포워딩
## 코드 변경 없이 캐싱 기능 추가하기
연산이 오래 걸리지만 결과는 안정적인 함수  `slow(x)`가 있다고 가정해 본다. 결과가 안정적이라는 것은 인풋이 같으면 아웃풋도 같다는 것을 의미한다.

`slow(x)`가 자주 호출된다면, 결과를 어딘가에 저장(캐싱)해 재연산에 걸리는 시간을 줄이고 싶을 것이다.

`slow` 함수 내부에 캐싱 관련 코드를 추가하는 대신, 다음과 같이 래퍼 함수를 만들어 캐싱 기능을 추가할 수 있다.
```js
function slow(x) {
  // CPU 집약적인 작업이 여기에 올 수 있다.
  alert(`slow(${x})을/를 호출함`);
  return x;
}

function cachingDecorator(func) {
  let cache = new Map();

  return function(x) {
    if (cache.has(x)) {    // cache에 해당 키가 있으면
      return cache.get(x); // 대응하는 값을 cache에서 읽어온다.
    }

    let result = func(x);  // 그렇지 않은 경우에는 func를 호출하고,

    cache.set(x, result);  // 그 결과를 캐싱(저장)한다.
    return result;
  };
}

slow = cachingDecorator(slow);

alert(slow(1)); // slow(1)이 저장되었다.
alert('다시 호출: ' + slow(1)); // 동일한 결과

alert(slow(2)); // slow(2)가 저장되었다.
alert('다시 호출: ' + slow(2)); // 윗줄과 동일한 결과
```
`cachingDecorator` 같이 인수로 받은 함수의 행동을 변경시켜주는 함수를 <strong>데코레이터(decorator)</strong>라고 부른다.

래퍼 함수 `cachingDecorator`를 사용할 때의 이점은 다음과 같다.
-   `cachingDecorator`를 재사용 할 수 있다. 원하는 함수 어디에든  `cachingDecorator`를 적용할 수 있다.
-   캐싱 로직이 분리되어  `slow`  자체의 복잡성이 증가하지 않는다.
-   필요하다면 여러 개의 데코레이터를 조합해서 사용할 수도 있다(추가 데코레이터는  `cachingDecorator`  뒤를 따른다).

## 'func.call’를 사용해 컨텍스트 지정하기
위에서 구현한 캐싱 데코레이터는 객체 메서드에 사용하기에는 적합하지 않다.
```js run
// worker.slow에 캐싱 기능을 추가해본다.
let worker = {
  someMethod() {
    return 1;
  },

  slow(x) {
    // CPU 집약적인 작업이라 가정
    alert(`slow(${x})을/를 호출함`);
    return x * this.someMethod(); // this가 undefined이기 때문에 접근에 실패한다.
  }
};

// 이전과 동일한 코드이다.
function cachingDecorator(func) {
  let cache = new Map();
  return function(x) {
    if (cache.has(x)) {
      return cache.get(x);
    }

    let result = func(x);

    cache.set(x, result);
    return result;
  };
}

alert(worker.slow(1)); // 기존 메서드는 잘 동작한다.

worker.slow = cachingDecorator(worker.slow); // 캐싱 데코레이터 적용

alert(worker.slow(2)); // 에러가 발생한다, Error: Cannot read property 'someMethod' of undefined
```
문제를 해결하기 위해 [func.call(context, …args)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/call)을 이용한다.
```js
func.call(context, arg1, arg2, ...)
```
첫 번째 인수가 `this`, 이어지는 인수가 `func`의 인수가 되고 `func` 함수가 호출된다.

다음은 다른 컨텍스트(다른 객체) 하에 함수를 호출하는 예시이다.
```js
function sayHi() {
  alert(this.name);
}

let user = { name: 'John' };
let admin = { name: 'Admin' };

// call을 사용해 원하는 객체가 'this'가 되도록 한다.
sayHi.call(user); // this = John
sayHi.call(admin); // this = Admin
```
`func.call`를 이용하면 객체 메서드에 데코레이터를 적용해도 오류가 발생하지 않는다.
```js
let worker = {
  someMethod() {
    return 1;
  },

  slow(x) {
    alert(`slow(${x})을/를 호출함`);
    return x * this.someMethod();
  }
};

function cachingDecorator(func) {
  let cache = new Map();
  return function(x) {
    if (cache.has(x)) {
      return cache.get(x);
    }

    let result = func.call(this, x); // 이제 'this'가 제대로 전달된다.

    cache.set(x, result);
    return result;
  };
}

worker.slow = cachingDecorator(worker.slow); // 캐싱 데코레이터 적용

alert(worker.slow(2)); // 제대로 동작한다.
alert(worker.slow(2)); // 제대로 동작한다. 다만, 원본 함수가 호출되지 않고 캐시 된 값이 출력된다.
```
`this`가 전달되는 자세한 과정은 다음과 같다.
1.  데코레이터를 적용한 후에  `worker.slow`는 래퍼  `function (x) { ... }`가 된다.
2.  `worker.slow(2)`를 실행하면 래퍼는  `2`를 인수로 받고,  `this=worker`가 된다(점 앞의 객체).
3.  결과가 캐시되지 않은 상황이라면  `func.call(this, x)`에서 현재  `this`(`=worker`)와 인수(`=2`)를 원본 메서드에 전달한다.

## 여러 인수 전달하기
지금의 데코레이터는 인수가 하나인 함수에만 적용할 수 있다. 인수가 여러 개인 함수에는 적용할 수 없다는 문제가 있다.

이 문제를 해결하는 방법은 여러 가지가 있다.
1.  복수 키를 지원하는 맵과 유사한 자료 구조를 구현한다(서드 파티 라이브러리 등을 사용해도 됨).
2.  중첩 맵을 사용한다.  `(max, result)` 쌍 저장은 `cache.set(min)`으로, `result`는  `cache.get(min).get(max)`을 사용해 얻는다.
3.  두 값을 하나로 합친다. 맵의 키로 문자열  `'min,max'`를 사용한다. 여러 값을 하나로 합치는 코드는 <strong>해싱 함수(hashing function)</strong>에 구현해 유연성을 높인다.

우리는 세 번째 방법을 사용할 것이다.

여기에 더하여 `func.call(this, x)`를 `func.call(this, ...arguments)`로 교체해, 래퍼 함수로 감싼 함수가 호출될 때 복수 인수를 넘길 수 있게 한다.

더 강력해진 `cachingDecorator`는 다음과 같다.
```js
let worker = {
  slow(min, max) {
    alert(`slow(${min},${max})을/를 호출함`);
    return min + max;
  }
};

function cachingDecorator(func, hash) {
  let cache = new Map();
  return function() {
    let key = hash(arguments);

    if (cache.has(key)) {
      return cache.get(key);
    }

    let result = func.call(this, ...arguments);

    cache.set(key, result);
    return result;
  };
}

function hash(args) {
  return args[0] + ',' + args[1];
}

worker.slow = cachingDecorator(worker.slow, hash);

alert(worker.slow(3, 5)); // 제대로 동작한다.
alert('다시 호출: ' + worker.slow(3, 5)); // 동일한 결과 출력된다(캐시된 결과).
```

## func.apply
`func.call` 대신 `func.apply`를 사용할 수 있다.
```js
func.apply(context, args)
```
`apply`는 `func`의 `this`를 `context`로 고정해주고, 유사 배열 객체인 `args`를 인수로 사용할 수 있게 해준다.

`call`은 복수의 인수를 따로 받고 `apply`는 인수를 유사 배열 객체로 받는다는 문법적 차이가 존재한다.

다음 코드 두 줄은 거의 같은 역할을 한다.
```js
func.call(context, ...args); // 전개 문법을 사용해 인수가 담긴 배열을 전달하는 것과
func.apply(context, args);   // call을 사용하는 것은 동일하다.
```
받아들이는 인수에 약간의 차이가 있다.
-   전개 문법  `...`은 **이터러블** `args`을 분해해서 `call`에 전달할 수 있도록 해준다.
-   `apply`는 오직 **유사 배열** 형태의 `args`만 받는다.

인수가 이터러블 형태라면 `call`을, 유사 배열 형태라면 `apply`를 사용하면 된다.

배열 같이 이터러블이면서 유사 배열인 객체에는 둘 다 사용할 수 있는데, 대부분의 자바스크립트 엔진은 내부에서 `apply`를 최적화 하기 때문에 `apply`를 사용하는 것이 좀 더 빠르다.

이렇게 컨텍스트와 함께 인수 전체를 다른 함수에 전달하는 것을 <strong>콜 포워딩(call forwarding)</strong>이라고 한다.

가장 간단한 형태의 콜 포워딩은 다음과 같다.
```js
let wrapper = function() {
  return func.apply(this, arguments);
};
```

## 메서드 빌리기
이제 위에서 구현한 해싱 함수를 개선해보겠다.
```js
function hash(args) {
  return args[0] + ',' + args[1];
}
```
`args`에 배열 메서드 `join`을 사용하면 에러가 발생한다. `args`가 유사 배열 객체이기 때문이다.

다음과 같은 방법을 사용하면 `join`을 사용할 수 있다.
```js run
function hash() {
  alert([].join.call(arguments)); // 1,2
}

hash(1, 2);
```
이 방법은 **메서드 빌리기**라고 부른다.

일반 배열에서 `join` 메서드를 빌려오고(`[].join`), `[].join.call`를 사용해 `arguments`를 컨텍스트로 고정한 후 `join`메서드를 호출하는 것이다.

## 데코레이터와 함수 프로퍼티
함수 또는 메서드를 데코레이터로 감싸 대체하는 것은 대체적으로 안전하다. 그런데 원본 함수에 `func.calledCount` 등의 커스텀 프로퍼티가 있으면 데코레이터를 적용한 함수에서는 프로퍼티를 사용할 수 없으므로 안전하지 않다. 함수에 프로퍼티가 있는 경우에는 데코레이터 사용에 주의해야 한다.

몇몇 데코레이터는 자신만의 프로퍼티를 갖기도 한다. 데코레이터는 함수가 얼마나 많이 호출되었는지 세거나 호출 시 얼마나 많은 시간이 소모되었는지 등의 정보를 래퍼의 프로퍼티에 저장할 수 있다.

함수 프로퍼티에 접근할 수 있게 해주는 데코레이터를 만드는 방법도 있다. 그런데 이걸 구현하려면 `Proxy`라는 특별한 객체를 사용해 함수를 감싸야 한다. `Proxy`에 대해서는 [Proxy와 Reflect](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/14-%EA%B8%B0%ED%83%80/01-Proxy%EC%99%80%20Reflect.md#proxy%EC%99%80-reflect) 챕터에서 다룰 것이다.
