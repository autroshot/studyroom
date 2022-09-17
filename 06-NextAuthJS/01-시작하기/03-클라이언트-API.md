# 클라이언트 API

넥스트인증 클라이언트 라이브러리를 사용하면 리액트 앱의 세션과 쉽게 상호 작용할 수 있습니다.

예시 세션 객체:

```json
{
  user: {
    name: string
    email: string
    image: string
  },
  expires: Date // 이것은 세션 내의 토큰이 아닌 세션의 만료입니다.
}
```

> **팁**
>
> 클라이언트에 반환된 세션 데이터에는 세션 토큰 또는 OAuth 토큰과 같은 민감한 정보가 포함되어 있지 않다. 여기에는 설명을 위해 로그인한 사용자에 대한 정보(예: 이름, 이메일, 이미지)를 페이지에 표시하는 데 필요한 데이터가 들어 있는 최소한의 페이로드가 포함됩니다.
>
> 세션 객체에서 추가 데이터를 반환해야 하는 경우 세션 콜백을 사용하여 클라이언트에 반환된 세션 객체를 커스타마이즈할 수 있습니다.

> **참고**
>
> `expires` 값은 순환됩니다. 즉, 레스트 API에서 세션을 검색할 때마다 이 값도 업데이트되어 세션 만료를 방지합니다.

## `useSession()`

- 클라이언트 측 - 예
- 서버 측 - 아니오

넥스트인증 클라이언트의 `useSession()` 리액트 훅은 누군가 로그인했는지 확인하는 가장 쉬운 방법입니다.

[`SessionProvider`](https://next-auth.js.org/getting-started/client#sessionprovider)가 `pages/_app.js`에 추가되었는지 확인해야 합니다.

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

`useSession()`은 다음 두 개의 값을 포함하는 객체를 반환합니다.

- `data` - 세 가지 값이 될 수 있습니다.
  - 세션을 아직 가져오지 않았을 때 `undefined`
  - 세션 검색에 실패한 경우 `null`
  - 성공할 경우 `Session`
- `status` - 가능한 세 가지 세션 상태에 대한 열거형 매핑 `"loading" | "authenticated" | "unauthenticated"`

### 세션 필요

넥스트의 `getServerSideProps`와 `getInitialProps` 작동 방식 때문에, 모든 보호된 페이지 로드는 세션이 유효한지 확인한 이후에 요청된 페이지(SSR)를 생성하기 위해 서버 측 요청을 해야 합니다. 이렇게 하면 서버 부하가 증가하므로 클라이언트 요청에 능숙하다면 대안이 있습니다. `useSession`을 사용해 항상 유효한 세션이 있는지 확인하는 것입니다.

초기 로드 상태 이후에 세션을 찾지 못한 경우 적절한 응답을 할 수 있습니다. 기본 동작은 사용자를 로그인 페이지로 리디렉션하는 것입니다. 여기서 로그인에 성공하면 시작한 페이지로 다시 보내집니다. 다른 작업을 수행하고 싶다면 `onFail()` 콜백을 정의합니다.

예시:

```jsx
// pages/protected.jsx

import { useSession } from "next-auth/react"

export default function Admin() {
  const { status } = useSession({
    required: true,
    onUnauthenticated() {
      // 인증받지 않은 사용자는 이곳에서 처리합니다.
    },
  })

  if (status === "loading") {
    return "Loading or not authenticated..."
  }

  return "User is logged in"
}
```

### 커스텀 클라이언트 세션 처리

넥스트의 `getServerSideProps`와 `getInitialProps` 작동 방식 때문에, 모든 보호된 페이지 로드는 세션이 유효한지 확인한 이후에 요청된 페이지를 생성하기 위해 서버 측 요청을 해야 합니다. 이 대체 솔루션을 사용하면 서버를 확인하고 페이지를 다시 생성할 필요 없이, 초기 확인에서 로드 상태를 표시할 수 있으며 이후의 모든 페이지 전환은 클라이언트 측에서 처리합니다.

```jsx
// pages/admin.jsx

export default function AdminDashboard() {
  const { data: session } = useSession()
  // 이 페이지 내의 리액트 트리에서는 세션이 항상 null이 아닙니다.
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
  // { required: true }가 제공된다면, status는 "loading"이나 "authenticated"만 가능합니다.
  const { status } = useSession({ required: true })

  if (status === "loading") {
    return <div>Loading...</div>
  }

  return children
}
```

페이지에서 역할 기반 인증을 위한 옵션 객체와 같은 것을 지원하도록 쉽게 확장하거나 수정할 수 있습니다.

예시:

```jsx
// pages/admin.jsx

AdminDashboard.auth = {
  role: "admin",
  loading: <AdminLoadingSkeleton />,
  unauthorized: "/login-with-different-user", // 이 url로 리디렉션됩니다.
}
```

`_app`의 로직 덕분에 인증이 필요하지 않은 페이지는 `/api/auth/session` 엔드포인트에 불필요하게 연결되지 않습니다.

자세한 내용은 [깃허브 이슈](https://github.com/nextauthjs/next-auth/issues/1210)에서 확인하세요.

### 넥스트인증과 리액트 쿼리

[리액트 쿼리](https://tanstack.com/query/v4/docs/adapters/react-query) 또는 [SWR](https://swr.vercel.app/) 같은 데이터 가져오기 라이브러리를 사용하여 자신만의 세션 관리 솔루션을 만들 수 있습니다. [`@next-auth/react-query`의 원본 구현](https://github.com/nextauthjs/react-query)을 사용하고 [`next-auth/react` 소스 코드](https://github.com/nextauthjs/next-auth/blob/main/packages/next-auth/src/react/index.tsx)를 시작점으로 삼을 수 있습니다.

## `getSession()`

- 클라이언트 측 - 예
- 서버 측 - 아니오 ([`unstable_getServerSession()`](https://next-auth.js.org/configuration/nextjs#unstable_getserversession) 참고)

넥스트인증은 현재 활성 세션을 반환하기 위해 클라이언트 측에서만 사용해야 하는 `getSession()` 헬퍼를 제공합니다.

서버 측에서 `getSession()`을 여전히 사용할 수는 있지만 앞으로는 `unstable_getServerSession`을 사용하는 것을 권장합니다. 이는 서버 측에서의 불필요한 추가 `fetch` 호출을 피하기 위함입니다. 자세한 내용은 [이슈](https://github.com/nextauthjs/next-auth/issues/1535)를 확인하세요.

> **참고**
>
> API는 향후 변경될 수 있으므로 `unstable_getServerSession`에는 현재 `unstable_` 접두사가 붙어 있습니다. 현재 알려진 버그는 없으며 사용에 문제가 없습니다.

이 헬퍼는 리액트 컨텍스트 외부에서 세션을 읽으려는 경우에 유용합니다.

`getSession()`은 호출되면 `/api/auth/session`에 요청을 보내고 [세션 객체](https://github.com/nextauthjs/next-auth/blob/main/packages/next-auth/src/core/types.ts#L407-L425)가 있는 프라미스를 반환합니다. 세션이 없으면 `null`을 반환합니다.

```jsx
async function myFunction() {
  const session = await getSession()
  /* ... */
}
```

[페이지와 API 경로를 보호하는 방법](https://next-auth.js.org/tutorials/securing-pages-and-api-routes)을 읽고 `unstability_getServerSession()`을 사용해 서버 측 호출에서 세션을 가져오는 방법을 확인하세요.

## `getCsrfToken()`

- 클라이언트 측 - 예
- 서버 측 - 예

`getCsrfToken()` 메서드는 POST 요청(예: 로그인 및 로그아웃)을 만드는 데 필요한 현재 사이트 간 요청 위조 토큰(CSRF 토큰)을 반환합니다.

내장 `signIn()`과 `signOut()` 메서드를 사용하지 않는 경우에만 이것을 사용해야 합니다.

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

## `getProviders()`

- 클라이언트 측 - 예
- 서버 측 - 예

`getProviders()` 메서드는 현재 로그인에 설정된 제공자 목록을 반환합니다.

`/api/auth/providers`를 호출하고 현재 설정된 인증 제공자 목록을 반환합니다.

동적 커스텀 로그인 페이지를 만들 때 유용합니다.

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
> `getCsrfToken()`와 달리, 서버 측에서 `getProviders()`를 호출할 때 클라이언트 측에서 호출하는 것처럼 아무 것도 건네줄 필요가 없습니다.

## `signIn()`

- 클라이언트 측 - 예
- 서버 측 - 아니오

`signIn()` 메서드는 사용자가 로그인 과정을 완료하고 시작한 페이지로 돌아갈 것을 보장합니다. 또한 이메일로 로그인할 때 이 메서드가 자동으로 CSRF 토큰을 처리합니다.

`signIn()` 메서드는 다음과 같이 클라이언트에서 다양한 방식으로 호출할 수 있습니다.

### 클릭 시 로그인 페이지로 리디렉션하기

```jsx
import { signIn } from "next-auth/react"

export default () => <button onClick={() => signIn()}>Sign in</button>
```

### 클릭 시 OAuth 로그인 흐름 시작하기

인수 없이 `signIn()` 메서드를 호출하면 넥스트인증 로그인 페이지로 리디렉션됩니다. 이를 건너뛰고 공급자 페이지로 리디렉션하려면 공급자의 `id`로 `signIn()`을 호출합니다.

다음 예시는 구글 로그인입니다.

```jsx
import { signIn } from "next-auth/react"

export default () => (
  <button onClick={() => signIn("google")}>Sign in with Google</button>
)
```

### 클릭 시 이메일 로그인 흐름 시작하기

이메일 흐름과 함께 사용할 경우 타겟 `email`을 옵션으로 전달합니다.

```jsx
import { signIn } from "next-auth/react"

export default ({ email }) => (
  <button onClick={() => signIn("email", { email })}>Sign in with Email</button>
)
```

### `callbackUrl` 지정하기

`callbackUrl`은 로그인 후 사용자가 리디렉션될 URL을 지정합니다. 기본값은 사용자의 현재 URL입니다.

`signIn()`의 두 번째 인수로 다른 `callbackUrl`을 지정할 수 있습니다. 이것은 모든 제공자에 적용됩니다.

예시:

- `signIn(undefined, { callbackUrl: '/foo' })`
- `signIn('google', { callbackUrl: 'http://localhost:3000/bar' })`
- `signIn('email', { email, callbackUrl: 'http://localhost:3000/foo' })`

URL은 [리디렉션 콜백 핸들러](https://next-auth.js.org/configuration/callbacks#redirect-callback)에서 유효한 것이어야 합니다. 기본적으로 URL은 동일한 호스트 이름의 절대 URL이거나 `/`로 시작하는 상대 URL이어야 합니다. 일치하지 않으면 홈페이지로 리디렉션됩니다. 다른 URL을 허용하도록 고유한 [리디렉션 콜백](https://next-auth.js.org/configuration/callbacks#redirect-callback)을 정의하는 것도 가능합니다.

### `redirect: false` 옵션 사용하기

> **참고**
>
> 리디렉션 옵션은 `credentials`과 `email` 제공자에서만 사용 가능합니다.

경우에 따라 동일한 페이지에서 로그인 응답을 처리하고 기본 리디렉션을 비활성화할 수 있습니다. 예를 들어 오류가 발생하는 경우(예: 사용자가 잘못된 자격 증명을 제공) 동일한 페이지에서 오류를 처리할 수 있습니다. 이를 위해 두 번째 매개변수 객체에 `redirect: false`를 전달합니다.

예시:

- `signIn('credentials', { redirect: false, password: 'password' })`
- `signIn('email', { redirect: false, email: 'bill@fillmurray.com' })`

그러면 `signIn`에서 다음과 같이 이행되는 프라미스를 반환합니다.

```json
{
  /**
   * 오류의 유형에 따라 다른 오류 코드가 될 것입니다.
   */
  error: string | undefined
  /**
   * HTTP 상태 코드로, 발생한 오류의 종류를 나타냅니다.
   */
  status: number
  /**
   * 로그인이 성공했다면 true입니다.
   */
  ok: boolean
  /**
   * 오류가 있었다면 null입니다.
   * 그렇지 않으면 사용자가 리디렉션될 url입니다.
   */
  url: string | null
}
```

### 추가 매개변수

`signIn()`의 세 번째 인수로 `/authorize` 엔드포인트에 추가 매개변수를 전달할 수 있습니다.

몇 가지 아이디어는 [인증 요청 OIDC 사양](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)을 참고합니다. (이것만이 가능한 것은 아니며 모든 매개변수가 전달됨)

예시:

- `signIn("identity-server4", null, { prompt: "login" })` - 항상 사용자에게 재인증을 요청
- `signIn("auth0", null, { login_hint: "info@example.com" })` - 제공자에게 이메일 주소를 알려줌

> **참고**
>
> [`provider.authorizationParams`](https://next-auth.js.org/configuration/providers/oauth#options)로 이 매개변수를 설정하는 것도 가능합니다.

> **참고**
>
> `redirect_uri`, `state` 매개변수는 항상 서버 측에서 재정의됩니다. 

## `signOut()`

- 클라이언트 측 - 예
- 서버 측 - 아니오

`signOut()` 메서드를 사용하면 사용자가 로그아웃 흐름을 완료하고 시작한 페이지로 돌아갈 것을 보장합니다. 또한 이 메서드는 CSRF 토큰을 자동으로 처리합니다.

로그아웃이 완료되면 브라우저에서 페이지를 다시 로드합니다.

```jsx
import { signOut } from "next-auth/react"

export default () => <button onClick={() => signOut()}>Sign out</button>
```

### `callbackUrl` 지정하기

`signIn()` 함수와 동일하게 `callbackUrl` 매개변수를 옵션으로 전달할 수 있습니다.

예시 - `signOut({ callbackUrl: 'http://localhost:3000/foo' })`

URL은 [리디렉션 콜백 핸들러](https://next-auth.js.org/configuration/callbacks#redirect-callback)에서 유효한 것이어야 합니다. 기본적으로 URL은 동일한 호스트 이름의 절대 URL이거나 `/`로 시작하는 상대 URL이어야 합니다. 일치하지 않으면 홈페이지로 리디렉션됩니다. 다른 URL을 허용하도록 고유한 리디렉션 콜백을 정의하는 것도 가능합니다.

### `redirect: false` 옵션 사용하기

`signOut`에 `redirect: false`를 전달하면 페이지가 다시 로드되지 않습니다. 세션이 삭제되고 `useSession` 훅에 알림이 전송되어 사용자와 관련된 모든 것이 자동으로 로그아웃됩니다. 이는 사용자에게 매우 만족스러운 경험을 선사합니다.

> **팁**
>
> 다른 페이지로 리디렉션하면서도 페이지를 다시 로드하지 않으려면 `const data = waitsignOut({redirect: false, callbackUrl: "/foo"})`를 시도해 보세요. 여기서 `data.url`은 넥스트의 `useRouter().push(data.url)`을 사용하여 깜빡임 없이 사용자를 리디렉션할 수 있는 유효한 URL입니다.

## `SessionProvider`

제공된 `<SessionProvider>`를 사용하면 `useSession()`의 인스턴스가 장막 뒤에서 [리액트 컨텍스트](https://reactjs.org/docs/context.html)를 사용해 컴포넌트 간에 세션 객체를 공유합니다. 또한 탭·창 간에 세션을 업데이트하고 동기화되게 관리합니다.

```jsx
// pages/_app.js

import { SessionProvider } from "next-auth/react"

export default function App({
  Component,
  pageProps: { session, ...pageProps },
}) {
  return (
    <SessionProvider session={session}>
      <Component {...pageProps} />
    </SessionProvider>
  )
}
```

위의 예시와 같이 `<SessionProvider>`에 `session` 페이지 프롭을 전달하면 서버 측과 클라이언트 측 렌더링을 모두 지원하는 페이지에서 세션을 두 번 확인하는 것을 피할 수 있습니다.

그러나 이는 올바른 `pageProps`를 건네준 페이지에서만 유효합니다. 보통은 다음과 같이 `getInitialProps`나 `getServerSideProps`에서 개별 페이지 단위로 수행됩니다.

```jsx
// pages/index.js

import { unstable_getServerSession } from "next-auth/next"
import { authOptions } from './api/auth/[...nextauth]'

...

export async function getServerSideProps({ req, res }) {
  return {
    props: {
      session: await unstable_getServerSession(req, res, authOptions)
    }
  }
}
```

`_app`의 `getInitialProps`에서 모든 페이지를 보호할 수 있습니다. 또는 페이지 단위로 이를 수행할 수 있습니다. 또는 [대체 클라이언트 세션 처리](#커스텀-클라이언트-세션-처리)에서 아래의 메서드를 사용해 각 인증 확인(SSR)을 차단하는 대신 페이지별 인증 확인이 가능합니다.

### 옵션

세션 상태는 열려 있는 모든 탭·창에서 자동으로 동기화되며 `refetchOnWindowFocus`가 `true`일 때는 포커스를 얻거나, 잃거나, 상태가 변경될 때(예: 사용자가 로그인 또는 로그아웃)마다 모두 업데이트됩니다.

세션 만료 시간이 30일(기본값) 이상이라면 제공자의 기본 옵션을 변경할 필요가 없습니다. 필요한 경우 클라이언트 측 함수에서 `getSession()`을 호출하여 모든 탭·창에서 세션 객체의 업데이트를 트리거할 수 있습니다.

그러나 세션 동작을 커스터마이즈하고 싶거나 짧은 세션 만료 시간을 원한다면, 해당 옵션을 제공자에게 건네줘서 `useSession()` 훅의 동작을 커스터마이즈합니다.

```jsx
// pages/_app.js

import { SessionProvider } from "next-auth/react"

export default function App({
  Component,
  pageProps: { session, ...pageProps },
}) {
  return (
    <SessionProvider
      session={session}
      // 커스텀 경로를 사용하며 앱이 "/"가 아닌 "/cool-app" 경로에 있는 경우
      basePath="cool-app"
      // 5분마다 세션을 다시 가져온다.
      refetchInterval={5 * 60}
      // 윈도우가 포커스되면 세션을 다시 가져온다.
      refetchOnWindowFocus={true}
    >
      <Component {...pageProps} />
    </SessionProvider>
  )
}
```

> **참고**
>
> 이 옵션은 로그인하지 않은 클라이언트에는 영향을 미치지 않습니다.
>
> 모든 탭·창은 로컬 세션 상태의 자체 복사본을 유지 관리합니다. 세션은 로컬스토리지나 세션스토리지와 같은 공유 저장소에 저장되지 않습니다. 한 탭·창의 업데이트는 다른 탭·창에 대한 메시지를 트리거하여 자체 세션 상태를 업데이트합니다.
>
> `refetchInterval`에 작은 값을 사용하면 인증된 클라이언트의 네트워크 트래픽과 부하가 증가하고 호스팅 비용과 성능에 영향을 미칠 수 있습니다.

#### 기본 경로

커스텀 기본 경로를 사용 중이고 앱 진입점이 도메인의 루트 `/`가 아니라 다른 곳(예: `/my-app/`)인 경우, `basePath` 프롭을 사용하여 넥스트인증이 이를 인식하게 만들고 모든 리디렉션 및 세션 처리가 예상대로 작동되게 합니다.

#### 다시 가져오기 간격

`refetchInterval` 옵션을 사용하면 서버에 연결하여 세션 만료를 막을 수 있습니다.

`refetchInterval`을 `0`(기본값)으로 설정하면 세션 폴링이 없습니다.

클라이언트가 세션 상태를 업데이트하기 위해 서버에 접속해야 하는 빈도를 초 단위로 지정합니다. 다시 가져오기가 트리거될 때 세션 상태가 만료되었다면 열려 있는 모든 탭·창이 이를 반영하도록 업데이트됩니다.

`refetchInterval`의 값은 항상 세션의 `maxAge` [세션 옵션](https://next-auth.js.org/configuration/options#session)의 값보다 작아야 합니다.

#### 창이 포커스되면 다시 가져오기

`refetchOnWindowFocus` 옵션을 사용하여 탭·창에서 포커스를 전환할 때의 세션 상태 자동 업데이트를 설정할 수 있습니다.

`refetchOnWindowFocus`를 `true`(기본값)로 설정하면 탭·창이 포커스를 얻거나 잃을 때 탭·창이 업데이트되고 컴포넌트의 상태가 초기화됩니다.

그러나 `false`로 설정된 경우, 세션 다시 가져오기를 중지하고 컴포넌트는 그대로 유지됩니다.

> **참고**
>
> 넥스트 앱의 `_app.js`에 대한 자세한 내용은 [넥스트 문서](https://nextjs.org/docs/advanced-features/custom-app)를 참고하세요.

### 커스텀 기본 경로

넥스트 앱이 커스텀 기본 경로를 사용하는 경우, 아래 예시와 [이곳](https://next-auth.js.org/configuration/options#nextauth_url)에 설명된 대로 `NEXTAUTH_URL` 환경 변수를 API 엔드포인트의 전체 경로로 설정합니다.

또한 아래 예시와 같이 `<SessionProvider>`에 `basePath` 페이지 프롭을 전달하여 넥스트인증에서 커스텀 기본 경로를 완전히 설정하고 사용되게 합니다.

다음 예시에서 사용된 커스텀 기본 경로는 `/custom-route`입니다.

```
NEXTAUTH_URL=https://example.com/custom-route/api/auth
```

```jsx
// pages/_app.js

import { SessionProvider } from "next-auth/react"
export default function App({
  Component,
  pageProps: { session, ...pageProps },
}) {
  return (
    <SessionProvider session={session} basePath="/custom-route/api/auth">
      <Component {...pageProps} />
    </SessionProvider>
  )
}
```

