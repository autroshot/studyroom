# async 이터레이터와 제너레이터
비동기 이터레이터(asynchronous iterator)를 사용하면 비동기적으로 들어오는 데이터를 필요에 따라 처리할 수 있다. 네트워크를 통해 데이터가 여러 번에 걸쳐 들어오는 상황을 처리할 수 있게 된다. 추가로 비동기 제너레이터(asynchronous generator)를 사용하면 이런 데이터를 좀 더 편리하게 처리할 수 있다.

## async 이터레이터
비동기 이터레이터는 일반 이터레이터와 유사하며 약간의 문법적인 차이가 있다.

[iterable 객체](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95.md#06-iterable-%EA%B0%9D%EC%B2%B4) 챕터에서 살펴본 바와 같이 ‘일반’ 이터러블은 객체이다.
```js
let range = {
  from: 1,
  to: 5,

  // for..of 최초 실행 시, Symbol.iterator가 호출된다.
  [Symbol.iterator]() {
    // Symbol.iterator 메서드는 이터레이터 객체를 반환한다.
    // 이후 for..of는 반환된 이터레이터 객체만을 대상으로 동작하는데,
    // 다음 값은 next()에서 정해진다.
    return {
      current: this.from,
      last: this.to,

      // for..of 반복문에 의해 각 이터레이션마다 next()가 호출된다.
      next() { // (2)
        //  next()는 객체 형태의 값, { done:.., value :... }를 반환한다.
        if (this.current <= this.last) {
          return { done: false, value: this.current++ };
        } else {
          return { done: true };
        }
      }
    };
  }
};

for(let value of range) {
  alert(value); // 1, 2, 3, 4, 5
}
```
이터러블 객체를 비동기적으로 만들려면 다음 작업이 필요하다.
1.  `Symbol.iterator`  대신,  `Symbol.asyncIterator`를 사용해야 한다.
2.  `next()`는 프라미스를 반환해야 한다.
3.  비동기 이터러블 객체를 대상으로 하는 반복 작업은  `for await (let item of iterable)`  반복문을 사용해 처리해야 한다.

위의 이터러블 객체 `range`를 토대로, 1초마다 비동기적으로 값을 반환하는 이터러블 객체를 만들어 보겠다.
```js
let range = {
  from: 1,
  to: 5,

  // for await..of 최초 실행 시, Symbol.asyncIterator가 호출된다.
  [Symbol.asyncIterator]() { // (1)
    // Symbol.asyncIterator 메서드는 이터레이터 객체를 반환한다.
    // 이후 for await..of는 반환된 이터레이터 객체만을 대상으로 동작하는데,
    // 다음 값은 next()에서 정해진다.
    return {
      current: this.from,
      last: this.to,

      // for await..of 반복문에 의해 각 이터레이션마다 next()가 호출된다.
      async next() { // (2)
        //  next()는 객체 형태의 값, { done:.., value :... }를 반환한다.
        // (객체는 async에 의해 자동으로 프라미스로 감싸진다)

        // 비동기로 무언가를 하기 위해 await를 사용할 수 있다.
        await new Promise(resolve => setTimeout(resolve, 1000)); // (3)

        if (this.current <= this.last) {
          return { done: false, value: this.current++ };
        } else {
          return { done: true };
        }
      }
    };
  }
};

(async () => {
  for await (let value of range) { // (4)
    alert(value); // 1,2,3,4,5
  }
})()
```
async 이터레이터는 일반 이터레이터와 구조가 유사한 것을 확인할 수 있다.

`next()`는  `async`  메서드일 필요는 없다. 프라미스를 반환하기만 하면 일반 메서드도 상관없다. 다만  `async`를 사용하면  `await`도 사용할 수 있기 때문에 여기서는 편의상  `async`메서드를 사용해 1초의 딜레이가 생기도록 했다.

일반 이터레이터와 async 이터레이터를 간단하게 비교하면 다음과 같다.
|       | 이터레이터 | async 이터레이터 |
|-------|-----------|-----------------|
| 이터레이터를 제공해주는 메서드 | `Symbol.iterator` | `Symbol.asyncIterator` |
| `next()`가 반환하는 값              | 모든 값         | `Promise`  |
| 반복 작업을 위해 사용하는 반복문                          | `for..of`         | `for await..of` |

---
:warning: **전개 문법 `...`은 비동기적으로 동작하지 않는다.**

일반적인 동기 이터레이터가 필요한 기능은 비동기 이터레이터와 함께 사용할 수 없다.

전개 문법은 일반 이터레이터가 필요하므로 다음 코드는 동작하지 않는다.
```js
alert( [...range] ); // Symbol.iterator가 없기 때문에 에러가 발생한다.
```
전개 문법은 `await`가 없는 `for..of`와 마찬가지로, `Symbol.asyncIterator`가 아닌 `Symbol.iterator`를 찾기 때문에 에러가 발생한다.

---

## async 제너레이터
이전 챕터에서 살펴봤던 `start`부터 `end`까지의 연속된 숫자를 생성해주는 제너레이터를 예시로 사용하겠다.
```js
function* generateSequence(start, end) {
  for (let i = start; i <= end; i++) {
    yield i;
  }
}

for(let value of generateSequence(1, 5)) {
  alert(value); // 1, 2, 3, 4, 5
}
```
일반 제너레이터는 동기적 문법이므로 `await`를 사용할 수 없다.

다음 예시와 같이 제너레이터 앞에 `async`를 붙여주면 `await`를 사용할 수 있다.
```js
async function* generateSequence(start, end) {
  for (let i = start; i <= end; i++) {
    // await를 사용할 수 있다.
    await new Promise(resolve => setTimeout(resolve, 1000));

    yield i;
  }
}

(async () => {
  let generator = generateSequence(1, 5);
  for await (let value of generator) {
    alert(value); // 1, 2, 3, 4, 5
  }
})();
```
async 제너레이터의 `generator.next()` 메서드는 비동기적이 되고, 프라미스를 반환한다.

일반 제너레이터에서는 `result = generator.next()`를 사용해 값을 얻었다. 반면 async 제너레이터에서는 다음과 같이 `await`를 붙여줘야 한다.
```js
result = await generator.next(); // result = { value: ..., done: true/false }
```

## async 이터러블
이전 챕터의 예시를 다시 상기해 보겠다.
```js
let range = {
  from: 1,
  to: 5,

  *[Symbol.iterator]() { // [Symbol.iterator]: function*()를 짧게 줄였다.
    for(let value = this.from; value <= this.to; value++) {
      yield value;
    }
  }
};

for(let value of range) {
  alert(value); // 1, 2, 3, 4, 5
}
```
제너레이터에 비동기 동작을 추가하려면 `Symbol.iterator`를 async `Symbol.asyncIterator`로 바꿔야 한다.
```js
let range = {
  from: 1,
  to: 5,

  async *[Symbol.asyncIterator]() { // [Symbol.asyncIterator]: async function*()와 동일하다.
    for(let value = this.from; value <= this.to; value++) {
      // 값 사이에 약간의 공백을 준다.
      await new Promise(resolve => setTimeout(resolve, 1000));

      yield value;
    }
  }
};

(async () => {
  for await (let value of range) {
    alert(value); // 1, 2, 3, 4, 5
  }

})();
```
이제 1초의 간격을 두고 값을 얻을 수 있다.

## 실제 사례
이제 실무에서 만날 수 있는 유스 케이스를 살펴 보겠다.

상당히 많은 온라인 서비스가 페이지네이션(pagination)을 구현해 데이터를 전송한다. 사용자 목록이 필요해서 서버에 요청을 보내면, 서버는 일정 단위로 사용자를 끊어 정보를 '한 페이지’로 구성한 후, 다음 페이지를 볼 수 있는 URL과 함께 응답한다.

이런 패턴은 사용자 목록 전송뿐만 아니라, 다양한 서비스에서 찾아볼 수 있다. GitHub에서 커밋 이력을 볼 때도 페이지네이션이 사용된다.

실제 GitHub API는 복잡하지만, 여기선 커밋 정보가 담긴 이터러블 객체를 만들어서 다음과 같이 객체를 대상으로 반복 작업을 할 수 있게 해주는 간단한 API를 만들어 보도록 하겠다.
```js
let repo = 'javascript-tutorial/en.javascript.info'; // 커밋 정보를 얻어올 GitHub 리포지토리이다.

for await (let commit of fetchCommits(repo)) {
  // 여기서 각 커밋을 처리한다.
}
```
필요할 때마다 요청을 보내 커밋 정보를 가져오는 함수 `fetchCommits(repo)`를 만들어 API를 구성하도록 하겠다. `fetchCommits(repo)`에서 페이지네이션 관련 일들을 모두 처리하도록 하면 원하는 대로 `for await..of`에서 각 커밋을 처리할 수 있을 것이다.

async 제너레이터를 이용하면 쉽게 함수를 구현할 수 있다.
```js
async function* fetchCommits(repo) {
  let url = `https://api.github.com/repos/${repo}/commits`;

  while (url) {
    const response = await fetch(url, { // (1)
      headers: { 'User-Agent': 'Our script' }, // GitHub는 모든 요청에 user-agent헤더를 강제한다.
    });

    const body = await response.json(); // (2) 응답은 JSON 형태로 온다(커밋이 담긴 배열).

    // (3) 헤더에 담긴 다음 페이지를 나타내는 URL을 추출한다.
    let nextPage = response.headers.get('Link').match(/<(.*?)>; rel="next"/);
    nextPage = nextPage?.[1];

    url = nextPage;

    for(let commit of body) { // (4) 페이지가 끝날 때까지 커밋을 하나씩 반환(yield)한다.
      yield commit;
    }
  }
}
```
1.  다운로드는  [fetch](../../03-%EC%B6%94%EA%B0%80%20%EC%A3%BC%EC%A0%9C/03-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC%20%EC%9A%94%EC%B2%AD/01-fetch.md)  메서드를 사용한다.  `fetch`를 사용하면 인증 정보나 헤더 등을 함께 실어 요청할 수 있습니다. GitHub에서 강제하는  `User-Agent`를 헤더에 실어 보내겠다.
2.  `fetch`  전용 메서드인  `response.json()`을 사용해 요청 결과를 JSON으로 파싱한다.
3.  응답의  `Link`  헤더에서 다음 페이지의 URL을 얻는다. 헤더에서  `https://api.github.com/repositories/93253246/commits?page=2`형태의 URL만 추출하기 위해 정규표현식을 사용하였다.
4.  커밋을 하나씩 반환하는데, 전체가 다 반환되면 다음  `while(url)`  반복문이 트리거되어 서버에 다시 요청을 보낸다.

사용법은 다음과 같다.
```js
(async () => {
  let count = 0;

  for await (const commit of fetchCommits('javascript-tutorial/en.javascript.info')) {
    console.log(commit.author.login);

    if (++count == 100) { // 100번째 커밋에서 멈춥니다.
      break;
    }
  }

})();
```

## 요약
일반적인 이터레이터와 제너레이터는 데이터를 가져오는 데 시간이 걸리지 않을 때에 적합하다.

그런데 약간의 지연이 있어서 데이터가 비동기적으로 들어오는 경우 async 이터레이터와 async 제너레이터, `for..of`대신 `for await..of`를 사용하게 된다.

일반 이터레이터와 async 이터레이터의 문법 차이는 다음과 같다.
|       | iterable | async iterable |
|-------|-----------|-----------------|
| iterator를 반환하는 메서드 | `Symbol.iterator` | `Symbol.asyncIterator` |
| `next()`가 반환하는 값 | `{value:..., done: true/false}`         | `{value:..., done: true/false}`를 감싸는 `Promise` |

일반 제너레이터와 async 제너레이터의 문법 차이는 다음과 같다.

|       | generators | async generator |
|-------|-----------|-----------------|
| 선언 | `function*` | `async function*` |
| `next()`가 반환하는 값          | `{value:..., done: true/false}`         | `{value:..., done: true/false}`를 감싸는 `Promise`  |

웹 개발을 하다 보면 띄엄띄엄 들어오는 데이터 스트림을 다뤄야 하는 경우가 자주 생긴다. 용량이 큰 파일을 다운로드하거나 업로드 할 때가 그 예이다. 이런 데이터를 처리할 때 async 제너레이터를 사용할 수 있다.

참고로 브라우저 등의 일부 호스트 환경은 데이터 스트림을 처리할 수 있게 해주는 API인 Streams을 제공하기도 한다. Streams API에서 제공하는 특별한 인터페이스를 사용하면, 데이터를 변경하여 한 스트림에서 다른 스트림으로 데이터를 전달할 수 있다. 따라서 한쪽에서 받은 데이터를 다른 쪽에 즉각 전달하는 것이 가능해진다.
