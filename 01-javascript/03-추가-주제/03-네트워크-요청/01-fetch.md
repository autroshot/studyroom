# fetch

자바스크립트는 필요할 때마다 서버에 네트워크 요청을 보내고 새로운 정보를 로드할 수 있다.

네트워크 요청을 이용해 다음 작업을 수행할 수 있다.
- 주문 제출하기
- 사용자 정보 로드하기
- 서버에서 최신 업데이트 받기

그리고 이 모든 것은 페이지를 새로고침하지 않아도 된다.

자바스크립트의 네트워크 요청에 대한 포괄적인 용어 'AJAX'(<b>A</b>synchronous <b>J</b>avaScript <b>A</b>nd <b>X</b>ML)가 있다. XML은 사용할 필요가 없다. 용어가 오래되어서 XML이 들어있는 것뿐이다.

서버에 네트워크 요청을 보내고 정보를 받아오는 방법에는 여러 가지가 있다.

모던하고 다재다능한 `fetch()` 메서드부터 알아보겠다. `fetch()`는 모던한 것들 중에는 구식 브라우저에서 가장 잘 지원된다. 지원이 안 되는 브라우저에서는 폴리필을 사용하면 된다.

문법:
```js
let promise = fetch(url, [options])
```
- `url` - 접근하려는 URL
- `options` - 선택적 매개변수로 메소드나 헤더 등을 지정할 수 있음

`options`을 비워두면 간단한 `GET` 요청이 된다.

`fetch()`를 호출하면 브라우저는 네트워크 요청을 보내고 결과를 기다리는 프라미스를 반환한다.

응답은 보통 두 단계에 거쳐 진행된다.

**먼저 서버에서 응답 헤더를 받으면, `fetch`에서 반환된 `promise`가 내장 클래스 [Response](https://fetch.spec.whatwg.org/#response-class)의 객체를 결과로 받으며 이행 상태가 된다.**

이 단계에서는 헤더의 HTTP 상태(status)로 성공 여부는 확인할 수 있지만, 아직 본문(body)은 확인할 수 없다.

네트워크 문제나 잘못된 URL 때문에 HTTP 요청을 만들 수 없었으면 프라미스는 거부 상태가 된다. 404나 500 같은 비정상적인 HTTP 상태는 오류를 일으키지 않는다.

HTTP 상태는 응답 프로퍼티에서 확인할 수 있다.
- **`status`** - HTTP 상태 코드(예: 200)
- **`ok`** - 불린 값으로 HTTP 상태 코드가 200과 299 사이일 경우 `true`이다.

예시:
```js
let response = await fetch(url);

if (response.ok) { // HTTP 상태 코드가 200~299일 경우
  // 응답 본문을 받는다(관련 메서드는 아래에서 설명한다).
  let json = await response.json();
} else {
  alert('HTTP-Error: ' + response.status);
}
```

**두 번째 단계에서는 추가 메서드를 이용해 응답의 본문을 얻는다.**

`response`에는 프라미스를 기반으로 하는 다양한 메서드가 있다. 이 메서드를 사용하면 다양한 형태의 응답 본문을 처리할 수 있다.

|메소드|설명|
|---|---|
|`response.text()`|응답을 읽고 텍스트를 반환한다.|
|`response.json()`|응답을 JSON 형태로 파싱한다.|
|`response.formData()`|응답을 `FormData` 객체 형태로 반환한다. `FormData`에 대한 자세한 내용은 [FormData 객체]()에서 다룰 것이다.|
|`response.blob()`|응답을 [Blob](https://ko.javascript.info/blob)(타입이 있는 바이너리 데이터) 형태로 반환한다.|
|`response.arrayBuffer()`|응답을 [ArrayBuffer](info:arraybuffer-binary-arrays)(바이너리 데이터를 로우 레벨 형식으로 표현한 것) 형태로 반환한다.|
|`response.body`|[ReadableStream](https://streams.spec.whatwg.org/#rs-class) 객체인 `response.body`를 사용하면 응답 본문을 청크 단위로 읽을 수 있다.|

지금까지 배운 내용을 토대로 GitHub에서 마지막 커밋을 JSON 객체 형태로 받아 보겠다.
```js
let url = 'https://api.github.com/repos/javascript-tutorial/ko.javascript.info/commits';
let response = await fetch(url);

let commits = await response.json(); // 응답 본문을 읽고 JSON 형태로 파싱한다.

alert(commits[0].author.login);
```
위 예시를 `await` 없이 프라미스만 사용하면 다음과 같이 바꿀 수 있다.
```js run
fetch('https://api.github.com/repos/javascript-tutorial/en.javascript.info/commits')
  .then(response => response.json())
  .then(commits => alert(commits[0].author.login));
```
응답을 텍스트 형태로 얻으려면 `.json()` 대신 `await response.text()`를 사용하면 된다.
```js
let response = await fetch('https://api.github.com/repos/javascript-tutorial/en.javascript.info/commits');

let text = await response.text(); // 응답 본문을 텍스트 형태로 읽는다.

alert(text.slice(0, 80) + '...');
```
다음은 `fetch`를 사용해 로고 이미지(바이너리 데이터)를 가져오는 예시이다.
```js async run
let response = await fetch('/article/fetch/logo-fetch.svg');

let blob = await response.blob(); // 응답을 Blob 객체 형태로 다운로드받는다.

// 다운로드받은 Blob을 담을 <img>를 만든다.
let img = document.createElement('img');
img.style = 'position:fixed;top:10px;left:10px;width:100px';
document.body.append(img);

// 이미지를 화면에 보여준다.
img.src = URL.createObjectURL(blob);

setTimeout(() => { // 3초 후 이미지를 숨긴다.
  img.remove();
  URL.revokeObjectURL(img.src);
}, 3000);
```

---
:warning: **중요:**

본문을 읽는 메서드는 하나만 선택할 수 있다.

예를 들어, `response.text()`를 사용해 이미 응답을 얻은 뒤에 `response.json()`을 사용하면 동작하지 않는다. 본문 콘텐츠가 이미 처리된 상태이기 때문이다.
```js
let text = await response.text(); // 응답 본문이 소비된다.
let parsed = await response.json(); // 이미 소비되어서 실패한다.
```
---

## 응답 헤더

응답 헤더는 `response.headers`에 맵과 유사한 형태로 저장된다.

맵은 아니지만 맵과 유사한 메서드를 지원한다. 메서드를 사용하면 헤더 일부만 추출하거나 헤더 전체를 순회할 수 있다.
```js
let response = await fetch('https://api.github.com/repos/javascript-tutorial/en.javascript.info/commits');

// 헤더 일부를 추출
alert(response.headers.get('Content-Type')); // application/json; charset=utf-8

// 헤더 전체를 순회
for (let [key, value] of response.headers) {
  alert(`${key} = ${value}`);
}
```

## 요청 헤더

`headers` 옵션을 사용하면 `fetch`의 요청 헤더를 설정할 수 있다. 다음과 같이 헤더들로 이루어진 객체를 넘겨줄 수 있다.
```js
let response = fetch(protectedUrl, {
  headers: {
    Authentication: 'secret'
  }
});
```
그런데 헤더 중에는 설정이 불가능한 [금지된 HTTP 헤더](https://fetch.spec.whatwg.org/#forbidden-header-name)가 존재한다. 그 목록은 다음과 같다.
- `Accept-Charset`, `Accept-Encoding`
- `Access-Control-Request-Headers`
- `Access-Control-Request-Method`
- `Connection`
- `Content-Length`
- `Cookie`, `Cookie2`
- `Date`
- `DNT`
- `Expect`
- `Host`
- `Keep-Alive`
- `Origin`
- `Referer`
- `TE`
- `Trailer`
- `Transfer-Encoding`
- `Upgrade`
- `Via`
- `Proxy-*`
- `Sec-*`

이 헤더들은 적절하고 안전한 HTTP를 보장하므로 브라우저에 의해서만 제어된다.

## POST 요청

`POST` 요청을 만들거나 다른 메소드로 요청을 만들려면 `fetch`의 옵션을 사용해야 한다.
- **`method`** - HTTP 메서드(예: `POST`)
- **`body`** - 요청 본문으로 다음 항목 중 하나이다.
  - 문자열(예: JSON 문자열)
  - `FormData`객체 - `form/multipart` 형태로 데이터를 전송하기 위해 쓰인다.
  - `Blob`나 `BufferSource` - 바이너리 데이터 전송을 위해 쓰인다.
  - [URLSearchParams](https://ko.javascript.info/url) - 데이터를 `x-www-form-urlencoded` 형태로 보내기 위해 쓰이는데, 요즘에는 잘 사용하지 않는다.

대부분은 JSON을 요청 본문에 실어 보내게 된다.

다음은 `user` 객체를 본문에 실어 보내는 예시이다.
```js run async
let user = {
  name: 'John',
  surname: 'Smith'
};

let response = await fetch('/article/fetch/post/user', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json;charset=utf-8'
  },
  body: JSON.stringify(user)
});

let result = await response.json();
alert(result.message);
```
요청 `body`가 문자열인 경우에는 `Content-Type` 헤더가 기본값인 `text/plain;charset=UTF-8`로 설정된다는 것을 주의해야 한다.

위 예시에서는 JSON을 보낼 것이므로 `headers` 옵션을 이용해 `Content-Type`을 `application/json`로 명시했다.

## 이미지 전송하기

`Blob`이나 `BufferSource` 객체를 사용하면 `fetch`로 바이너리 데이터를 전송할 수 있다.

다음 예시에서는 `<canvas>`에서 마우스를 움직여 이미지를 만들고 '전송' 버튼을 누르면 이미지를 서버에 전송한다.
```html
<body style="margin:0">
  <canvas id="canvasElem" width="100" height="80" style="border:1px solid"></canvas>

  <input type="button" value="전송" onclick="submit()">

  <script>
    canvasElem.onmousemove = function(e) {
      let ctx = canvasElem.getContext('2d');
      ctx.lineTo(e.clientX, e.clientY);
      ctx.stroke();
    };

    async function submit() {
      let blob = await new Promise(resolve => canvasElem.toBlob(resolve, 'image/png'));
      let response = await fetch('/article/fetch/post/image', {
        method: 'POST',
        body: blob
      });

      // 전송이 잘 되었다는 응답이 오고 이미지 사이즈가 얼럿창에 출력된다.
      let result = await response.json();
      alert(result.message);
    }

  </script>
</body>
```

이번에는 `Content-Type` 헤더를 명시적으로 설정하지 않았다. `Blob` 객체는 내장 타입을 갖기 때문에 `Content-Type`를 별도로 설정하지 않아도 된다. 예시는 이미지를 전송하기 때문에 `toBlob`에 의해 `image/png`가 자동으로 설정되었다. 이렇게 `Blob` 객체의 경우 해당 객체의 타입이 `Content-Type` 헤더의 값이 된다.

위 예시의 함수 `submit()`을 `async/await` 없이 작성하면 다음과 같다.
```js
function submit() {
  canvasElem.toBlob(function(blob) {        
    fetch('/article/fetch/post/image', {
      method: 'POST',
      body: blob
    })
      .then(response => response.json())
      .then(result => alert(JSON.stringify(result, null, 2)))
  }, 'image/png');
}
```

## 요약

일반적인 `fetch` 요청은 다음과 같이 두 개의 `await` 호출로 구성된다.
```js
let response = await fetch(url, options); // 응답 헤더와 함께 이행된다.
let result = await response.json(); // json 본문을 읽는다.
```
물론 `await` 대신 프라미스를 사용해도 된다.
```js
fetch(url, options)
  .then(response => response.json())
  .then(result => /* 결과 처리 */)
```
응답 객체의 프로퍼티는 다음과 같다.
- `response.status` - 응답의 HTTP 코드
- `response.ok` - 응답 상태가 200과 299 사이에 있는 경우 `true`
- `response.headers` - 맵과 유사한 형태의 HTTP 헤더

응답 본문을 얻으려면 다음과 같은 메서드를 사용하면 된다.
- `response.text()` - 응답을 텍스트 형태로 반환함
- `response.json()` - 응답을 JSON 객체로 변환함
- `response.formData()` - 응답을 `FormData` 객체 형태로 반환(`multipart/form-data` 인코딩은 다음 챕터에서 다룸)
- `response.blob()` - 응답을 [Blob](https://javascript.info/blob)(타입이 있는 바이너리 데이터) 형태로 반환함
- `response.arrayBuffer()` -  응답을 [ArrayBuffer](https://javascript.info/arraybuffer-binary-arrays)(바이너리 데이터를 로우 레벨로 표현한 것) 형태로 반환함

지금까지 배운 `fetch` 옵션은 다음과 같다.
- `method` - HTTP 메서드
- `headers` - 요청 헤드가 담긴 객체(모든 헤더가 허용되지는 않음)
- `body` - `string`, `FormData`, `BufferSource`, `Blob`, `UrlSearchParams` 객체 형태로 보낼 데이터
