# getServerSideProps

페이지에서 `getServerSideProps`(SSR)이라는 함수를 내보내면, Next.js는 `getServerSideProps`에서 반환된 데이터를 사용하여 각 요청에서 이 페이지를 미리 렌더링한다.

```jsx
export async function getServerSideProps(context) {
  return {
    props: {}, // 페이지 컴포넌트에 props로 전달된다.
  }
}
```

> 렌더링 방식에 관계없이 모든 `props`는 페이지 컴포넌트로 전달되고 초기 HTML의 클라이언트 측에서 볼 수 있다. 이것은 페이지의 올바른 수화를 위한 것이다. 클라이언트에 있으면 안 되는 민감한 정보는 `props`로 전달하면 안 된다.

## getServerSideProps는 언제 실행될까

`getServerSideProps`는 서버 측에서만 실행되고 브라우저에서는 실행되지 않는다. 페이지에서 `getServerSideProps`를 사용하면 다음의 작업이 수행된다.

- 해당 페이지를 직접 요청하면 `getServerSideProps`가 요청 타임에서 실행된다. 해당 페이지는 반환된 props로 미리 렌더링된다.
- [`next/link`](https://nextjs.org/docs/api-reference/next/link)나 [`next/router`](https://nextjs.org/docs/api-reference/next/router)를 통해 클라이언트 측 페이지 전환에서 해당 페이지를 요청하면, Next.js가 `getServerSideProps`을 실행하는 API 요청을 서버에 보낸다.

`getServerSideProps`는 페이지를 렌더링하는 데 사용할 JSON을 반환한다. 이 모든 작업은 Next.js에서 자동으로 처리되므로 `getServerSideProps` 정의 외에는 추가 작업을 수행할 필요가 없다.

[next-code-elimination 도구](https://next-code-elimination.vercel.app/)를 사용하여 Next.js가 클라이언트 측 번들에서 제거하는 것들을 확인할 수 있다.

`getServerSideProps`는 페이지에서만 내보낼 수 있다. 페이지가 아닌 파일에서는 내보낼 수 없다.

`getServerSideProps`를 독립된 함수로 내보내야 한다는 점을 유의해야 한다. `getServerSideProps`를 페이지 컴포넌트의 프로퍼티로 추가하면 작동하지 않는다.

[`getServerSideProps` API 레퍼런스](https://nextjs.org/docs/api-reference/data-fetching/get-server-side-props)는 `getServerSideProps`에서 사용할 수 있는 모든 매개변수와 props를 다룬다.

## getServerSideProps는 언제 사용해야 할까

요청 타임에서 데이터를 가져와야 하는 페이지를 렌더링해야 하는 경우에만 `getServerSideProps`를 사용한다. 이는 요청의 데이터나 프로퍼티의 근본적 특성 때문일 수 있다. 그 예로는 `authorization` 헤더 또는 지리적 위치가 있다. `getServerSideProps`를 사용하는 페이지는 요청 타임에서 서버 측에서 렌더링되며 [캐시 제어 헤더가 설정된](https://nextjs.org/docs/going-to-production#caching) 경우에만 캐시된다.

요청에서 데이터를 렌더링할 필요가 없는 경우에는 [클라이언트 측](https://nextjs.org/docs/basic-features/data-fetching/get-server-side-props#fetching-data-on-the-client-side)이나 [`getStaticProps`](https://nextjs.org/docs/basic-features/data-fetching/get-static-props)에서 데이터를 가져오는 것을 고려할 필요가 있다.

### getServerSideProps 또는 경로 API

서버에서 데이터를 가져올 때 [경로 API](https://nextjs.org/docs/api-routes/introduction)를 사용하고 싶다면 `getServerSideProps`에서 해당 경로 API를 호출하면 된다. 하지만 이는 `getServerSideProps`와 서버에서 실행되는 경로 API를 둘 다 요청하는 불필요하고 비효율적인 접근 방식이다.

그 이유는 다음과 같다. 경로 API는 CMS에서 일부 데이터를 가져오는 데 사용된다. 그러면 해당 경로 API가 `getServerSideProps`에서 직접 호출된다. 이 방식은 추가 호출이 발생하여 성능이 저하된다.

대신 경로 API 내부에서 사용되는 로직을 `getServerSideProps`로 직접 옮기는 것이 좋다. 이는 `getServerSideProps` 내부에서 직접 CMS, 데이터베이스, 기타 API를 호출하는 것을 의미할 수 있다.

## 클라이언트 측에서 데이터 가져오기

페이지에 자주 업데이트되는 데이터가 포함되어 있고 데이터를 미리 렌더링할 필요가 없는 경우에는 [클라이언트 측](https://nextjs.org/docs/basic-features/data-fetching/client-side)에서 데이터를 가져올 수 있다. 이에 대한 예로는 사용자별 데이터가 있다.

- 먼저 데이터가 없는 페이지를 즉시 표시한다. 페이지의 일부는 정적 생성을 사용하여 미리 렌더링할 수 있다. 누락된 데이터에 대한 로딩 상태를 표시할 수 있다.
- 그런 다음 클라이언트 측에서 데이터를 가져와 준비가 되면 표시한다.

이 접근 방식은 사용자 대시보드 페이지에 적합하다. 대시보드는 사용자별 비공개 페이지이므로 SEO는 관련이 없으며 페이지를 미리 렌더링할 필요가 없다. 데이터가 자주 업데이트되므로 요청 타임에서 데이터 가져오기를 해야 한다.

## getServerSideProps를 사용하여 요청 타임에서 데이터 가져오기

다음 예시는 요청 타임에서 데이터를 가져오고 결과를 미리 렌더링하는 방법을 보여준다.

```jsx
function Page({ data }) {
  // data를 렌더링한다...
}

// 이 함수는 매 요청마다 호출된다.
export async function getServerSideProps() {
  // 외부 API에서 data를 가져온다.
  const res = await fetch(`https://.../data`)
  const data = await res.json()

  // data를 페이지에 props로 전달한다.
  return { props: { data } }
}

export default Page
```

## SSR을 사용한 캐싱

`getServerSideProps` 내부의 캐싱 헤더(`Cache-Control`)를 사용하여 동적 응답을 캐시할 수 있다. 예를 들어 [`stale-while-revalidate`](https://web.dev/stale-while-revalidate/)을 이용할 수 있다.

```jsx
// 이 값은 10초 동안 신선하다. (s-maxage=10)
// 요청이 다음 10초 이내에 반복되면, 이전 캐시 값은 여전히 신선할 것이다.
// 요청이 59초 이전에 반복되면 캐시 값은 오래되었지만 렌더링된다.
//
// 백그라운드에서는 캐시를 새 값으로 채우는 재검증 요청이 수행된다.
// 페이지를 새로고침하면 새 값을 보게 된다.
export async function getServerSideProps({ req, res }) {
  res.setHeader(
    'Cache-Control',
    'public, s-maxage=10, stale-while-revalidate=59'
  )

  return {
    props: {},
  }
}
```

[캐싱](https://nextjs.org/docs/going-to-production#caching)에서 자세한 내용을 확인한다.

## getServerSideProps가 오류 페이지를 렌더링할까

`getServerSideProps` 내부에서 오류가 발생하면 `pages/500.js` 파일이 표시된다. 해당 파일을 만드는 방법은 [500 페이지](https://nextjs.org/docs/advanced-features/custom-error-page#500-page) 문서를 확인한다. 개발 중에는 이 파일이 사용되지 않고 대신 개발 오버레이가 표시된다.