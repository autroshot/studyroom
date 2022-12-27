# REST API

넥스트인증은 넥스트인증 클라이언트에서 사용하는 레스트 API를 노출합니다.

#### `GET` `/api/auth/signin`

내장, 비브랜드 로그인 페이지를 표시합니다.

#### `POST` `/api/auth/signin/:provider`

제공자별 로그인 흐름을 시작합니다.

POST 제출에는 `/api/auth/csrf`의 CSRF 토큰이 필요합니다.

OAuth 제공자의 경우, 이 엔드포인트를 호출하면 ID 제공자에 대한 권한 부여 요청이 시작됩니다. [OAuth 사양](https://datatracker.ietf.org/doc/html/rfc6749#section-4.1.1)에서 자세한 내용을 확인하세요.

이메일 제공자를 사용하는 경우, 이 엔드포인트를 호출하면 사용자의 이메일 주소로 로그인 URL이 전송됩니다.

`signIn` 메서드에서도 이 엔드포인트를 내부적으로 사용합니다.

#### `GET`/`POST` `/api/auth/callback/:provider`

로그인하는 동안 OAuth 서비스의 반환 요청을 처리합니다.

`checks: ["state"]` 옵션을 지원하는 OAuth 2.0 제공자의 경우 로그인 흐름이 시작될 때 생성된 것과 비교하여 상태 매개변수를 확인합니다. 이는 로그인 동안 `POST` 및 `GET` 호출에서 모두 일치해야 하는 CSRF 토큰의 해시를 사용합니다.

[OAuth 사양](https://datatracker.ietf.org/doc/html/rfc6749#section-4.1.2)에서 자세한 내용을 확인하세요.

#### `GET` `/api/auth/signout`

내장, 비브랜드 로그아웃 페이지를 표시합니다.

#### `POST` `/api/auth/signout`

사용자 로그아웃 처리합니다. 이는 악의적인 링크가 동의 없이 사용자를 로그아웃시키는 것을 방지하기 위한 `POST` 제출입니다. [세션을 저장](https://next-auth.js.org/configuration/options#session)할 때 선택한 흐름에 따라 사용자 세션이 쿠키나 데이터베이스에서 무효화 또는 제거됩니다.

`POST` 제출에는 `/api/auth/csrf`의 CSRF 토큰이 필요합니다.

`signOut` 메서드에서도 이 엔드포인트를 내부적으로 사용합니다.

#### `GET` `/api/auth/session`

클라이언트에 안전한 세션 객체를 반환하거나 세션이 없는 경우 빈 객체를 반환합니다.

반환되는 세션 객체의 내용은 [`session` 콜백](https://next-auth.js.org/configuration/callbacks#session-callback)으로 설정할 수 있습니다.

#### `GET` `/api/auth/csrf`

CSRF 토큰을 포함된 객체를 반환합니다. 넥스트인증에서 CSRF 보호는 모든 인증 경로에 있습니다. 서명된 HttpOnly, 호스트 전용 쿠키를 사용하는 '이중 제출 쿠키 방식'을 사용합니다.

이 엔드포인트에서 반환된 CSRF 토큰은 모든 API 엔드포인트에 대한 모든 `POST` 제출에 `csrfToken`이라는 폼 변수로 전달되어야 합니다.

#### `GET` `/api/auth/providers`

설정된 OAuth 서비스 목록과 각 서비스의 세부 정보(예: 로그인 및 콜백 URL)를 반환합니다.

커스텀 가입 페이지를 동적으로 생성하고 각 OAuth 제공자의 콜백 URL을 확인할 때 유용합니다.

> **참고**
>
> 기본 경로는 `/api/auth`이며 `NEXTAUTH_URL`에서 커스텀 경로를 지정할 수 있습니다.
>
> 예시:
>
> `NEXTAUTH_URL=https://example.com/myapp/api/authentication`
> `/api/auth/signin` → `/myapp/api/authentication/signin`