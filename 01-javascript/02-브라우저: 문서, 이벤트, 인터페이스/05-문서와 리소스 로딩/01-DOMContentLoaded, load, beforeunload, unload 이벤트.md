# 페이지 관련 이벤트: DOMContentLoaded, load, beforeunload, unload
HTML 문서의 생명 주기에는 다음과 같은 세 가지 주요 이벤트가 관여한다.
- `DOMContentLoaded` -- 브라우저가 HTML을 완전히 불러오고 DOM 트리가 생성되지만, 이미지나 스타일 시트 같은 외부 리소스는 아직 로드되지 않았을 수 있다.
- `load` -- HTML뿐만 아니라 외부 리소스도 모두 불러왔다.
- `beforeunload/unload` -- 사용자가 페이지를 떠나고 있다.

위의 세 이벤트는 다음과 같이 활용할 수 있다.
- `DOMContentLoaded` -- DOM이 준비되어 핸들러가 DOM 노드를 조회하고 인터페이스를 초기화할 수 있다.
- `load` -- 외부 리소스가 로드되어 스타일이 적용되고 이미지 크기를 알 수 있다.
- `beforeunload` -- 사용자가 떠날 때, 사용자가 변경 사항을 저장했는지 확인하고 정말로 떠나고 싶은지 물어볼 수 있다.
- `unload` -- 사용자가 거의 떠났지만 통계 전송과 같은 일부 작업을 수행할 수 있다.

## DOMContentLoaded
`DOMContentLoaded` 이벤트는 `document` 객체에서 발생한다.

따라서 이 이벤트를 다루려면 `addEventListener`를 사용해야 한다.
```js
document.addEventListener('DOMContentLoaded', ready);
// "document.onDOMContentLoaded = ..."는 동작하지 않는다.
```
예시:
```html
<script>
  function ready() {
    alert('DOM이 준비되었습니다!');

    // 이미지가 로드되지 않은 상태이기 때문에 사이즈는 0x0이다.
    alert(`이미지 사이즈: ${img.offsetWidth}x${img.offsetHeight}`);
  }

  document.addEventListener('DOMContentLoaded', ready);
</script>

<img id="img" src="https://en.js.cx/clipart/train.gif?speed=1&cache=0">
```
위 예시에서 `DOMContentLoaded` 핸들러는 문서가 로드되었을 때 실행된다. 따라서 핸들러 아래에 있는 `<img>`뿐만 아니라 모든 요소에 접근할 수 있다.

그렇지만 이미지 로딩은 기다리지 않기 때문에 `alert` 창에는 이미지 사이즈가 `0`으로 뜬다.

단순히 DOM 트리가 완성되면 `DOMContentLoaded` 이벤트가 발생하는 것이 아니다. 몇 가지 특이 사항이 있다.

### DOMContentLoaded와 scripts
브라우저는 HTML 문서를 읽다가 `<script>` 태그를 만나면, DOM 트리 구성을 잠시 멈추고 `<script>`를 실행한다. 스크립트에 DOM을 수정하는 코드나 `document.write`가 있을 수 있기 때문이다. 따라서 `DOMContentLoaded`는 모든 스크립트가 끝날 때까지 기다린다.

예시:
```html
<script>
  document.addEventListener('DOMContentLoaded', () => {
    alert('DOM이 준비되었습니다!');
  });
</script>

<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.3.0/lodash.js"></script>

<script>
  alert('라이브러리 로딩이 끝나고 인라인 스크립트가 실행되었습니다.');
</script>
```
예시를 실행하면 "라이브러리 로딩이 끝나고..."가 먼저 보이고 "DOM이 준비되었습니다!"가 출력된다. 스크립트가 모두 실행된 후에 `DOMContentLoaded` 이벤트가 처리되는 것이다.

---
:warning: **DOMContentLoaded를 막지 않는 스크립트**

이 규칙에는 두 가지 예외 사항이 있다.
1. `async` 속성이 있는 스크립트는 `DOMContentLoaded`를 막지 않는다. 자세한 내용은 다음 챕터에서 다룰 것이다.
2. `document.createElement('script')`로 동적으로 생성되고 웹 페이지에 추가된 스크립트는 `DOMContentLoaded`를 막지 않는다.

---

### DOMContentLoaded와 styles
외부 스타일 시트는 DOM에 영향을 주지 않기 때문에 `DOMContentLoaded`는 외부 스타일 시트를 기다리지 않는다. 

하지만 예외가 하나 있다. 스타일 뒤에 스크립트가 있으면, 스크립트는 스타일 시트가 로드될 때까지 기다린다.
```html
<link type="text/css" rel="stylesheet" href="style.css">
<script>
  // 이 스크립트는 위 스타일 시트가 로드될 때까지 실행되지 않는다.
  alert(getComputedStyle(document.body).marginTop);
</script>
```
이런 예외는 스크립트가 요소의 좌표나 스타일에 종속적인 속성을 원할 수 있어서 존재한다. 

`DOMContentLoaded`이 스크립트를 기다리는 것처럼 스타일도 기다리게 된다.

### 브라우저 내장 자동완성
Firefox, Chrome, Opera는 `DOMContentLoaded`에서 폼을 자동완성한다.

페이지에 아이디와 비밀번호를 적는 폼이 있고 브라우저에 해당 정보를 기억하고 있으면, `DOMContentLoaded`에서 폼을 자동으로 채운다. 물론 사용자가 허가가 있어야 한다.

따라서 스크립트의 로딩이 오래 걸려서 `DOMContentLoaded` 이벤트가 지연되면 자동완성도 자연히 지연된다.

## window.onload
`window` 객체의 `load` 이벤트는 스타일, 이미지 등의 리소스가 모두 로드되었을 때 실행된다. `load` 이벤트는 `onload` 프로퍼티로도 사용할 수 있다.

다음 예시에서 `window.onload`는 이미지가 모두 로드된 후에 실행되기 때문에 이미지 크기가 제대로 출력된다.
```html
<script>
  window.onload = function() { // window.addEventListener('load', (event) => {와 동일하다.
    alert('페이지 전체가 로드되었습니다.');

    // 이번에는 이미지가 제대로 불러와 진 후에 얼럿창이 실행된다.
    alert(`이미지 사이즈: ${img.offsetWidth}x${img.offsetHeight}`);
  };
</script>

<img id="img" src="https://en.js.cx/clipart/train.gif?speed=1&cache=0">
```

## window.onunload
`window`의 `unload` 이벤트는 방문자가 페이지를 떠날 때 발생한다. 이 이벤트에서는 팝업 창을 닫는 것과 같은 딜레이가 없는 작업을 수행할 수 있다.

분석 정보를 보내는 작업도 여기서 할 수 있다.

마우스 클릭, 스크롤, 조회된 페이지 영역 등 페이지 사용 방식에 대한 데이터를 수집한다고 가정해 보겠다.

`unload` 이벤트는 사용자가 페이지를 떠날 때 발생하므로 여기서 데이터를 서버에 저장하고 싶을 것이다.

메서드 `navigator.sendBeacon(url, data)`은 바로 이런 용도를 위해 존재한다. 메서드에 대한 자세한 설명은 <https://w3c.github.io/beacon/>에서 볼 수 있다.

`sendBeacon`는 백그라운드에서 데이터를 전송한다. 다른 페이지로 이동해도 딜레이가 안 생기는데, 이는 브라우저가 페이지를 떠나도 `sendBeacon`는 계속 실행되고 있기 때문이다.

사용법:
```js
let analyticsData = { /* 분석 정보가 담긴 객체 */ };

window.addEventListener('unload', function() {
  navigator.sendBeacon('/analytics', JSON.stringify(analyticsData));
};
```
- 요청은 POST 방식으로 전송된다.
- [Fetch](https://javascript.info/fetch) 챕터에서 설명한 대로 문자열뿐만 아니라 폼을 비롯한 기타 포맷으로도 전송할 수 있다. 하지만 보통은 문자열 형태(stringified)의 객체가 전송된다.
- 전송 데이터는 64kb로 제한된다.

`sendBeacon` 요청이 완료된 시점에는 브라우저가 이미 문서를 떠났을 수 있으므로 서버 응답을 받을 수 있는 방법이 없다. 사용자 분석 정보에 관한 응답은 대개 비어있다.

fetch 메서드는 페이지를 떠난 후에도 요청이 가능하게 해주는 플래그 `keepalive`를 지원한다. 자세한 내용은 [Fetch API](https://javascript.info/fetch-api) 챕터에서 확인할 수 있다.

다른 페이지로의 전환을 취소하고 싶다면 여기서는 할 수 없다. 바로 다음에 배우는 `onbeforeunload`에서 가능하다.

## window.onbeforeunload
사용자가 현재 페이지를 떠나거나 창을 닫으려고 할 때 `beforeunload` 핸들러에서 추가 확인을 요청할 수 있다.

`beforeunload` 이벤트를 취소하면 브라우저는 방문자에게 확인을 요청한다.

다음 예시를 실행하고 새로고침을 하면 브라우저에서 확인을 요청한다.
```js
window.onbeforeunload = function() {
  return false;
};
```
역사적 이유로, 비어있지 않은 문자열을 반환하는 것도 이벤트를 취소하는 것으로 간주한다. 과거에는 브라우저에서 이 문자열을 메시지에 표시했는데, [모던 명세서](https://html.spec.whatwg.org/#unloading-documents)에서는 이것을 금지하고 있다.

예시:
```js
window.onbeforeunload = function() {
  return '저장되지 않은 변경사항이 있습니다. 정말 페이지를 떠나실 건가요?';
};
```
이렇게 금지된 이유는 일부 사이트 관리자들이 오해의 소지가 있고 성가신 메시지를 띄우기 위해 `beforeunload`를 남용했기 때문이다. 오래된 브라우저에서는 여전히 커스텀 메시지가 표시되기도 한다.

---
:warning: **`beforeunload` 핸들러에서는 `event.preventDefault()`가 동작하지 않는다.**

대부분의 브라우저는 `event.preventDefault()`를 무시한다.

따라서 다음 코드는 작동하지 않을 가능성이 있다.
```js
window.addEventListener('beforeunload', event => {
  // 작동하지 않는다.
	event.preventDefault();
});
```
의도한 대로 코드가 제대로 동작하게 만들려면 `event.returnValue`을 문자열로 설정해야 한다.
```js
window.addEventListener('beforeunload', event => {
  // 작동한다. window.onbeforeunload에서 반환하는 것과 동일하다.
	event.returnValue = '저장되지 않은 변경사항이 있습니다. 정말 페이지를 떠나실 건가요?';
});
```

---

## readyState
문서가 로드된 후에 `DOMContentLoaded` 핸들러를 설정하면 핸들러는 절대 실행되지 않을 것이다.

그런데 문서가 로드되었는지 아닌지를 알 수 없는 경우가 있다. 지금이든 나중에든 DOM의 로딩이 끝났을 때 함수가 실행되기를 원한다고 가정해 보겠다.

이때 현재 로딩 상태를 알려주는 `document.readyState` 프로퍼티를 사용할 수 있다.

프로퍼티의 가능한 값은 세 개이다.
- `"loading"` -- 문서가 로딩 중이다.
- `"interactive"` -- 문서의 로딩이 끝났다.
- `"complete"` -- 이미지와 같은 리소스까지 모두 로딩이 끝났다.

`document.readyState`의 값을 확인하고 상황에 맞게 핸들러를 설정하거나 코드를 실행할 수 있다.

예시:
```js
function work() { /*...*/ }

if (document.readyState == 'loading') {
  // 아직 로딩 중이므로 이벤트를 기다린다.
  document.addEventListener('DOMContentLoaded', work);
} else {
  // DOM이 완성되었다.
  work();
}
```
상태가 바뀔 때 발생하는 `readystatechange` 이벤트도 있다.

다음 예시에서는 `readystatechange` 이벤트를 이용해 모든 상태를 출력한다.
```js
// 현재 상태를 출력한다.
console.log(document.readyState);

// 상태가 변경될 때마다 출력한다.
document.addEventListener('readystatechange', () => console.log(document.readyState));
```
`readystatechange` 이벤트는 문서 로딩 상태를 추적하는 대체 매커니즘으로, 오래전에 만들어진 이벤트이다. 요즘은 거의 사용하지 않는다.

전체 이벤트 흐름을 살펴보겠다.

예시:
```html
<script>
  log('initial readyState:' + document.readyState);

  document.addEventListener('readystatechange', () => log('readyState:' + document.readyState));
  document.addEventListener('DOMContentLoaded', () => log('DOMContentLoaded'));

  window.onload = () => log('window onload');
</script>

<iframe src="iframe.html" onload="log('iframe onload')"></iframe>

<img src="http://en.js.cx/clipart/train.gif" id="img">
<script>
  img.onload = () => log('img onload');
</script>
```
실행 결과는 다음과 같다.
1. [1] initial readyState:loading
2. [2] readyState:interactive
3. [2] DOMContentLoaded
4. [3] iframe onload
5. [4] img onload
6. [4] readyState:complete
7. [4] window onload

대괄호 안의 숫자는 대략적인 이벤트 발생 시간을 나타낸다. 같은 숫자는 1 미리 초 오차 범위 내에서 동시에 실행된 이벤트라는 것을 의미한다.

- `document.readyState`는 `DOMContentLoaded`가 실행되기 바로 직전에 `interactive`가 된다. 이 두 개는 사실 같은 것을 의미한다.
- `document.readyState`는 `iframe`, `img`를 비롯한 리소스 전부가 로드되었을 때 `complete`가 된다. 위 예시에서 `img.onload`와 `window.onload`가 실행된 시점과 거의 동일한 시점에 `readyState`의 값이 `complete`로 바뀐다는 것을 확인할 수 있다. `readyState`의 값이 `complete`로 바뀐다는 것은 `window.onload`가 실행된다는 것과 동일한 의미이다. 이 둘의 차이점은 `window.onload`는 다른 `load` 핸들러가 전부 실행된 후에 동작한다는 것에 있다.

## 요약
페이지 로드 관련 이벤트는 다음과 같다.
- `DOMContentLoaded` -- DOM이 준비되면 `document` 객체에서 실행된다. 이 시점부터 요소에 자바스크립트를 적용할 수 있다.
  - `<script>...</script>`나 `<script src="..."></script>`를 사용해 삽입한 스크립트는 DOMContentLoaded가 실행되는 것을 막는다. 브라우저는 이 스크립트의 실행을 기다린다.
  - `DOMContentLoaded`가 실행되어도 이미지를 비롯한 기타 리소스는 여전히 로딩 중일 수 있다.
- `load` -- 페이지를 비롯한 이미지 등의 리소스 전부가 모두 불러와졌을 때 `window` 객체에서 실행된다. 모든 자원이 로드되는 걸 기다리기에는 시간이 오래 걸릴 수 있어서 이 이벤트는 잘 사용되지 않는다.
- `beforeunload` -- 사용자가 페이지를 떠나려 할 때 `window` 객체에서 발생한다. 이 이벤트를 취소하려 하면 브라우저는 사용자에게 "변경사항이 저장되지..."등의 메시지를 띄워 정말 페이지를 떠날 건지를 물어본다.
- `unload` -- 사용자가 최종적으로 사이트를 떠날 때 `window` 객체에서 발생한다. `unload` 이벤트 핸들러에서는 지연을 만드는 복잡한 작업이나 사용자와의 상호작용은 할 수 없다. 이 제약사항 때문에 `unload` 이벤트는 아주 드물게 사용된다. `navigator.sendBeacon`로 네트워크 요청을 보낼 수 있다.
- `document.readyState` -- 문서의 현재 상태를 나타낸다. `readystatechange` 이벤트를 사용하면 변화를 추적할 수 있다.
  - `loading` -- 문서를 불러오는 중이다.
  - `interactive` -- 문서가 완전히 로딩된 때이다. `DOMContentLoaded`가 실행되기 바로 직전에 해당 값으로 변경된다.
  - `complete` -- 문서를 비롯해 이미지 등의 리소스도 모두 불러와졌을 때이다. `window.onload`가 실행되기 바로 직전에 해당 값으로 변경된다.
