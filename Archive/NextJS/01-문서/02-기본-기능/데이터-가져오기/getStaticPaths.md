# getStaticPaths

페이지에 [동적 경로](../../03-라우팅/02-동적-경로.md)가 있고 `getStaticProps`를 사용하는 경우 정적으로 생성할 경로 목록을 정의해야 합니다.

동적 경로를 사용하는 페이지에서 `getStaticPaths`(정적 사이트 생성)라는 함수를 내보낼 때, 넥스트는 `getStaticPaths`에서 지정한 모든 경로를 정적으로 미리 렌더링합니다.

```jsx
// pages/posts/[id].js

// /posts/1과 /posts/2을 생성합니다.
export async function getStaticPaths() {
  return {
    paths: [{ params: { id: '1' } }, { params: { id: '2' } }],
    fallback: false, // true나 'blocking'이 올 수 있습니다.
  }
}

// getStaticPaths는 getStaticProps을 필요로 합니다.
export async function getStaticProps(context) {
  return {
    // 페이지 컴포넌트에 프랍으로 전달됩니다.
    props: { post: {} },
  }
}

export default function Post({ post }) {
  // post를 렌더링합니다...
}
```

[`getStaticPaths` API 레퍼런스](https://nextjs.org/docs/api-reference/data-fetching/get-static-paths)는 `getStaticPaths`에서 사용할 수 있는 모든 매개변수와 프랍을 다룹니다.

## getStaticPaths는 언제 사용해야 할까

`getStaticPaths`는 동적 경로와 다음을 사용하는 페이지를 정적으로 사전 렌더링하는 경우에 사용해야 합니다.

- 데이터를 헤드리스 CMS에서 가져옴
- 데이터를 데이터베이스에서 가져옴
- 데이터를 파일 시스템에서 가져옴
- 데이터를 공개적으로 캐시할 수 있음(사용자 데이터가 아님)
- 페이지는 SEO를 위해 사전 렌더링되어야 하며 매우 빨라야 함. `getStaticProps`는 `HTML`과 `JSON`파일을 생성하며, 둘 다 성능을 위해 CDN에 캐시될 수 있음

## getStaticPaths는 언제 실행될까

`getStaticPaths`는 프로덕션에서 빌드하는 동안에만 실행되며 런타임에는 호출되지 않습니다. 이 [도구](https://next-code-elimination.vercel.app/)를 사용하여 `getStaticPaths` 내부에 작성된 코드가 클라이언트 측 번들에서 제거되었는지 확인할 수 있습니다 .

### getStaticProps는 getStaticPaths와 관련하여 어떻게 실행될까

- `getStaticProps`는 빌드 중에 반환된 모든 `paths`에 대해 `next build` 동안 실행됨
- `getStaticProps`는 `fallback: true`를 사용할 때 백그라운드에서 실행됨
- `getStaticProps`는 `fallback: blocking`을 사용할 때 초기 렌더링 전에 호출됨

## getStaticPaths는 어디에서 사용할 수 있을까

- `getStaticPaths`는 반드시 `getStaticProps`와 함께 사용해야 함
- `getStaticPaths`는 [`getServerSideProps`](https://nextjs.org/docs/basic-features/data-fetching/get-server-side-props)와 함께 사용할 수 없음
- `getStaticProps`도 사용하는 [동적 경로](../../02-라우팅/02-동적-경로)에서 `getStaticPaths`를 내보낼 수 있음
- 페이지가 아닌 파일(예: `components` 폴더)에서 `getStaticPaths`를 내보낼 수 없음
- `getStaticPaths`를 페이지 컴포넌트의 프로퍼티가 아닌 독립된 함수로 내보내야 함

## 개발 중에는 모든 요청에 대해 실행됩니다

개발 중(`next dev`)에는 `getStaticPaths`가 모든 요청마다 호출됩니다.

## 주문형 경로 생성하기

`getStaticProps`를 사용하면 [`fallback`](https://nextjs.org/docs/api-reference/data-fetching/get-static-paths#fallback-blocking)을 사용하는 주문형 대신 빌드 중에 어떤 페이지를 생성할지 제어할 수 있습니다. 빌드 중에 많은 페이지를 생성하면 빌드가 느려집니다.

빈 배열로 된 `paths`를 반환하여 주문형으로 모든 페이지 생성을 연기할 수 있습니다. 이는 넥스트 앱을 여러 환경에 배포할 때 특히 유용할 수 있습니다.

예를 들어 미리 보기(프로덕션 빌드는 제외)를 위해 주문형으로 모든 페이지를 생성하여 빌드 속도를 높일 수 있습니다. 이것은 수백에서 수천 개의 정적 페이지가 있는 사이트에 유용합니다.

```jsx
// pages/posts/[id].js

export async function getStaticPaths() {
  // 이것이 true이면(미리보기 환경에서는 true임)
  // 정적 페이지를 사전 렌더링하지 않습니다.
  // 빌드가 빨라지고 첫 페이지 로딩이 느려집니다.
  if (process.env.SKIP_BUILD_STATIC_GENERATION) {
    return {
      paths: [],
      fallback: 'blocking',
    }
  }

  // posts를 얻기 위해 외부 API 엔드포인트를 호출합니다.
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // posts에 기반해 사전 렌더링하고 싶은 paths을 얻습니다.
  // 프로덕션 환경에서는 모든 페이지를 사전 렌더링합니다.
  // 빌드는 느려지고 첫 페이지 로딩이 빨라집니다.
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))

  // { fallback: false }는 paths 이외의 경로가 404가 된다는 것을 의미합니다.
  return { paths, fallback: false }
}
```

