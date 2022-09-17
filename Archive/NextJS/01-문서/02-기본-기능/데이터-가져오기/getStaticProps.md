# getStaticProps

페이지에서 `getStaticProps`(정적 사이트 생성)이라는 함수를 내보내면, 넥스트는 `getStaticProps`에서 반환된 props를 사용하여 빌드 타임에 이 페이지를 미리 렌더링합니다.

```jsx
export async function getStaticProps(context) {
  return {
    props: {}, // 페이지 컴포넌트에 props로 전달됩니다.
  }
}
```

> **참고**
>
> 렌더링 방식에 관계없이 모든 `props`는 페이지 컴포넌트로 전달되고 초기 HTML의 클라이언트 측에서 볼 수 있습니다. 이것은 페이지의 올바른 수화를 위한 것입니다. 클라이언트에 있으면 안 되는 민감한 정보는 `props`로 전달하면 안 됩니다.

## getStaticProps는 언제 사용해야 할까

다음의 경우에 `getStaticProps`을 사용해야 합니다.

- 페이지를 렌더링하는 데 필요한 데이터가 사용자의 요청에 앞서 빌드 타임에 얻을 수 있음
- 데이터를 헤드리스 CMS에서 가져옴
- 페이지는 SEO를 위해 사전 렌더링되어야 하며 매우 빨라야 함. `getStaticProps`는 `HTML`과 `JSON`파일을 생성하며, 둘 다 성능을 위해 CDN에 캐시될 수 있음
- 데이터가 공개적으로 캐시될 수 있음(사용자 테이터가 아님). 이 조건은 미들웨어를 사용하여 경로를 다시 작성하는 특정 상황에서 우회가 가능함

## getStaticProps는 언제 실행될까

`getStaticProps`는 항상 서버에서 실행되고 클라이언트에서는 실행되지 않습니다. 이 [도구](https://next-code-elimination.vercel.app/)를 사용하여 `getStaticProps` 내부에 작성된 코드가 클라이언트 측 번들에서 제거되었는지 확인할 수 있습니다.

- `getStaticProps`는 `next build` 과정에서 항상 실행됨
- `getStaticProps`는 [`fallback: true`](https://nextjs.org/docs/api-reference/data-fetching/get-static-paths#fallback-true)를 사용할 때 백그라운드에서 실행됨
- `getStaticProps`는 [`fallback: blocking`](https://nextjs.org/docs/api-reference/data-fetching/get-static-paths#fallback-blocking)를 사용할 때 첫 랜더링 전에 호출됨
- `getStaticProps`는 `revalidate`를 사용할 때 백그라운드에서 실행됨
- `getStaticProps`는 [`revalidate()`](./점진적인-정적-재생성.md/#주문형-재검증)을 사용할 때 백그라운드에서 주문형으로 실행됨

[점진적인 정적 재생성](./점진적인-정적-재생성.md)과 함께 사용하면, `getStaticProps`는 오래된 페이지의 유효성을 다시 검사하고 새 페이지가 브라우저에 제공되는 동안 백그라운드에서 실행됩니다.

`getStaticProps`는 정적 HTML을 생성하므로 들어오는 요청(쿼리 매개변수나 HTTP 헤더)에 접근할 수 없습니다. 페이지 요청에 접근해야 하는 경우에는 `getStaticProps`에 추가로 [미들웨어](https://nextjs.org/docs/advanced-features/middleware)를 사용하는 것을 고려해야 합니다.

## getStaticProps를 사용하여 CMS에서 데이터 가져오기

다음 예시는 CMS에서 블로그 게시물 목록을 가져오는 방법을 보여줍니다.

```jsx
// posts는 빌드 타임에 getStaticProps()에 의해 채워질 것입니다.
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

// 이 함수는 서버 측에서 빌드 타임에 호출됩니다.
// 클라이언트 측에서 호출되지 않으므로 데이터베이스 쿼리를 여기서 직접 해도 됩니다.
export async function getStaticProps() {
  // posts를 얻기 위해 외부 API 엔드포인트를 호출합니다.
  // 모든 데이터 가져오기 라이브러리를 사용할 수 있습니다.
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // { props: { posts } }을 반환함으로써, Blog 컴포넌트는
  // posts를 빌드 타임에 프롭으로 받을 것입니다.
  return {
    props: {
      posts,
    },
  }
}

export default Blog
```

[`getStaticProps` API 레퍼런스](https://nextjs.org/docs/api-reference/data-fetching/get-static-props)는 `getStaticProps`에서 사용할 수 있는 모든 매개변수와 props를 다룹니다.

## 서버 측 코드 직접 작성하기

`getStaticProps`는 서버 측에서만 실행되므로 클라이언트 측에서는 실행되지 않습니다. 브라우저용 JS 번들에도 포함되지 않으므로 브라우저로 보내지 않고 직접 데이터베이스 쿼리를 작성할 수 있습니다.

즉, `getStaticProps`에서 외부 소스에서 데이터를 가져오는 API 경로를 사용하는 대신 `getStaticProps`에 직접 서버 측 코드를 작성할 수 있습니다.

다음 예시에서 API 경로는 CMS에서 일부 데이터를 가져오는 데 사용됩니다. 그러면 해당 API 경로가 `getStaticProps`에서 직접 호출됩니다. 이 방식은 추가적인 호출이 발생하여 성능이 저하됩니다. 대신 CMS에서 데이터를 가져오는 논리를 `lib/` 디렉터리에서 `getStaticProps`에 공유할 수 있습니다.

```jsx
// lib/load-posts.js

// 다음 함수는 `lib/` 디렉터리에서 
// getStaticProps와 API 경로에 공유됩니다.
export async function loadPosts() {
  // posts를 얻기 위해 외부 API 엔드포인트를 호출합니다.
  const res = await fetch('https://.../posts/')
  const data = await res.json()

  return data
}

// pages/blog.js
import { loadPosts } from '../lib/load-posts'

// 이 함수는 서버 측에서만 실행됩니다.
export async function getStaticProps() {
  // `/api` 경로를 가져오는 대신 getStaticProps에서 직접
  // 동일한 함수를 호출할 수 있습니다.
  const posts = await loadPosts()

  // 반환되는 props는 페이지 컴포넌트에 전달됩니다.
  return { props: { posts } }
}
```

또는 데이터를 가져오기를 위해 API 경로를 사용하는 대신, [`fetch()`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) API를 `getStaticProps`에서 직접 사용하는 것도 가능합니다.

넥스트가 클라이언트 측 번들에서 제거하는 것을 확인하려면 [next-code-elimination 도구](https://next-code-elimination.vercel.app/)를 사용할 수 있습니다.

## HTML과 JSON을 정적으로 생성하기

`getStaticProps`가 포함된 페이지가 빌드 타임에 미리 렌더링되면, 넥스트가 페이지 HTML 파일 외에 `getStaticProps`의 실행 결과를 담은 JSON 파일을 생성합니다.

JSON 파일은 [`next/link`](https://nextjs.org/docs/api-reference/next/link)이나 [`next/router`](https://nextjs.org/docs/api-reference/next/router)를 통한 클라이언트 측 라우팅에 사용됩니다. `getStaticProps`를 사용하여 미리 렌더링된 페이지로 이동하면 넥스트가 이 JSON 파일(빌드 타임에 미리 계산됨)을 가져와 페이지 컴포넌트의 프롭으로 사용합니다. 즉, 클라이언트 측 페이지 전환은 내보내진 JSON만 사용하고 `getStaticProps` 자체를 호출하지는 않습니다.

## getStaticProps는 어디에서 사용할 수 있을까

`getStaticProps`는 **페이지**에서만 내보낼 수 있습니다. 페이지가 아닌 파일, `_app`, `_document`, `_error`에서는 내보낼 수 없습니다.

이런 제한이 있는 이유 중 하나는 페이지가 렌더링되기 전에 리액트에 필요한 모든 데이터가 있어야 하기 때문입니다.

또한 `getStaticProps`는 독립된 함수로 내보내야 합니다. `getStaticProps`를 페이지 컴포넌트의 프로퍼티로 추가하면 작동하지 않습니다.

> **참고**
>
> [커스텀 앱](https://nextjs.org/docs/advanced-features/custom-app)을 만든 경우에는 `pageProps`를 링크 문서에 설명된 대로 페이지 컴포넌트에 전달하고 있는지 확인인해야 합니다. 그렇지 않으면 프롭이 비어 있을 것입니다.

## 개발 중에는 모든 요청에 대해 실행됩니다

개발 중(`next dev`)에는 `getStaticProps`가 모든 요청마다 호출됩니다.

## 미리보기 모드

[**미리보기 모드**](https://nextjs.org/docs/advanced-features/preview-mode)를 사용하여 빌드 타임 대신 **요청 타임**에 정적 생성을 우회하고 페이지를 렌더링할 수 있습니다. 예를 들어 헤드리스 CMS를 사용 중이며 초안이 게시되기 전에 미리보기를 원할 수 있습니다.

