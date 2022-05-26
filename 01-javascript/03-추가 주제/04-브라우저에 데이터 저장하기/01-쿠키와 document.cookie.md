# 쿠키와 document.cookie

쿠키는 브라우저에 저장되는 작은 크기의 문자열로 HTTP 프로토콜의 일부이다.

쿠키는 일반적으로 웹 서버에서 응답의 `Set-Cookie` HTTP 헤더를 사용해 만들어진다. 그 다음에 브라우저는 자동으로 `Cookie` HTTP 헤더를 이용해, 동일한 도메인에 대한 모든 요청에 쿠키의 내용을 추가한다.

쿠키는 클라이언트 식별과 같은 인증에 가장 많이 쓰인다.

1. 사용자가 로그인하면 서버는 HTTP 응답 헤더의 `Set-Cookie`에 담긴 '세션 식별자(session identifier)' 정보를 사용해 쿠키를 설정한다.
2. 사용자가 동일 도메인에 접속하려고 하면 브라우저는 HTTP `Cookie` 헤더에 인증 정보가 담긴 고윳값(세션 식별자)을 함께 실어 서버에 요청을 보낸다.
3. 서버는 브라우저가 보낸 요청 헤더의 세션 식별자를 읽어 사용자를 식별한다.

`document.cookie` 프로퍼티를 이용하면 브라우저에서도 쿠키에 접근할 수 있다.

## 쿠키 읽기

```js
// 쿠키를 확인한다.
alert(document.cookie); // cookie1=value1; cookie2=value2;...
```

`document.cookie`는 `name=value` 쌍으로 구성되어 있고, 각 쌍은 `;`로 구분한다. 이때 쌍 하나는 하나의 독립된 쿠키를 나타낸다.

`;`을 기준으로 `document.cookie`의 값을 분리하면 원하는 쿠키를 찾을 수 있다. 정규 표현식이나 배열 관련 함수를 사용하면 된다.

## 쿠키 쓰기

`document.cookie`에 직접 값을 쓸 수 있다. 이때 `cookie`는 데이터 프로퍼티가 아닌 접근자(accessor) 프로퍼티이다. 접근자 프로퍼티에 대한 자세한 내용은 [프로퍼티 getter와 setter](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/07-%EA%B0%9D%EC%B2%B4%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%20%EC%84%A4%EC%A0%95/02-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%20getter%EC%99%80%20setter.md#%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-getter%EC%99%80-setter) 챕터에서 확인 가능하다.

`document.cookie`에 값을 할당하면, 브라우저는 이 값을 받아 해당 쿠키를 갱신한다. 이때 다른 쿠키의 값은 변경되지 않는다.

다음과 같이 코드를 작성하면 이름이 `user`인 쿠키를 찾아 그 값을 `John`으로 갱신한다.

```js run
document.cookie = "user=John"; // 이름이 'user'인 쿠키의 값만 갱신함 
alert(document.cookie); // 모든 쿠키 보여주기
```

코드를 실행하면 여러 개의 쿠키가 출력되는 것을 확인할 수 있습니다. 이를 통해 `document.cookie=` 연산은 모든 쿠키를 덮어쓰지 않고, 명시된 쿠키인 `user`의 값만 갱신한 것을 알 수 있습니다.

쿠키의 이름과 값에는 모든 글자가 허용된다. 하지만 형식의 유효성을 유지하기 위해 반드시 내장 함수 `encodeURIComponent`를 사용해 이름과 값을 이스케이프 처리해야 한다.

```js
// 특수 값(공백)은 인코딩 처리해야 한다.
let name = 'my name';
let value = 'John Smith';

// 인코딩 처리 후, 쿠키를 변경한다.
document.cookie = encodeURIComponent(name) + '=' + encodeURIComponent(value);

alert(document.cookie); // ...; my%20name=John%20Smith
```

---
:warning: **쿠키의 한계**

쿠키에는 몇 가지 제약 사항이 있다.
- `encodeURIComponent`로 인코딩한 이후의 `name=value` 쌍은 4KB를 넘을 수 없다. 이 용량을 넘는 정보는 쿠키에 저장할 수 없다.
- 도메인 하나당 저장할 수 있는 쿠키의 개수는 20여 개 정도로 한정되어 있다. 개수는 브라우저에 따라 조금씩 다르다.

---

쿠키에는 여러 옵션이 있다. 그 중 많은 옵션은 중요하며 반드시 지정해야 한다.

옵션은 `key=value` 뒤에 나열하고 `;`로 구분한다.

예시:
```js
document.cookie = "user=John; path=/; expires=Tue, 19 Jan 2038 03:14:07 GMT"
```

## path

- **`path=/mypath`**

URL path(경로)의 접두사는 절대 위치여야 한다. 이 경로 아래에 있는 페이지만 쿠키에 접근할 수 있다. 기본값은 현재 경로이다.

예를 들어 `path=/admin`을 설정한 쿠키는 `/admin`과 `/admin/something`에서 볼 수 있고, `/home` 이나 `/adminpage`에서는 볼 수 없다.

보통은 `path` 옵션을 `path=/`같이 루트로 설정해 웹사이트의 모든 페이지에서 쿠키에 접근할 수 있게 한다.

## domain

- **`domain=site.com`**

쿠키에 접근 가능한 도메인을 지정한다. 다만 몇 가지 제약이 있어서 아무 도메인이나 지정할 수는 없다.

**다른 2단계 도메인에서 쿠키에 접근할 수 있게 만드는 방법은 없다.** `other.com`에서는 `site.com`에서 만든 쿠키를  절대 받을 수 없다.

쿠키에는 민감한 데이터가 저장될 수 있으므로 쿠키에 대한 접근은 동일한 사이트에서만 가능하다.

쿠키를 설정한 하나의 도메인에서만 접근 가능한 것이 기본값이다.

또한 `forum.site.com`와 같은 서브 도메인에서도 접근 불가능한 것이 기본값이다.

```js
// site.com에서 쿠키를 설정한다.
document.cookie = 'user=John'

// site.com의 서브도메인인 forum.site.com에서 user 쿠키에 접근하려 한다.
alert(document.cookie); // 찾을 수 없다.
```

서브 도메인에서도 쿠키를 얻을 수 있게 하려면 `site.com`에서 쿠키를 설정할 때 `domain` 옵션에 루트 도메인 `site.com`을 명시하면 된다.

```js
// site.com에서
// 어떤 서브 도메인(*.site.com)에서든 쿠키에 접근할 수 있게 설정한다.
document.cookie = 'user=John; domain=site.com';

// 이제 forum.site.com과 같은 서브도메인에서도 쿠키 정보를 얻을 수 있다.
alert(document.cookie); // user=John 쿠키를 확인할 수 있다.
```

하위 호환성을 위해 주소 앞에 점을 붙인 `domain=.site.com`도 `domain=site.com`과 동일하게 작동한다. 오래된 표기법이지만 구식 브라우저를 지원하려면 이 표기법을 사용해야 한다.

## expires와 max-age

expires(유효 일자)나 max-age(만료 기간) 옵션이 지정되어 있지 않으면, 브라우저가 닫힐 때 쿠키도 함께 삭제된다. 이런 쿠키를 '세션 쿠키(session cookie)'라고 부른다.

`expires` 나 `max-age` 옵션을 설정하면 브라우저를 닫아도 쿠키가 삭제되지 않는다.

- **`expires=Tue, 19 Jan 2038 03:14:07 GMT`**

브라우저는 해당 유효 일자까지 쿠키를 유지하다가, 해당 일자가 되면 쿠키를 자동으로 삭제한다.

쿠키의 유효 일자는 반드시 GMT(Greenwich Mean Time) 포맷으로 설정해야 한다. `date.toUTCString`을 사용하면 해당 포맷으로 쉽게 변경할 수 있다. 다음은 유효 기간이 하루인 쿠키를 만드는 예시이다.

```js
// 지금으로부터 하루 후이다.
let date = new Date(Date.now() + 86400e3);
date = date.toUTCString();
document.cookie = 'user=John; expires=' + date;
```

`expires` 값을 과거로 지정하면 쿠키는 바로 삭제된다.

-  **`max-age=3600`**

`max-age`는 `expires`의 대안으로, 쿠키 만료 기간을 초 단위로 설정한다.

`0`이나 음수값으로 설정하면 쿠키는 바로 삭제된다.

```js
// 1시간 뒤에 쿠키가 삭제된다.
document.cookie = 'user=John; max-age=3600';

// 만료 기간을 0으로 지정하여 쿠키를 바로 삭제한다.
document.cookie = 'user=John; max-age=0';
```

## secure

- **`secure`**

쿠키는 반드시 HTTPS를 통해서만 전송되어야 한다.

**기본적으로 `http://site.com`에서 설정한 쿠키는 `https://site.com`에도 볼 수 있으며 그 반대도 동일하다.**

쿠키는 도메인 기반이며 프로토콜을 구분하지 않는다.

하지만 `secure` 옵션이 설정된 경우, `https://site.com`에서 설정한 쿠키는 `http://site.com`에서 접근할 수 없다. 쿠키에 민감한 내용이 저장되어 있어 암호화되지 않은 HTTP 연결을 통해 전달되는 것을 원하지 않는다면 이 옵션을 사용하면 된다.

```js
// (https://에서 통신하고 있다고 가정한다)
// 설정한 쿠키는 HTTPS 통신에서만 접근할 수 있다.
document.cookie = "user=John; secure";
```

## samesite

또 다른 보안 속성인 `samesite` 옵션은 크로스 사이트 요청 위조(cross-site request forgery, XSRF) 공격을 막기 위해 만들어진 옵션이다.

쿠키의 `samesite` 옵션을 이용하면 'XSRF 보호 토큰' 없이도 크로스 사이트 요청 위조를 막을 수 있다. XSRF 보호 토큰에 대한 자세한 내용은 [원문](https://ko.javascript.info/cookie#ref-997)에서 확인 가능하다.

이 옵션에 가능한 값은 다음 두 가지이다.

- **`samesite=strict`(값을 설정하지 않고 그냥 `samesite` 옵션만 써줘도 동일하게 동작함)**

사용자가 사이트 외부에서 요청을 보낼 때, `samesite=strict` 옵션이 있는 쿠키는 절대로 전송되지 않는다. 

하지만 다음과 같은 약간의 불편함도 감수해야 한다.

만약 사용자가 메모장 등에 `bank.com`에 요청을 보낼 수 있는 링크를 기록해 놓았다가 이 링크를 클릭해 접속하면 `bank.com`이 사용자를 인식하지 못하는 상황이 발생한다. 실제로 이런 경우 `samesite=strict` 옵션이 설정된 쿠키는 전송되지 않습니다.

이런 문제는 쿠키 두 개를 함께 사용해 해결할 수 있습니다. "Hello, John"과 같은 환영 메시지를 출력해주는 일반 인증(general recognition)용 쿠키, 데이터 교환 시 사용하는 `samesite=strict` 옵션이 있는 쿠키를 따로 두는 것이다. 이렇게 하면 사이트 외부에서 오는 사람에게 환영 메시지는 정상적으로 표시되고, 두 번째 쿠키는 은행 웹사이트에서 지불을 시작해야 만들어진다.

- **`samesite=lax`**

`samesite=lax`는 사용자 경험을 해치지 않으면서 XSRF 공격을 막을 수 있는 느슨한 접근법이다.

`strict`와 마찬가지로 `lax`도 사이트 외부에서 요청을 보낼 때 브라우저가 쿠키를 보내는 것을 막아준다. 하지만 예외 사항이 존재한다. 

다음 두 조건을 동시에 만족할 때는 쿠키가 전송된다.
1. '안전한' HTTP 메서드인 경우(예: GET). 

    안전한 HTTP 메서드 목록은 [RFC7231 명세](https://tools.ietf.org/html/rfc7231)에서 확인할 수 있다. 안전한 메서드는 읽기 작업만 수행하고 쓰기나 데이터 교환 작업은 수행하지 않는다. 참고로 링크를 따라가는 행위는 항상 GET으로 안전한 메서드이다.
    
2. 작업이 최상위 레벨 탐색에서 이루어질 때(예: 브라우저 주소창에서 URL을 변경).

    대다수의 작업은 이 조건을 충족한다. `<iframe>` 안에서의 탐색은 최상위 레벨 탐색이 아니다. AJAX 요청 또한 탐색 행위가 아니므로 이 조건을 충족하지 못한다.

가장 흔한 작업인 '해당 URL로 이동하기'는 조건을 충족하므로 쿠키가 전송된다.

하지만 외부 사이트에서 AJAX 요청을 보내거나 폼을 전송하는 등의 복잡한 작업을 시도할 때는 쿠키가 전송되지 않는다.

이런 제약사항이 괜찮다면, `samesite=lax` 옵션은 사용자 경험을 해치지 않으면서 보안을 강화해주는 좋은 방법이 될 수 있다.

`samesite`는 훌륭한 옵션이지만 한가지 단점이 존재한다. 오래된 브라우저(2017년 이전 버전)에서는 지원되지 않는다는 것이다. 따라서 `samesite`에만 의존한다면 구식 브라우저에서 문제가 생길 것이다.

하지만 여전히 `samesite` 옵션을 XSRF 토큰 같은 다른 보안 기법과 함께 사용하면 보안을 충분히 강화할 수 있다.

## httpOnly

이 옵션은 자바스크립트와 전혀 관계가 없지만, 튜토리얼의 완성도를 위해 알아볼 것이다.

`httpOnly` 옵션은 웹서버에서 `Set-Cookie` 헤더를 이용해 쿠키를 설정할 때 지정할 수 있다.

이 옵션은 자바스크립트가 쿠키를 사용할 수 없게 한다. `document.cookie`를 통해 쿠키를 보거나 조작할 수 없게 된다.

해커가 악의적인 자바스크립트 코드를 페이지에 삽입하고 사용자가 그 페이지에 접속하기를 기다리는 방식의 공격을 막고 싶을 때 이 옵션을 사용한다.

`httpOnly` 옵션이 설정된 쿠키는 `document.cookie`로 쿠키 정보를 읽을 수 없기 때문에 쿠키를 보호할 수 있다.

## 부록: 쿠키 함수

쿠키를 다룰 때 유용하게 사용할 수 있는 몇 가지 함수를 알아보겠다. `document.cookie`를 수동으로 조작하지 않고 이 함수들을 활용하면 좀 더 편리하게 쿠키를 다룰 수 있다.

유사한 기능을 하는 다양한 쿠키 라이브러리가 존재하므로, 이 코드는 데모 목적으로 보면 된다. 데모이지만 실제 환경에서도 동작하는 코드이다.

### getCookie(name)

쿠키에 접근하는 가장 간단한 방법은 [정규 표현식]()을 사용하는 것이다.

다음 `getCookie(name)` 함수는 주어진 `name`의 쿠키를 반환한다.

```js
// 주어진 이름의 쿠키를 반환하는데,
// 조건에 맞는 쿠키가 없다면 undefined를 반환한다.
function getCookie(name) {
  let matches = document.cookie.match(new RegExp(
    '(?:^|; )' + name.replace(/([\.$?*|{}\(\)\[\]\\\/\+^])/g, '\\$1') + '=([^;]*)'
  ));
  return matches ? decodeURIComponent(matches[1]) : undefined;
}
```

위 코드에서 `new RegExp`는 `; name=<value>` 패턴을 찾기 위해 동적으로 생성된다.

쿠키값은 인코딩된 상태이기 때문에 `getCookie`는 내장 함수인 `decodeURIComponent`를 이용해 쿠키값을 디코딩한다는 것을 주목해야 한다.

### setCookie(name, value, options)

현재 경로(`path=/`)를 기본으로, 주어진 `name`과 `value`를 가진 쿠키를 설정한다(다른 기본값을 추가할 수 있다).

```js run
function setCookie(name, value, options = {}) {
  options = {
    path: '/',
    // 필요하면 옵션 기본값을 설정할 수 있다.
    ...options
  };

  if (options.expires instanceof Date) {
    options.expires = options.expires.toUTCString();
  }

  let updatedCookie = encodeURIComponent(name) + '=' + encodeURIComponent(value);

  for (let optionKey in options) {
    updatedCookie += '; ' + optionKey;
    let optionValue = options[optionKey];
    if (optionValue !== true) {
      updatedCookie += '=' + optionValue;
    }
  }

  document.cookie = updatedCookie;
}

// 사용 예시:
setCookie('user', 'John', { secure: true, 'max-age': 3600 });
```

### deleteCookie(name)

만료 기간을 음수로 설정하면 쿠키를 삭제할 수 있다.

```js
function deleteCookie(name) {
  setCookie(name, '', {
    'max-age': -1
  })
}
```

---
:warning: **갱신이나 삭제는 동일한 도메인과 경로에서 해야 한다.**

쿠키를 갱신하거나 삭제할 때는, 쿠키를 설정할 때 지정했던 도메인이나 경로를 사용해야 한다. 

---


## 부록: 서드 파티 쿠키

사용자가 방문 중인 도메인이 아닌 다른 도메인에서 설정한 쿠키를 '서드 파티 쿠키(third-party cookie)'라고 부른다. 

광고 회사는 사용자의 이용 행태를 추적하고, 광고를 제공하기 위해 오래전부터 서드 파티 쿠키를 사용하고 있다. 서드파티 쿠키는 쿠키를 설정한 도메인에 종속되기 때문에 광고 회사는 사용자가 어떤 사이트를 방문했는지 추적할 수 있다.

브라우저에는 이런 쿠키를 비활성화 할 수 있는 기능이 있는데, 이 기능을 사용하면 추적을 막을 수 있다.

여기에 더하여 일부 모던 브라우저는 서드 파티 쿠키에 대한 특별한 정책을 도입하여 광고회사의 추적을 막는다.
- Safari는 서드파티 쿠키를 전면적으로 허용하지 않는다.
- Firefox는 서드 파티 도메인 블랙 리스트를 만들어 리스트에 오른 도메인의 서드 파티 쿠키를 차단한다. 

---
:information_source: **주의**

`<script src="https://google-analytics.com/analytics.js">`같이 태그로 서드 파티 도메인에서 스크립트를 읽어오고, 이 스크립트 안에 `document.cookie`로 쿠키를 설정하는 코드가 있다면, 이때 만들어진 쿠키는 서드파티 쿠키가 아니다.

스크립트가 어디서 왔는지는 상관없이 쿠키는 현재 페이지의 도메인에 속한다.

---

## 부록: GDPR

이 주제는 자바스크립트와 전혀 관계가 없지만, 쿠키를 설정할 때 명심해야 할 사항이다.

EU(유럽연합)에는 사용자 개인 정보 보호를 강제하는 법령인 GDPR이 있다. 쿠키를 추적하는 경우 사용자로부터 명시적인 허가를 얻어야 한다는 것이 이 법령의 중요 요건 중 하나이다. 

이 요건은 쿠키를 이용한 사용자 추적, 식별에 관한 내용을 담고 있다.

쿠키를 단순히 정보 저장의 용도로만 사용한다면 이 법령이 강제하는 사항을 지킬 필요가 없다.

하지만, 인증 세션과 함께 쿠키를 설정하거나 id를 추적한다면 사용자의 동의를 반드시 얻어야 한다.

웹사이트는 다음과 같은 방법으로 GDPR에 대응할 수 있다. 이 방법이 적용된 사이트를 접해 본 적이 있을 것이다.

1. 인증된 사용자에 대해서만 추적 쿠키를 설정하려는 경우

    가입 양식에 "개인 정보 취급 방침 동의" 같은 확인란을 만들고, 사용자가 이에 동의할 경우에만 추적 쿠키를 설정한다.

2. 모든 사용자를 대상으로 추적 쿠키를 설정하려는 경우

    최초 방문자에게 쿠키설정에 대한 동의를 요구하는 '작은 창'을 보여주고, 사용자가 이에 동의한 경우에만 콘텐츠를 표시하고, 추적 쿠키를 설정한다. 새로운 방문자는 이런 절차가 번거롭다고 생각할 수 있다. 콘텐츠를 가리면서 "무조건 클릭해야 하는 창"을 그 누구도 달가워하지 않는다. 하지만 GDPR을 준수하려면 이 창이 반드시 있어야 한다.

## 요약

`document.cookie`는 쿠키에 접근할 수 있게 해준다.
- 쓰기는 해당 쿠키의 값만 갱신한다.
- 쿠키 이름과 값은 반드시 **인코딩**해야 한다.
- 쿠키 하나가 차지하는 용량은 최대 4KB까지이고, 사이트 하나당 약 20여 개를 허용한다(브라우저에 따라 다름).

쿠키 옵션:
- `path=/`의 기본값은 현재 경로이고, 설정한 경로나 그 하위 경로에서만 쿠키 정보를 볼 수 있다.
- `domain=site.com` 옵션에 아무 값을 입력하지 않았다면 쿠키를 설정한 도메인에서만 쿠키 정보를 얻을 수 있다. 명시적으로 도메인 주소를 설정한 경우에는 해당 도메인의 서브 도메인에서도 쿠키 정보를 얻을 수 있다.
- `expires`나 `max-age`로 쿠키의 만료 시간을 지정한다. 이 옵션이 없으면 브라우저가 닫힐 때 쿠키도 같이 사라진다.
- `secure` 는 HTTPS 연결에서만 쿠키를 사용할 수 있게 한다.
- `samesite` 는 요청이 사이트 외부에서 일어날 때 브라우저가 쿠키를 보내지 못하게 한다. XSRF 공격을 막을 때 유용하다.

추가 사항:
- 브라우저에 따라 서드 파티 쿠키를 허용하지 않을 수 있다. Safari는 기본적으로 서드 파티 쿠키를 금지한다.
- 사용자가 EU 국가 거주자인 경우 GDPR을 준수해야 한다. 따라서 사용자 추적 시 반드시 동의를 얻어야 한다.
