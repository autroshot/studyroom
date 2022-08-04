# 동적 API 경로

API 라우트는 [동적 경로](https://nextjs.org/docs/routing/dynamic-routes)를 지원하며 `pages`에 사용된 것과 동일한 파일 명명 규칙을 따른다.

예를 들어 API 경로 `pages/api/post/[pid].js`에는 다음 코드가 있다.

```js
export default function handler(req, res) {
  const { pid } = req.query
  res.end(`Post: ${pid}`)
}
```

이제 `/api/post/abc`에 대한 요청은 `Post: abc`로 응답한다.