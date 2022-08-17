# 클라이언트 API

NextAuth.js 클라이언트 라이브러리를 사용하면 리액트 앱의 세션과 쉽게 상호 작용할 수 있다.

예시 세션 객체

```json
{
  user: {
    name: string
    email: string
    image: string
  },
  expires: Date // 이것은 세션 내의 토큰이 아닌 세션의 만료이다.
}
```

> **팁**
>
> 클라이언트에 반환된 세션 데이터에는 세션 토큰 또는 OAuth 토큰과 같은 민감한 정보가 포함되어 있지 않다. 여기에는 설명을 위해 로그인한 사용자에 대한 정보(예: 이름, 이메일, 이미지)를 페이지에 표시하는 데 필요한 데이터가 들어 있는 최소한의 페이로드가 포함된다.
>
> 세션 객체에서 추가 데이터를 반환해야 하는 경우 세션 콜백을 사용하여 클라이언트에 반환된 세션 객체를 커스타마이징할 수 있다.

> **참고**
>
> `expires` 값은 순환된다. 즉, REST API에서 세션을 검색할 때마다 이 값도 업데이트되어 세션 만료를 방지한다.

## useSession()

- 클라이언트 측 - 예
- 서버 측 - 아니오

NextAuth.js 클라이언트의 `useSession()` 리액트 훅은 누군가 로그인했는지 확인하는 가장 쉬운 방법이다.

[`SessionProvider`](https://next-auth.js.org/getting-started/client#sessionprovider)가 `pages/_app.js`에 추가되었는지 확인해야 한다.

예시:

```jsx
import { useSession } from "next-auth/react"

export default function Component() {
  const { data: session, status } = useSession()

  if (status === "authenticated") {
    return <p>Signed in as {session.user.email}</p>
  }

  return <a href="/api/auth/signin">Sign in</a>
}
```

`useSession()`은 다음 두 개의 값을 포함하는 객체를 반환한다.

- `data` - 세 가지 값이 될 수 있다.
  - 세션을 아직 가져오지 않았을 때 `undefined`
  - 세션 검색에 실패한 경우 `null`
  - 성공할 경우 `Session`
- `status` - 가능한 세 가지 세션 상태에 대한 열거형 매핑 `"loading" | "authenticated" | "unauthenticated"`

### 세션 필요

Next.js가 `getServerSideProps`과 `getInitialProps`를 처리하는 방식 때문에, 모든 보호된 페이지 로드는 세션이 유효한지 확인한 다음 요청된 페이지(SSR)를 생성하기 위해 서버 측 요청을 해야 한다. 이렇게 하면 서버 부하가 증가하므로 클라이언트 요청에 능숙하다면 대안이 있다. `useSession`을 사용해 항상 유효한 세션이 있는지 확인할 수 있다. 초기 로드 상태 이후에 세션을 찾지 못한 경우, 응답할 적절한 행동를 정의할 수 있다.

기본 동작은 사용자를 로그인 페이지로 리다이렉션하는 것이다. 여기서 로그인에 성공하면 시작한 페이지로 다시 보내진다. 다른 작업을 수행하고 싶다면 `onFail()` 콜백을 정의할 수도 있다.

예시:

```jsx
// pages/protected.jsx

import { useSession } from "next-auth/react"

export default function Admin() {
  const { status } = useSession({
    required: true,
    onUnauthenticated() {
      // 인증받지 않은 사용자는 이곳에서 처리한다.
    },
  })

  if (status === "loading") {
    return "Loading or not authenticated..."
  }

  return "User is logged in"
}
```

### 커스텀 클라이언트 세션 처리

Next.js가 `getServerSideProps`과 `getInitialProps`를 처리하는 방식으로 때문에, 모든 보호된 페이지 로드는 세션이 유효한지 확인한 다음 요청된 페이지를 생성하기 위해 서버 측 요청을 해야 한다. 이 대체 솔루션을 사용하면 서버를 확인하고 페이지를 다시 생성할 필요 없이 초기 확인 시 로드 상태를 표시할 수 있으며 이후의 모든 페이지 전환은 클라이언트 측이 된다.

```jsx
// pages/admin.jsx

export default function AdminDashboard() {
  const { data: session } = useSession()
  // 이 페이지 내의 리액트 트리에서는 세션이 항상 Null이 아니다.
  return "Some super secret dashboard"
}

AdminDashboard.auth = true
```

```jsx
// pages/_app.jsx

export default function App({
  Component,
  pageProps: { session, ...pageProps },
}) {
  return (
    <SessionProvider session={session}>
      {Component.auth ? (
        <Auth>
          <Component {...pageProps} />
        </Auth>
      ) : (
        <Component {...pageProps} />
      )}
    </SessionProvider>
  )
}

function Auth({ children }) {
  // { required: true }가 제공된다면, status는 "loading"이나 "authenticated"만 가능하다.
  const { status } = useSession({ required: true })

  if (status === "loading") {
    return <div>Loading...</div>
  }

  return children
}
```

페이지에서 역할 기반 인증을 위한 옵션 객체와 같은 것을 지원하도록 쉽게 확장/수정할 수 있다.

예시:

```jsx
// pages/admin.jsx

AdminDashboard.auth = {
  role: "admin",
  loading: <AdminLoadingSkeleton />,
  unauthorized: "/login-with-different-user", // 이 url로 리다이렉트된다.
}
```

`_app`의 로직 덕분에 인증이 필요하지 않은 페이지는 `/api/auth/session` 엔드포인트에 불필요하게 연결되지 않는다.

자세한 내용은 다음 [깃허브 이슈](https://github.com/nextauthjs/next-auth/issues/1210)에서 확인할 수 있다.

### NextAuth.js + 리액트 쿼리

[React Query](https://tanstack.com/query/v4/docs/adapters/react-query) 또는 [SWR](https://swr.vercel.app/)과 같은 데이터 가져오기 라이브러리를 사용하여 자신만의 세션 관리 솔루션을 만들 수 있다 . [`@next-auth/react-query`의 원본 구현](https://github.com/nextauthjs/react-query)을 사용하고 [`next-auth/react` 소스 코드](https://github.com/nextauthjs/next-auth/blob/main/packages/next-auth/src/react/index.tsx)를 시작점으로 삼을 수 있다.

## getSession()

- 클라이언트 측 - 예
- 서버 측 - 아니오 ([`unstable_getServerSession()`](https://next-auth.js.org/configuration/nextjs#unstable_getserversession) 참고)

NextAuth.js는 현재 활성 세션을 반환하기 위해 클라이언트 측에서만 호출해야 하는 `getSession()` 헬퍼를 제공한다.

서버 측에서는 **이것을 여전히 사용할 수 있지만** 앞으로는 `unstable_getServerSession`을 사용하는 것이 좋다. 이는 서버 측에서의 불필요한 추가 `fetch` 호출을 피하기 위함이다. 자세한 내용은 [이 이슈](https://github.com/nextauthjs/next-auth/issues/1535)를 확인한다.

> **참고**
>
> API는 향후 변경될 수 있으므로 `unstable_getServerSession`에는 현재 `unstable_` 접두사가 있다. 현재 알려진 버그는 없으며 사용하기에 안전하다.

이 헬퍼는 리액트 컨텍스트 외부에서 세션을 읽으려는 경우에 유용하다.

`getSession()`은 호출되면 `/api/auth/session`에 요청을 보내고 [세션 객체](https://github.com/nextauthjs/next-auth/blob/main/packages/next-auth/src/core/types.ts#L407-L425)가 있는 프라미스를 반환한다. 세션이 없으면 `null`을 반환한다.

```jsx
async function myFunction() {
  const session = await getSession()
  /* ... */
}
```

[페이지와 API 경로를 보호하는 방법](https://next-auth.js.org/tutorials/securing-pages-and-api-routes)을 읽고 `unstability_getServerSession()`을 사용해 서버 측 호출에서 세션을 가져오는 방법을 확인한다.

## getCsrfToken()

- 클라이언트 측 - 예
- 서버 측 - 예

`getCsrfToken()` 메서드는 POST 요청(예: 로그인 및 로그아웃)을 만드는 데 필요한 현재 교차 사이트 요청 위조 토큰(CSRF 토큰)을 반환한다.

내장 `signIn()`과 `signOut()` 메서드를 사용하지 않는 경우에만 이것을 사용해야 한다.

클라이언트 측 예시:

```jsx
async function myFunction() {
  const csrfToken = await getCsrfToken()
  /* ... */
}
```

서버 측 예시:

```jsx
import { getCsrfToken } from "next-auth/react"

export default async (req, res) => {
  const csrfToken = await getCsrfToken({ req })
  /* ... */
  res.end()
}
```

## getProviders()

- 클라이언트 측 - 예
- 서버 측 - 예

`getProviders()` 메서드는 현재 로그인에 설정된 제공자 목록을 반환한다.

`/api/auth/providers`를 호출하고 현재 설정된 인증 제공자 목록을 반환한다.

동적 커스텀 로그인 페이지를 만들 때 유용할 수 있다.

API 경로 예시:

```jsx
// pages/api/example.js

import { getProviders } from "next-auth/react"

export default async (req, res) => {
  const providers = await getProviders()
  console.log("Providers", providers)
  res.end()
}
```

> **참고**
>
> `getCsrfToken()`와 달리, 서버 측에서 `getProviders()`를 호출할 때 클라이언트 측에서 호출하는 것처럼 아무 것도 건네줄 필요가 없다.

## signIn()

- 클라이언트 측 - 예
- 서버 측 - 아니오

`signIn()`을 사용하면 사용자가 로그인 과정을 완료한 후 시작한 페이지에서 다시 종료된다. 또한 이메일로 로그인할 때 이 메서드가 자동으로 CSRF 토큰을 처리한다.

`signIn()` 메서드는 다음과 같이 클라이언트에서 다양한 방식으로 호출할 수 있다.

### 클릭 시 로그인 페이지로 리다이렉션하기

```jsx
import { signIn } from "next-auth/react"

export default () => <button onClick={() => signIn()}>Sign in</button>
```

### 클릭 시 OAuth 로그인 흐름 시작하기

기본적으로 인수 없이 `signIn()` 메서드를 호출하면 NextAuth.js 로그인 페이지로 리다이렉션된다. 이를 건너뛰고 즉시 공급자 페이지로 리다이렉션하려면 공급자의 `id`로 `signIn()`을 호출한다.

다음 예시는 구글 로그인이다.

```jsx
import { signIn } from "next-auth/react"

export default () => (
  <button onClick={() => signIn("google")}>Sign in with Google</button>
)
```

### 클릭 시 이메일 로그인 흐름 시작하기

이메일 흐름과 함께 사용할 경우 타겟 `email`을 옵션으로 전달한다.

```jsx
import { signIn } from "next-auth/react"

export default ({ email }) => (
  <button onClick={() => signIn("email", { email })}>Sign in with Email</button>
)
```

### callbackUrl 지정하기

`callbackUrl`은 로그인 후 사용자가 리다이렉션될 URL을 지정한다. 기본값은 사용자의 현재 URL이다.

`signIn()`의 두 번째 인수로 지정하여 다른 `callbackUrl`을 지정할 수 있다. 이것은 모든 제공자에 적용된다.

예시:

- `signIn(undefined, { callbackUrl: '/foo' })`
- `signIn('google', { callbackUrl: 'http://localhost:3000/bar' })`
- `signIn('email', { email, callbackUrl: 'http://localhost:3000/foo' })`

URL은 [리다이렉션 콜백 핸들러](https://next-auth.js.org/configuration/callbacks#redirect-callback)에서 유효한 것이어야 한다. 기본적으로 URL은 동일한 호스트 이름의 절대 URL이거나 슬래시로 시작하는 상대 URL이어야 한다. 일치하지 않으면 홈페이지로 리다이렉션된다. 다른 URL을 허용하도록 고유한 리다이렉션 콜백을 정의할 수 있다 .

### redirect: false 옵션 사용하기

> **참고**
>
> 리다이렉션 옵션은 `credentials`과 `email` 제공자에서만 사용 가능하다.

경우에 따라 동일한 페이지에서 로그인 응답을 처리하고 기본 리다이렉션을 비활성화할 수 있다. 예를 들어 오류가 발생하는 경우(예: 사용자가 제공한 잘못된 자격 증명) 동일한 페이지에서 오류를 처리할 수 있다. 이를 위해 두 번째 매개변수 객체 `redirect: false`를 전달할 수 있다.

예시:

- `signIn('credentials', { redirect: false, password: 'password' })`
- `signIn('email', { redirect: false, email: 'bill@fillmurray.com' })`

그러면 `signIn`에서 다음과 같이 이행되는 프라미스을 반환한다.

```json
{
  /**
   * 오류의 유형에 따라 다른 오류 코드가 될 것이다.
   */
  error: string | undefined
  /**
   * HTTP 상태 코드,
   * 발생한 오류의 종류를 나타낸다.
   */
  status: number
  /**
   * 로그인이 성공했다면 true이다.
   */
  ok: boolean
  /**
   * 오류가 있었다면 `null` 이다.
   * 그렇지 않으면 사용자가 리다이렉션될 url이다.
   */
  url: string | null
}
```

### 추가 매개변수

`signIn()`의 세 번째 인수를 통해 `/authorize` 엔드포인트에 추가 매개변수를 전달할 수도 있다.

몇 가지 아이디어는 [인증 요청 OIDC 사양](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)을 참고한다. (이것만이 가능한 것은 아니며 모든 매개변수가 전달됨)

예시:

- `signIn("identity-server4", null, { prompt: "login" })` - 항상 사용자에게 재인증을 요청
- `signIn("auth0", null, { login_hint: "info@example.com" })` - 제공자에게 이메일 주소를 알려줌

> **참고**
>
> [`provider.authorizationParams`](https://next-auth.js.org/configuration/providers/oauth#options)를 통해 이러한 매개변수를 설정하는 것도 가능하다.

> **참고**
>
> `redirect_uri`, `state` 매개변수는 항상 서버 측에서 재정의된다. 

## signOut()

- 클라이언트 측 - 예
- 서버 측 - 아니오

로그아웃하려면 `signOut()` 메서드를 사용하여 사용자가 로그아웃 흐름을 완료한 후 시작한 페이지에서 다시 종료하도록 한다. 또한 이 메서드는 CSRF 토큰을 자동으로 처리한다.

로그아웃이 완료되면 브라우저에서 페이지를 다시 로드한다.

```jsx
import { signOut } from "next-auth/react"

export default () => <button onClick={() => signOut()}>Sign out</button>
```

