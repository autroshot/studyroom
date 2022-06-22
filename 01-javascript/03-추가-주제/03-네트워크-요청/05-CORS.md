# CORS

`fetch` 요청을 다른 웹사이트로 보내면 요청이 실패한다.

```js
try {
  await fetch('http://example.com');
} catch(err) {
  alert(err); // TypeError: Failed to fetch
}
```

도메인·프로토콜·포트 세 가지에 의해 결정되는 <strong>오리진(origin)</strong>이 달라서 요청이 실패한 것이다.

도메인이나 서브도메인, 프로토콜, 포트가 다른 곳에 요청을 보내는 것을 <strong>Cross-Origin Request(크로스 오리진 요청)</strong>라고 한다. 크로스 오리진 요청을 보내려면 원격 오리진에서 전송받은 특별한 헤더가 필요하다.

이러한 정책을 <strong>CORS(Cross-Origin Resource Sharing, 크로스 오리진 리소스 공유)</strong>라고 부른다.

## 왜 CORS가 필요한가에 대한 짧은 역사

과거 수 년 동안, 보안 상의 이유로 한 사이트의 스크립트에서 다른 사이트에 있는 콘텐츠에 접근할 수 없다는 제약이 있었다.

하지만 여러 가지 필요에 의해 개발자들은 이 제약을 피해 다른 웹 사이트에 요청을 보내기 위한 트릭들을 만들었다.

처음 네트워크 요청 메서드가 등장했을 때에는 크로스 오리진 요청이 불가능했다. 하지만 긴 논의 끝에 크로스 오리진 요청을 허용하기로 결정되었다. 대신 크로스 오리진 요청은 서버에서 명시적으로 크로스 오리진 요청을 ‘허가’ 했다는 것을 알려주는 특별한 헤더를 전송받았을 때만 가능하도록 제약을 걸게 되었다.

## 안전한 요청

크로스 오리진 요청은 <strong>안전한 요청(safe request)</strong>과 그 외로 구분된다.

안전한 요청은 다음의 두 가지 조건을 충족하는 말 그대로 안전한 요청이다.

1. 안전한 메서드(safe method) - GET이나 POST, HEAD를 사용한 요청
2. 안전한 헤더(safe header) - 다음 목록에 속하는 헤더
    - `Accept`
    - `Accept-Language`
    - `Content-Language`
    - 값이 `application/x-www-form-urlencoded`이나 `multipart/form-data`, `text/plain`인 `Content-Type`

두 조건을 모두 충족하지 않는 요청은 안전하지 않은(unsafe) 요청으로 분류된다. `PUT` 메서드를 사용하거나 헤더에 `API-Key`가 명시된 요청이 그 예이다.

안전한 요청과 그렇지 않은 요청의 근본적인 차이는 **특별한 방법을 사용하지 않고도 `<form>`이나 `<script>`를 사용해 요청을 만들 수 있다는 점**에 있다.

## CORS와 안전한 요청

크로스 오리진 요청을 보낼 경우 브라우저는 항상 `Origin`이라는 헤더를 요청에 추가한다.

`https://javascript.info/page`에서 `https://anywhere.com/request`에 요청을 보낸다고 가정해 보겠다. 헤더는 다음과 같은 형태가 된다.

```
GET /request
Host: anywhere.com
Origin: https://javascript.info
...
```

보시다시피 `Origin` 헤더에는 요청이 이뤄지는 페이지 경로(/page)가 아닌 오리진(도메인·프로토콜·포트) 정보가 담기게 된다.

서버는 요청 헤더에 있는 `Origin`를 검사하고, 요청을 받아들이기로 동의한 상태라면 특별한 헤더 `Access-Control-Allow-Origin`를 응답에 추가한다. 이 헤더에 허가된 오리진(위 예시에서는 `https://javascript.info`), 또는 `*`가 들어있으면 응답은 성공한 것이다.

이 과정에서 브라우저는 신뢰할 수 있는 중재자 역할을 한다.
1. 크로스 오리진 요청을 할 때 `Origin`에 올바른 값이 들어있다는 것을 보증한다.
2. 응답의 `Access-Control-Allow-Origin`로 서버의 허가 여부를 확인하고, 허가를 받았다면 자바스크립트를 이용한 응답 접근을 허용한다. 허가를 못 받았다면 에러를 일으킨다.

![xhr-another-domain](https://user-images.githubusercontent.com/95019875/170432624-34b1794d-9745-4ffd-a1b3-d382c0fbed61.svg)

다음은 허가를 받은 서버 응답의 예시이다.
```
200 OK
Content-Type:text/html; charset=UTF-8
Access-Control-Allow-Origin: https://javascript.info
```

## 응답 헤더

크로스 오리진 요청이 이뤄진 경우, 자바스크립트는 기본적으로 '안전한' 응답 헤더로 분류되는 헤더에만 접속할 수 있다. 안전한 응답 헤더는 다음과 같다.

- `Cache-Control`
- `Content-Language`
- `Content-Type`
- `Expires`
- `Last-Modified`
- `Pragma`

이 외의 응답 헤더에 접근하면 에러가 발생다.

---
:information_source: **주의:**

위 리스트에는 `Content-Length` 헤더가 없다.

`Content-Length` 헤더에는 전체 응답의 길이가 담겨있다. 무언가를 다운로드 중일 때 진행률을 추적하고 싶다면 이 헤더를 이용한다. 그리고 이 헤더에 접근하려면 추가적인 허가가 필요하다.

---

자바스크립트를 사용해 안전하지 않은 응답 헤더에 접근하려면 서버에서 `Access-Control-Expose-Headers`라는 헤더를 보내줘야 한다. `Access-Control-Expose-Headers`에는 자바스크립트 접근을 허용하는 안전하지 않은 헤더 목록이 담겨있다. 각 헤더는 콤마로 구분된다.

예시:

```
200 OK
Content-Type:text/html; charset=UTF-8
Content-Length: 12345
API-Key: 2c9de507f2c54aa1
Access-Control-Allow-Origin: https://javascript.info
Access-Control-Expose-Headers: Content-Length,API-Key
```

이제 자바스크립트로 응답 헤더의 `Content-Length`와 `API-Key`를 읽을 수 있게 되었다.

## 안전하지 않은 요청

HTTP 메소드로 `GET`, `POST`뿐만 아니라 `PATCH`, `DELETE`를 비롯한 어떤 메소드도 사용할 수 있다.

그런데 과거에는 웹페이지에서 `GET`, `POST` 이외의 HTTP 메서드를 사용해 요청을 보낼 것이라는 생각을 못했다. 아직까지도 이런 메서드를 다룰 수 없는 웹서버도 꽤 존재한다. 이런 서버들은 `GET`, `POST` 이외의 메서드를 사용한 요청이 오면 브라우저가 보낸 요청이 아니라고 판단하고 접근 권한을 확인한다.

이런 혼란스러운 상황을 피하고자 브라우저는 '안전하지 않은' 요청이 이뤄지는 경우, 서버에 바로 요청을 보내지 않고 'preflight' 요청이라는 사전 요청을 서버에 보내 권한이 있는지를 확인한다.

자세한 과정은 [원본](https://javascript.info/fetch-crossorigin#unsafe-requests)에서 확인할 수 있다.

참고로 preflight 요청은 무대 뒤에서 일어나기 때문에 자바스크립트에는 표시되지 않는다.

## 자격 증명

자바스크립트로 크로스 오리진 요청을 보내는 경우, 기본적으로 쿠키나 HTTP 인증 같은 <strong>자격 증명(credential)</strong>이 함께 전송되지 않는다.

HTTP 요청의 경우 대개 쿠키가 함께 전송되는데, 자바스크립트를 사용해 만든 크로스 오리진 요청은 예외이다.

따라서 `fetch('http://another.com')`를 사용해 요청을 보내도 `another.com` 관련 쿠키가 함께 전송되지 않는다.

이런 예외가 생긴 이유는 자격 증명과 함께 전송되는 요청은 훨씬 더 강력하기 때문이다. 이것이 허용되면 자바스크립트에서 자격 증명을 사용해서 사용자의 민감한 정보에 접근할 수 있게 된다.

그럼에도 불구하고 서버에서 이를 허용하고 싶다면, 자격 증명이 담긴 헤더를 명시적으로 허용하겠다는 세팅을 서버에 해야 한다.

`fetch` 메서드에 자격 증명 정보를 함께 전송하려면 다음과 같이 `credentials: "include"` 옵션을 추가하면 된다.

```js
fetch('http://another.com', {
  credentials: 'include'
});
```

이렇게 옵션을 추가하면 `fetch`로 요청을 보낼 때 `another.com`에 대응하는 쿠키가 함께 전송된다.

자격 증명 요청을 받기로 서버가 동의했다면 서버는 응답에 `Access-Control-Allow-Origin` 헤더와 `Access-Control-Allow-Credentials: true` 헤더를 추가해서 보낸다.

예시:

```
200 OK
Access-Control-Allow-Origin: https://javascript.info
Access-Control-Allow-Credentials: true
```

자격 증명이 함께 전송되는 요청을 보낼 때는 `Access-Control-Allow-Origin`에 `*`을 쓸 수 없다. 정확한 출처가 명시되어야 한다. 이것은 추가 안전 조치로, 요청을 보낸 곳이 신뢰할만한 곳인지를 서버가 확실히 알 수 있게 해준다.

## 요약

브라우저 관점에서 크로스 오리진 요청은 안전한(safe) 요청과 그렇지 않은 요청으로 나뉜다.

안전한 요청은 다음의 조건을 모두 충족하는 요청이다.
- 메서드: GET이나 POST 혹은 HEAD
- 헤더:
    - `Accept`
    - `Accept-Language`
    - `Content-Language`
    - 값이 `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`인 `Content-Type`

두 요청의 근본적인 차이점은 다음과 같다. 안전한 요청은 아주 오래전부터 `<form>`이나 `<script>` 태그를 이용해 보낼 수 있었다는 점이다.

실질적인 차이점은, 안전한 요청은 요청 즉시 `Origin` 헤더와 함께 보내지는 반면, 안전하지 않은 요청은 허가를 받기 위해 사전에 preflight 요청을 보낸다는 것이다.
