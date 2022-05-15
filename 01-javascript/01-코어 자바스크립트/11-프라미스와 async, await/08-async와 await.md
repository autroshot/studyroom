# async와 await
`async`와 `await`라는 특별한 문법을 사용하면 프라미스를 좀 더 편하게 사용할 수 있다.

## async 함수
`async`는 function 앞에 위치한다.
```js
async function f() {
  return 1;
}
```
function 앞에 `async`를 붙이면 해당 함수는 항상 프라미스를 반환한다. 프라미스가 아닌 값을 반환하더라도 이행 상태의 프라미스(resolved promise)로 값을 감싸 이행된 프라미스가 반환되도록 한다.

다음 예시의 함수를 호출하면 `result`가 `1`인 이행 프라미스가 반환된다.
```js
async function f() {
  return 1;
}

f().then(alert); // 1
```
명시적으로 프라미스를 반환하는 것도 가능한데, 결과는 동일하다.
```js
async function f() {
  return Promise.resolve(1);
}

f().then(alert); // 1
```

## await
**await는 async 함수 안에서만 동작**하며 문법은 다음과 같다.
```js
let value = await promise;
```
자바스크립트는 `await` 키워드를 만나면 프라미스가 처리될 때까지 기다린다. 결과는 그 이후 반환된다.

1초 후 이행되는 프라미스를 예시로 사용하여 `await`가 어떻게 동작하는지 살펴보겠다.
```js
async function f() {
  let promise = new Promise((resolve, reject) => {
    setTimeout(() => resolve('완료!'), 1000)
  });

  let result = await promise; // 프라미스가 이행될 때까지 기다림 (*)

  alert(result); // '완료!'
}

f();
```
함수를 호출하고, 함수 본문이 실행되는 도중에 `(*)`로 표시한 줄에서 실행이 잠시 중단되었다가 프라미스가 처리되면 실행이 재개된다. 이때 프라미스 객체의 `result` 값이 변수 `result`에 할당된다. 따라서 위 예시를 실행하면 1초 뒤에 '완료!'가 출력된다.

`await`는 말 그대로 프라미스가 처리될 때까지 함수 실행을 기다리게 만든다. 프라미스가 처리되면 그 결과와 함께 실행이 재개된다. 프라미스가 처리되길 기다리는 동안에는 엔진이 다른 일(다른 스크립트를 실행, 이벤트 처리 등)을 할 수 있기 때문에, CPU 리소스가 낭비되지 않는다.

`await`는 `promise.then`보다 좀 더 세련되게 프라미스의 `result` 값을 얻을 수 있도록 해주는 문법이다. `promise.then`보다 가독성이 좋고 사용도 쉽다.

---
:warning: **일반 함수에는 `await`을 사용할 수 없다.**

`async` 함수가 아닌데 `await`을 사용하면 문법 에러가 발생한다.
```js
function f() {
  let promise = Promise.resolve(1);
  let result = await promise; // Syntax error
}
```

---
[프라미스 체이닝](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/11-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80%20async%2C%20await/03-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%20%EC%B2%B4%EC%9D%B4%EB%8B%9D.md#%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4-%EC%B2%B4%EC%9D%B4%EB%8B%9D)  챕터의  `showAvatar()`  예시를  `async/await`를 사용해 다시 작성해 보겠다.

1.  먼저  `.then`  호출을  `await`로 바꿔야 한다.
2.  function 앞에  `async`를 붙여  `await`를 사용할 수 있도록 해야 한다.
```js
async function showAvatar() {
  // JSON 읽기
  let response = await fetch('/article/promise-chaining/user.json');
  let user = await response.json();

  // github 사용자 정보 읽기
  let githubResponse = await fetch(`https://api.github.com/users/${user.name}`);
  let githubUser = await githubResponse.json();

  // 아바타 보여주기
  let img = document.createElement('img');
  img.src = githubUser.avatar_url;
  img.className = 'promise-avatar-example';
  document.body.append(img);

  // 3초 대기
  await new Promise((resolve, reject) => setTimeout(resolve, 3000));

  img.remove();

  return githubUser;
}

showAvatar();
```
---
:information_source: **`await`는 최상위 레벨 코드에서 작동하지 않는다.**

다음 코드는 동작하지 않는다.
```js
// 최상위 레벨 코드에서는 문법 에러가 발생한다
let response = await fetch('/article/promise-chaining/user.json');
let user = await response.json();
```
하지만 익명 async 함수로 코드를 감싸면 최상위 레벨 코드에도 `await`를 사용할 수 있다.
```js
(async () => {
  let response = await fetch('/article/promise-chaining/user.json');
  let user = await response.json();
  ...
})();
```
---
:information_source: **`await`는 ‘thenable’ 객체를 받는다.**

`promise.then`처럼 `await`에도 thenable 객체(`then` 메서드가 있는 호출 가능한 객체)를 사용할 수 있다.

다음 예시에서 `await`는 데모용 클래스 `Thenable`의 인스턴스를 받을 수 있다.
```js run
class Thenable {
  constructor(num) {
    this.num = num;
  }
  then(resolve, reject) {
    alert(resolve);
    // 1000밀리초 후에 이행된다(result는 this.num*2).
    setTimeout(() => resolve(this.num * 2), 1000);
  }
};

async function f() {
  // 1초 후, 변수 result는 2가 된다.
  let result = await new Thenable(1);
  alert(result);
}

f();
```

---
:information_source: **async 클래스 메서드**

메서드 이름 앞에 `async`를 추가하면 async 클래스 메서드를 선언할 수 있다.
```js
class Waiter {
  async wait() {
    return await Promise.resolve(1);
  }
}

new Waiter()
  .wait()
  .then(alert); // 1
```
`async` 메서드와 `async` 함수는 프라미스를 반환하고 `await`를 사용할 수 있다는 점에서 동일하다.

---

## 에러 핸들링
프라미스가 정상적으로 이행되면 `await promise`는 프라미스 객체의 `result`에 저장된 값을 반환한다. 반면 프라미스가 거부되면 마치 `throw`문을 작성한 것처럼 에러가 던져진다.

예시:
```js
async function f() {
  await Promise.reject(new Error('에러 발생!'));
}
```
위 코드는 다음 코드와 동일한다.
```js
async function f() {
  throw new Error('에러 발생!');
}
```
실제 상황에선 프라미스가 거부 되기 전에 약간의 시간이 지체되는 경우가 있다. 이런 경우에는 `await`가 에러를 던지기 전에 지연이 발생한다.

`await`가 던진 에러는  `throw`가 던진 에러를 잡을 때처럼  `try..catch`를 사용해 잡을 수 있다.
```js
async function f() {
  try {
    let response = await fetch('http://유효하지-않은-주소');
  } catch(err) {
    alert(err); // TypeError: failed to fetch
  }
}

f();
```
에러가 발생하면 제어 흐름이 `catch` 블록으로 넘어간다.

다음과 같이 여러 줄의 코드를 `try`로 감싸는 것도 가능하다.
```js
async function f() {
  try {
    let response = await fetch('http://유효하지-않은-주소');
    let user = await response.json();
  } catch(err) {
    // fetch와 response.json에서 발행한 에러 모두를 여기서 잡는다.
    alert(err);
  }
}

f();
```
`try..catch`가 없으면 async 함수 `f()`를 호출해 만든 프라미스가 거부 상태가 된다. `f()`에 `.catch`를 추가하면 거부된 프라미스를 처리할 수 있다.
```js
async function f() {
  let response = await fetch('http://유효하지-않은-주소');
}

// f()는 거부 상태의 프라미스가 된다.
f().catch(alert); // TypeError: failed to fetch
```
`.catch`를 추가하는 것을 잊으면 처리되지 않은 프라미스 에러가 발생합니다. 이런 에러는 [프라미스와 에러 핸들링](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/11-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80%20async%2C%20await/04-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80%20%EC%97%90%EB%9F%AC%20%ED%95%B8%EB%93%A4%EB%A7%81.md#%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80-%EC%97%90%EB%9F%AC-%ED%95%B8%EB%93%A4%EB%A7%81) 챕터에서 배웠듯이 전역 이벤트 핸들러 `unhandledrejection`을 사용해 잡을 수 있다.

---
:information_source: **`async/await`와 `promise.then/catch`**

`async/await`을 사용하면 `await`가 대기를 처리해주기 때문에 `.then`이 거의 필요하지 않다. 여기에 더하여 `.catch` 대신 일반 `try..catch`를 사용할 수 있다는 장점도 생긴다. `promise.then`보다 `async/await`를 사용하는 것이 보통은 더 편리하다.

---
:information_source: **`async/await`는 `Promise.all`과 함께 사용할 수 있다.**

여러 개의 프라미스가 모두 처리되길 기다려야 하는 상황이라면 이 프라미스들을 `Promise.all`로 감싸고 여기에 `await`를 붙여 사용할 수 있다.
```js
// 프라미스 처리 결과가 담긴 배열을 기다린다.
let results = await Promise.all([
  fetch(url1),
  fetch(url2),
  ...
]);
```
실패한 프라미스에서 발생한 에러는 보통 에러와 마찬가지로 `Promise.all`로 전파된다. 에러 때문에 생긴 예외는 `try..catch`로 처리할 수 있다.

---

## 요약
function 앞에  `async`  키워드를 추가하면 두 가지 효과가 있다.
1.  함수는 언제나 프라미스를 반환한다.
2.  함수 안에서  `await`를 사용할 수 있다.

프라미스 앞에  `await`  키워드를 붙이면 자바스크립트는 프라미스가 처리될 때까지 대기한다. 처리가 완료되면 조건에 따라 다음과 같은 동작이 이어진다.
1.  에러 발생: 예외가 생성된다(에러가 발생한 장소에서  `throw error`를 호출한 것과 동일하다).
2.  에러 미발생: 프라미스 객체의 result 값을 반환한다.

**`async/await`를 함께 사용하면 읽고, 쓰기 쉬운 비동기 코드를 작성할 수 있다.**

`async/await`를 사용하면  `promise.then/catch`를 사용할 일이 거의 없다. 하지만 가장 바깥 스코프에서 비동기 처리가 필요한 경우처럼  `promise.then/catch`를 써야만 하는 경우가 생기기 때문에  `async/await`가 프라미스를 기반으로 한다는 사실을 알고 있어야 한다. 

여러 작업이 있고, 이 작업들이 모두 완료될 때까지 기다리려면  `Promise.all`을 활용할 수 있다.
