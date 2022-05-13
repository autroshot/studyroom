# 프라미스 API
`Promise` 클래스에는 5가지의 정적 메서드가 있다.

## Promise.all
여러 개의 프라미스를 동시에 실행시키고 모든 프라미스가 준비될 때까지 기다리는 상황이 있을 수 있다. 복수의 URL에 동시에 요청을 보내고, 다운로드가 모두 완료된 후에 콘텐츠를 처리할 때가 그 예이다.

`Promise.all`은 이때 사용할 수 있다.

문법:
```js
let promise = Promise.all([...promises...]);
```
`Promise.all`은 요소 전체가 프라미스인 배열(엄밀히는 이터러블 객체)을 받고 새로운 프라미스를 반환한다.

배열 안의 프라미스가 모두 처리되면 새로운 프라미스가 이행되는데, 배열 안의 프라미스의 결괏값을 담은 배열이 새로운 프라미스의 `result`가 된다.

다음 `Promise.all`은 3초 후에 처리되고, 반환되는 프라미스의 `result`는 배열 `[1, 2, 3]`이 된다.
```js
Promise.all([
  new Promise(resolve => setTimeout(() => resolve(1), 3000)), // 1
  new Promise(resolve => setTimeout(() => resolve(2), 2000)), // 2
  new Promise(resolve => setTimeout(() => resolve(3), 1000))  // 3
]).then(alert); // 프라미스 전체가 처리되면 1, 2, 3이 반환된다. 각 프라미스는 배열을 구성하는 요소가 된다.
```
**배열 `result`의 요소 순서는 `Promise.all`에 전달된 프라미스 순서와 동일하다**는 점을 유의해야 한다. 이행 순서와는 무관하다.

작업해야 할 데이터가 담긴 배열을 프라미스 배열로 매핑하고, 이 배열을  `Promise.all`로 감싸는 트릭이 자주 사용된다.

다음은 URL이 담긴 배열을  `fetch`를 써서 처리하는 예시이다.
```js
let urls = [
  'https://api.github.com/users/iliakan',
  'https://api.github.com/users/Violet-Bora-Lee',
  'https://api.github.com/users/jeresig'
];

// fetch를 사용해 url을 프라미스로 매핑한다.
let requests = urls.map(url => fetch(url));

// Promise.all은 모든 작업이 이행될 때까지 기다린다.
Promise.all(requests)
  .then(responses => responses.forEach(
    response => alert(`${response.url}: ${response.status}`)
  ));
```
다음은 GitHub 유저네임이 담긴 배열을 사용해 사용자 정보를 가져오는 예시이다.
```js
let names = ['iliakan', 'Violet-Bora-Lee', 'jeresig'];

let requests = names.map(name => fetch(`https://api.github.com/users/${name}`));

Promise.all(requests)
  .then(responses => {
    // 모든 응답이 성공적으로 이행되었다.
    for(let response of responses) {
      alert(`${response.url}: ${response.status}`); // 모든 url의 응답코드가 200이다.
    }

    return responses;
  })
  // 응답 메시지가 담긴 배열을 response.json()로 매핑해, 내용을 읽는다.
  .then(responses => Promise.all(responses.map(r => r.json())))
  // JSON 형태의 응답 메시지는 파싱 되어 배열 'users'에 저장됩니다.
  .then(users => users.forEach(user => alert(user.name)));
```
`Promise.all`에 전달되는 프라미스 중 하나라도 거부되면,  `Promise.all`이 반환하는 프라미스는 에러와 함께 바로 거부된다.
```js
Promise.all([
  new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
  new Promise((resolve, reject) => setTimeout(() => reject(new Error('에러 발생!')), 2000)),
  new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
]).catch(alert); // Error: 에러 발생!
```
---
:warning: **에러가 발생하면 다른 프라미스는 무시된다.**

프라미스가 하나라도 거부되면 `Promise.all`은 즉시 거부되고 배열에 저장된 다른 프라미스의 결과는 완전히 무시된다. 이행된 프라미스의 결과도 무시된다.

`fetch`를 사용해 호출 여러 개를 만들면, 그중 하나가 실패하더라도 호출은 계속 일어난다.

프라미스에는 '취소’라는 개념이 없어서 `Promise.all`도 프라미스를 취소하지 않는다. [Fetch: Abort](https://ko.javascript.info/fetch-abort) 챕터에서 배울 `AbortController`를 사용하면 프라미스 취소가 가능은 하지만, `AbortController`는 프라미스 API는 아니다.

---

:information_source: **`이터러블 객체`가 아닌 일반 값도 `Promise.all(iterable)`에 넘길 수 있다.**

`Promise.all(...)`은 대개 프라미스가 요소인 이러터블 객체(대부분 배열)를 받는다. 그런데 요소가 프라미스가 아닌 객체일 경우에는 요소 ‘그대로’ 결과 배열에 전달된다.
```js
Promise.all([
  new Promise((resolve, reject) => {
    setTimeout(() => resolve(1), 1000)
  }),
  2,
  3
]).then(alert); // 1, 2, 3
```
이미 결과를 알고 있는 값은 이 특징을 이용해 `Promise.all`에 그대로 전달하면 된다.

---

## Promise.allSettled
---
:warning: **최근에 추가됨**

스펙에 추가된 지 얼마 안 된 문법이다. 구식 브라우저는 폴리필이 필요하다.

---
`Promise.all`은 프라미스가 하나라도 거절되면 전체를 거절한다. 따라서 프라미스 결과가 모두 필요한 경우처럼 ‘모 아니면 도’ 일 때 유용하다.
```js
Promise.all([
  fetch('/template.html'),
  fetch('/style.css'),
  fetch('/data.json')
]).then(render); // render 메서드는 fetch 결과가 전부 있어야 제대로 동작한다.
```
반면, `Promise.allSettled`는 모든 프라미스가 처리될 때까지 기다린다. 반환되는 배열은 다음과 같은 요소를 갖는다.
-   응답이 성공할 경우: `{status:"fulfilled", value:result}`
-   에러가 발생한 경우: `{status:"rejected", reason:error}`

`fetch`를 사용해 여러 사람의 정보를 가져오고 있다고 가정해 보겠다. 여러 요청 중 하나가 실패해도 다른 요청 결과는 여전히 필요하다.

이때  `Promise.allSettled`를 사용할 수 있다.
```js run
let urls = [
  'https://api.github.com/users/iliakan',
  'https://api.github.com/users/Violet-Bora-Lee',
  'https://no-such-url'
];

Promise.allSettled(urls.map(url => fetch(url)))
  .then(results => { // (*)
    results.forEach((result, num) => {
      if (result.status == 'fulfilled') {
        alert(`${urls[num]}: ${result.value.status}`);
      }
      if (result.status == 'rejected') {
        alert(`${urls[num]}: ${result.reason}`);
      }
    });
  });
```
`(*)`로 표시한 줄의 `results`는 다음과 같다.
```js
[
  {status: 'fulfilled', value: ...응답...},
  {status: 'fulfilled', value: ...응답...},
  {status: 'rejected', reason: ...에러 객체...}
]
```
`Promise.allSettled`를 사용하면 이처럼 각 프라미스의 상태와 '값 또는 에러'를 받을 수 있다.

## Promise.race
`Promise.race`는 `Promise.all`과 비슷하다. 다만 가장 먼저 처리되는 프라미스의 결과혹은 에러를 반환한다.

문법:
```js
let promise = Promise.race(iterable);
```
예시:
```js
Promise.race([
  new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
  new Promise((resolve, reject) => setTimeout(() => reject(new Error("에러 발생!")), 2000)),
  new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
]).then(alert); // 1
```
첫 번째 프라미스가 가장 빨리 처리 상태가 되기 때문에 첫 번째 프라미스의 결과가 result 값이 된다. `Promise.race`에서는 '경주의 승자’가 나타난 순간 다른 프라미스의 결과 또는 에러는 무시된다.

## Promise.resolve와 Promise.reject
프라미스 메서드 `Promise.resolve`와 `Promise.reject`는 [async와 await](https://ko.javascript.info/async-await) 챕터에서 배우는 `async/await` 문법이 생긴 후로 쓸모없어졌기 때문에 근래에는 거의 사용하지 않는다.

### Promise.resolve
`Promise.resolve(value)`는 결괏값이  `value`인 이행 상태 프라미스를 생성한다.

다음 코드와 동일한 일을 수행한다.
```js
let promise = new Promise(resolve => resolve(value));
```
아래 함수 `loadCached`는 인수로 받은 URL을 대상으로 `fetch`를 호출하고, 그 결과를 기억(cache)한다. 나중에 동일한 URL을 대상으로 `fetch`를 호출하면 캐시에서 호출 결과를 즉시 가져오는데, 이때 `Promise.resolve`를 사용해 캐시 된 내용을 프라미스로 만들어 반환 값이 항상 프라미스가 되게 한다.
```js
let cache = new Map();

function loadCached(url) {
  if (cache.has(url)) {
    return Promise.resolve(cache.get(url)); // (*)
  }

  return fetch(url)
    .then(response => response.text())
    .then(text => {
      cache.set(url,text);
      return text;
    });
}
```
`loadCached`를 호출하면 프라미스가 반환된다는 것이 보장되기 때문에 `loadCached(url).then(...)`을 사용할 수 있다.

### Promise.reject
`Promise.reject(error)`는 결괏값이  `error`인 거부 상태 프라미스를 생성한다.

다음 코드와 동일한 일을 수행한다.
```js
let promise = new Promise((resolve, reject) => reject(error));
```

## 요약
#### `Promise.all(promises)`
모든 프라미스가 이행될 때까지 기다렸다가 그 결괏값을 담은 배열을 반환한다. 주어진 프라미스 중 하나라도 실패하면  `Promise.all`는 거부되고, 나머지 프라미스의 결과는 무시된다.

#### `Promise.allSettled(promises)`
최근에 추가된 메서드로 모든 프라미스가 처리될 때까지 기다렸다가 그 결과(객체)를 담은 배열을 반환한다. 객체에는 다음과 같은 정보가 담긴다.
    -   `status`:  `"fulfilled"`  또는  `"rejected"`
    -   `value`(프라미스가 성공한 경우) 또는  `reason`(프라미스가 실패한 경우)

#### `Promise.race(promises)`
가장 먼저 처리된 프라미스의 결과 또는 에러를 담은 프라미스를 반환한다.

#### `Promise.resolve(value)`
주어진 값을 사용해 이행 상태의 프라미스를 만든다.

#### `Promise.reject(error)`
주어진 에러를 사용해 거부 상태의 프라미스를 만든다.

실무에서는 `Promise.all`을 가장 많이 사용한다.
