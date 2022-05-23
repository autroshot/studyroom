# defer, async 스크립트
모던 웹브라우저에서 돌아가는 스크립트들은 대부분 HTML보다 '무겁다'. 용량이 커서 다운로드에 오랜 시간이 걸리고 처리도 느리다.

브라우저는 HTML을 읽다가 `<script>...</script>` 태그를 만나면 스크립트를 먼저 실행해야 하므로 DOM 생성을 멈춘다. 이는 `src` 속성이 있는 외부 스크립트 `<script src="..."></script>`를 만났을 때도 동일하다. 외부에서 스크립트를 다운받고 실행한 후에 남은 페이지를 처리할 수 있다.

이는 두 가지 중요한 문제로 이어진다.
1. 스크립트는 자신의 아래에 있는 DOM 요소를 볼 수 없으므로 핸들러 등을 추가할 수 없다.
2. 페이지 위쪽에 용량이 큰 스크립트가 있는 경우 스크립트가 페이지를 '막아버린다'. 페이지에 접속하는 사용자들은 스크립트를 다운받고 실행할 때까지 페이지 콘텐츠를 볼 수 없다.

```html
<p>...스크립트 앞 콘텐츠...</p>

<script src="https://javascript.info/article/script-async-defer/long.js?speed=1"></script>

<!-- 스크립트 다운로드 및 실행이 끝나기 전까지 아래 내용이 보이지 않는다. -->
<p>...스크립트 뒤 콘텐츠...</p>
```
이에 대한 몇 가지 해결법이 있다. 다음 예시처럼 페이지 하단에 스크립트를 넣을 수 있다. 이렇게 하면 스크립트 위에 있는 요소를 볼 수 있으며 페이지 콘텐츠가 표시되는 것을 막지 않는다.
```html
<body>
  ... 스크립트 위 콘텐츠들 ...

  <script src="https://javascript.info/article/script-async-defer/long.js?speed=1"></script>
</body>
```
그런데 이 방법은 완벽한 해결책이 아닙니다. 브라우저가 HTML 문서를 모두 다운로드한 후에만 스크립트를 인식하고 다운로드를 시작할 수 있다고 가정해 보겠다. 이때 HTML 문서가 매우 길면 눈에 띄는 지연이 생길 수 있다.

속도가 빠른 곳은 괜찮겠지만 세계의 많은 사람들은 여전히 ​​인터넷 속도가 느리고 완벽하지 않은 모바일 인터넷 연결을 사용한다.

다행히도 이런 문제를 해결할 수 있는 `<script>` 속성이 있다. 바로 `defer`와 `async`이다.

## defer
`defer` 속성은 브라우저에게 스크립트를 기다리지 말 것을 지시한다. 브라우저는 계속해서 HTML을 처리하고 DOM을 만든다. 스크립트는 백그라운드에서 로드되며 DOM이 완전히 만들어지면 스크립트가 실행된다.

위의 예시와 동일하지만 `defer`가 추가되었다.
```html
<p>...스크립트 앞 콘텐츠...</p>

<script defer src="https://javascript.info/article/script-async-defer/long.js?speed=1"></script>

<!-- 바로 볼 수 있다. -->
<p>...스크립트 뒤 콘텐츠...</p>
```
- `defer`가 있는 스크립트는 페이지를 절대 막지 않는다.
- `defer`가 있는 스크립트는 DOM이 준비되면 실행된다. 하지만 `DOMContentLoaded` 이벤트 전에 실행된다.

다음 예시를 통해 두 번째 항목을 설명하겠다.
```html
<p>...스크립트 앞 콘텐츠...</p>

<script>
  document.addEventListener('DOMContentLoaded', () => alert("`defer` 스크립트가 실행된 후, DOM이 준비되었습니다!")); // (2)
</script>

<script defer src="https://javascript.info/article/script-async-defer/long.js?speed=1"></script>

<p>...스크립트 뒤 콘텐츠...</p>
```

1. 페이지 콘텐츠는 바로 표시된다.
2. `DOMContentLoaded` 이벤트 핸들러는 지연된 스크립트(deferred script)를 기다린다. 스크립트가 다운로드되고 실행될 때만 핸들러가 트리거된다.

**지연된 스크립트들은 일반 스크립트와 동일하게 상대적인 순서를 유지한다.**

다음 예시에는 두 개의 지연된 스크립트, `long.js`와 `small.js`가 있다.
```html
<script defer src="https://javascript.info/article/script-async-defer/long.js"></script>
<script defer src="https://javascript.info/article/script-async-defer/small.js"></script>
```
브라우저는 성능 향상을 위해 페이지의 스크립트들을 스캔하고 **병렬로 다운로드**한다. 따라서 위의 예시에서는 크기가 작은 `small.js`의 다운로드가 먼저 완료될 것이다.

그러나 `defer` 속성은 상대적인 순서를 그대로 유지시킨다. 따라서 `small.js`는 먼저 로드되더라도 계속 기다리다가, `long.js`가 실행된 이후에야 실행된다.

이 작동 방식은 자바스크립트 라이브러리를 로드한 후에 이 라이브러리를 의존하는 스크립트를 로드해야 하는 경우에 중요할 수 있다.

---
:information_source: **`defer` 속성은 외부 스크립트에서만 유효하다.**

`<script>`에 `src`가 없으면 `defer` 속성은 무시된다. 

---

## async
`async`(비동기) 속성은 `defer`와 다소 비슷하다. 이 속성이 있는 스크립트는 페이지를 막지 않는다. 하지만 행동에는 중요한 차이가 있다.

`async` 속성은 스크립트가 완전히 독립적이라는 것을 의미한다.
- 브라우저는 `async` 스크립트를 막지 않는다.
- 다른 스크립트는 `async` 스크립트를 기다리지 않고 `async` 스크립트도 다른 스크립트를 기다리지 않는다.
- `DOMContentLoaded` 이벤트와 async 스크립트는 서로를 기다리지 않는다.
  - 페이지 구성이 끝난 후에 async 스크립트 로딩이 끝난 경우, `DOMContentLoaded`는 async 스크립트 전에 발생할 수 있다.
  - async 스크립트가 짧거나 HTTP 캐시에 있었던 경우, `DOMContentLoaded`는 async 스크립트 이후에 발생할 수 있다.

즉 `async` 스크립트는 백그라운드에서 로드되고 준비가 되면 실행된다. DOM과 다른 스크립트는 async 스크립트를 기다리지 않으며 반대도 마찬가지다. 로드되면 바로 실행되는 완전히 독립적인 스크립트이다.

다음은 `defer`에서 본 것과 비슷한 예시이다. `defer` 대신 `async`가 들어갔다.

두 스크립트는 서로를 기다리지 않는다. 먼저 로드되는 스크립트(아마 `small.js`일 것이다)가 먼저 실행된다. 
```html
<p>...스크립트 앞 콘텐츠...</p>

<script>
  document.addEventListener('DOMContentLoaded', () => alert('DOM이 준비 되었습니다!'));
</script>

<script async src="https://javascript.info/article/script-async-defer/long.js"></script>
<script async src="https://javascript.info/article/script-async-defer/small.js"></script>

<p>...스크립트 뒤 콘텐츠...</p>
```
- 페이지 콘텐츠가 바로 표시된다. `async`는 이것을 막지 않는다.
- `DOMContentLoaded` 이벤트의 발생 시점은 `async` 이전이 될 수도, 이후가 될 수도 있다.
- `small.js`는 뒤에 있지만 작은 스크립트이므로 `long.js`보다 로드가 먼저 되고 실행도 먼저 될 것이다. 만약 `long.js`이 캐시되었던 경우에는 먼저 로드되고 실행된다. 정리하면 async 스크립트는 로드가 먼저 된 순서대로 실행된다.

async 스크립트는 카운터나 광고처럼 독립적인 서드 파티 스크립트를 페이지에 통합할 때 유용하다. 우리의 스크립트와 의존 관계가 아니고 우리의 스크립트가 기다릴 필요도 없기 때문이다.
```html
<!-- Google Analytics는 일반적으로 다음과 같이 삽입한다. -->
<script async src="https://google-analytics.com/analytics.js"></script>
```

---
:information_source: **`async` 속성은 외부 스크립트에서만 유효하다.**

`defer`과 동일하게 `<script>`에 `src`가 없으면 `async` 속성은 무시된다. 

---

## 동적 스크립트
자바스크립트를 사용하여 스크립트를 만들고 문서에 동적으로 추가할 수 있다.
```js run
let script = document.createElement('script');
script.src = "/article/script-async-defer/long.js";
document.body.append(script); // (*)
```
스크립트는 문서에 추가되는 즉시 로드를 시작한다`(*)`.

**동적 스크립트(dynamic script)는 기본적으로 'async' 스크립트처럼 행동한다.**

따라서 다음과 같은 특징을 지닌다.
- 동적 스크립트는 아무것도 기다리지 않으며, 반대도 마찬가지다.
- 먼저 로드되면 먼저 실행된다.

명시적으로 `script.async=false`을 설정하면 이 특징은 사라진다. 그러면 스크립트는 `defer`처럼 문서 순서에 따라 실행된다.

다음 예시에서 `loadScript(src)` 함수는 스크립트를 추가하고 `async`를 `false`로 설정한다.

따라서 `long.js`가 항상 먼저 실행된다.
```js
function loadScript(src) {
  let script = document.createElement('script');
  script.src = src;
  script.async = false;
  document.body.append(script);
}

// async=false이기 때문에 long.js가 먼저 실행된다.
loadScript('/article/script-async-defer/long.js');
loadScript('/article/script-async-defer/small.js');
```

## 요약
`async`와 `defer` 스크립트는 다운로드될 때 페이지 렌더링을 막지 않는다는 공통점이 있다. 따라서 사용자는 즉시 페이지 콘텐츠를 읽고 페이지를 파악할 수 있게 된다.

하지만 `async`와 `defer`에는 본질적인 차이점이 있다.

|         | 순서 | DOMContentLoaded |
|---------|---------|---------|
| `async` | 문서 내 순서와 상관없이 먼저 다운로드된 스크립트가 먼저 실행된다. | 관련이 없다. async 스크립트는 HTML 문서가 완전히 다운로드되지 않아도 로드되고 실행될 수 있다. 스크립트 크기가 작거나 캐싱된 경우, 또는 HTML 문서가 매우 길 때 이런 일이 발생한다. |
| `defer` | 문서 순서(문서에 위치한 순서) |  defer 스크립트는 문서 다운로드와 파싱이 완료된 이후, 그리고 `DOMContentLoaded` 바로 전에 실행된다. |

실무에서 `defer`는 전체 DOM이 필요하거나 실행 순서가 중요한 경우에 사용된다. `async`는 카운터나 광고처럼 독립적인 스크립트나 실행 순서가 중요하지 않은 경우에 사용된다.

---
:warning: **페이지는 스크립트가 없어도 사용 가능해야 한다.**

`defer`나 `async`를 사용하면 사용자는 스크립트가 로드되기 전에도 페이지를 볼 수 있다는 점을 주의해야 한다.

이 경우에는 일부 그래픽 컴포넌트가 초기화되지 않을 수 있다.

따라서 아직 작동하지 않는 곳에는 '로딩' 표시를 넣고 버튼을 비활성화해야 한다. 사용자는 자신이 페이지에서 어떤 것을 할 수 있는지, 아직 준비 중인 곳은 어디인지 명확하게 알 수 있어야 한다.

---

다음은 일반 스크립트와 `defer`, `async` 속성이 있는 스크립트의 차이를 그림으로 나타낸 것이다.

![async-defer-1](https://user-images.githubusercontent.com/95019875/169816720-0df3b697-81cd-4b54-ac1b-f38ad76a540c.png)
![async-defer-2](https://user-images.githubusercontent.com/95019875/169816740-c242640b-8027-40d5-9160-6c4af8f8825b.png)
