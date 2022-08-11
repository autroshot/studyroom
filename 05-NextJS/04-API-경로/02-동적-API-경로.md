# 동적 API 경로

API 경로는 [동적 경로](https://nextjs.org/docs/routing/dynamic-routes)를 지원하며 `pages`와 동일한 파일 명명 규칙을 따른다.

예를 들어 API 경로 `pages/api/post/[pid].js`가 다음과 같다고 가정해 보겠다.

```js
export default function handler(req, res) {
  const { pid } = req.query
  res.end(`Post: ${pid}`)
}
```

이제 `/api/post/abc`에 대한 요청은 `Post: abc`로 응답한다.

### 인덱스 경로와 동적 API 경로

매우 일반적인 RESTful 패턴은 경로를 다음과 같이 설정하는 것이다.

- `GET api/posts` - 페이지가 매겨진 게시물 목록을 얻음
- `GET api/posts/12345`- 아이디가 12345인 게시물을 얻음

이를 두 가지 방법으로 모델링할 수 있다.

- 옵션 1:
  - `/api/posts.js`
  - `/api/posts/[postId].js`
- 옵션 2:
  - `/api/posts/index.js`
  - `/api/posts/[postId].js`

둘은 동일하다.

`/api/posts/[postId].js`만을 사용하는 것은 유효하지 않다. 동적 경로(아래의 모든 경로 잡기 포함)에는 어떤 상황에서도 `undefined` 상태가 없고 `GET api/posts`는 `/api/posts/[postId].js`와 일치하지 않기 때문이다.

### 모든 API 경로 잡기

API 경로는 대괄호 안에 점 세 개(`...`)를 추가해 모든 경로를 잡도록 확장할 수 있다.

예를 들어 `pages/api/post/[...slug].js`는 `/api/post/a`뿐만 아니라 `/api/post/a/b`, `/api/post/a/b/c` 등을 일치시킨다.

> **참고**
>
> `slug` 대신 `[...param]`와 같은 다른 이름을 사용할 수 있다.

일치하는 매개변수는 페이지에 쿼리 매개변수(예시에서는 `slug`)로 전송되며, 항상 배열이므로 경로 `api/post/a`의 `query` 객체는 다음과 같다.

```json
{ "slug": ["a"] }
```

그리고 `api/post/a/b`와 같은 경로에는 다음과 같이 새 매개변수가 배열에 추가된다.

```json
{ "slug": ["a", "b"] }
```

`pages/api/post/[...slug].js`에 대한 API 경로는 다음과 같이 작성할 수 있다.

```js
export default function handler(req, res) {
  const { slug } = req.query
  res.end(`Post: ${slug.join(', ')}`)
}
```

이제 `/api/post/a/b/c`에 대한 요청은 `Post: a, b, c`로 응답한다.

### 선택적으로 모든 API 경로 잡기

'모든 경로 잡기'는 이중 대괄호에 매개변수를 넣어서(`[[...slug]]`) 선택적으로 만들 수 있다.

예를 들어 `pages/api/post/[[...slug]].js`는 `/api/post`, `/api/post/a`, `/api/post/a/b`를 일치시킨다.

'선택적으로 모든 경로 잡기'의 주요 차이점은 매개변수가 없는 경로도 일치한다는 점이다. `/api/post`가 그 예이다.

`query` 객체는 다음과 같다.

```json
{ } // GET `/api/post` (빈 객체)
{ "slug": ["a"] } // `GET /api/post/a` (단일 요소를 가진 배열)
{ "slug": ["a", "b"] } // `GET /api/post/a/b` (여러 요소를 가진 배열)
```

## 주의 사항

- 미리 정의된 API 경로는 동적 API 경로보다 우선하며 동적 API 경로는 모든 API 경로를 잡는다.

  예시:

  - `pages/api/post/create.js`는 `/api/post/create`를 일치시킴
  - `pages/api/post/[pid].js`는 `/api/post/1`, `/api/post/abc`를 일치시킴. `/api/post/create`는 일치시키지 않음
  - `pages/api/post/[...slug].js`는 `/api/post/1/2`, `/api/post/a/b/c`를 일치시킴. `/api/post/create`, `/api/post/abc`는 일치시키지 않음

