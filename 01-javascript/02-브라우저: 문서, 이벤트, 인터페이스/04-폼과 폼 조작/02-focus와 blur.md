# focus와 blur
사용자가 폼 요소를 클릭하거나 탭 키를 눌러 요소로 이동하면 해당 요소가 포커스(focus)된다. `autofocus`라는 HTML 속성을 사용해도 요소를 포커스할 수 있으며 해당 요소는 페이지가 로드된 후 자동으로 포커싱된다.

요소를 포커싱한다는 것은 일반적으로 '여기에 데이터를 입력할 준비를 하라'는 것을 의미하기 때문에 요소 포커싱이 이뤄지는 순간에 요구사항을 충족시키는 초기화 코드를 실행할 수 있다.

요소가 포커스를 잃는 순간(blur)은 요소가 포커스를 얻는 순간보다 더 중요할 수 있다. 사용자가 다른 곳을 클릭하거나 탭 키를 눌러 다음 폼 필드로 이동하면 요소는 포커스를 잃게 된다.

요소가 포커스를 잃는 것은 대개 '데이터 입력이 완료되었다'는 것을 의미하기 때문에 포커싱이 해제되는 순간에 데이터를 체크하거나 입력된 데이터를 저장하기 위해 서버에 요청을 보내는 등의 작업을 실행할 수 있다.

## focus, blur 이벤트
`focus` 이벤트는 요소가 포커스를 받을 때, `blur` 이벤트는 포커스를 잃을 때 발생한다.

두 이벤트를 입력 필드 유효성 검사에 사용해 보겠다.

예시에서 각 핸들러는 다음과 같은 역할을 한다.

- `blur` 핸들러에서는 필드에 이메일이 잘 입력되었는지 확인하고 잘 입력되지 않은 경우에는 에러를 보여준다.
- `focus` 핸들러에서는 에러 메시지를 숨긴다(이메일 재확인은 `blur` 핸들러에서 한다).

```html
<style>
  .invalid { border-color: red; }
  #error { color: red; }
</style>

이메일: <input type="email" id="input">
<div id="error"></div>

<script>
input.onblur = function() {
  if (!input.value.includes('@')) { // @ 유무를 이용해 유효한 이메일 주소가 아닌지 체크한다.
    input.classList.add('invalid');
    error.innerHTML = '올바른 이메일 주소를 입력하세요.'
  }
};

input.onfocus = function() {
  if (this.classList.contains('invalid')) {
    // 사용자가 새로운 값을 입력하려고 하므로 에러 메시지를 지운다.
    this.classList.remove('invalid');
    error.innerHTML = "";
  }
};
</script>
```
모던 HTML을 사용하면 `required`, `pattern` 등의 다양한 속성을 사용해 입력값을 검증 할 수 있다. HTML 속성만으로도 검증이 가능한 것이다. 

그럼에도 불구하고 자바스크립트를 사용하는 이유는 자바스크립트가 좀 더 유연하기 때문이다. 여기에 더하여 자바스크립트를 사용하면 제대로 된 값이 입력되었을 때 자동으로 해당값을 서버에 보낼 수도 있다.

## focus, blur 메서드
`elem.focus()`와 `elem.blur()` 메서드를 사용하면 요소에 포커스를 줄 수도 있고 제거할 수도 있다.

값이 유효하지 않으면 input을 떠나지 못하게 하는 예시를 살펴보겠다.
```html
<style>
  .error {
    background: red;
  }
</style>

이메일: <input type="email" id="input">
<input type="text" style="width:220px" placeholder="이메일 형식이 아닌 값을 입력하고 여기에 포커스를 주세요.">

<script>
  input.onblur = function() {
    if (!this.value.includes('@')) { // 이메일이 아니다.
      // 에러를 출력한다.
      this.classList.add('error');

      // input 필드가 포커스 되도록 한다.
      input.focus();
    } else {
      this.classList.remove('error');
    }
  };
</script>
```
이 예시는 Firefox([버그](https://bugzilla.mozilla.org/show_bug.cgi?id=53579))를 제외한 모든 브라우저에서 정상적으로 동작한다.

이메일이 아닌 값을 입력하고 탭 키나 다른 곳을 클릭해 `<input>`을 벗어나려 하면 `onblur` 메서드가 동작해 포커스를 다시 입력 필드로 돌려놓는다.

`onblur`는 요소가 포커스를 잃은 후에 발생하기 때문에 만약 `onblur` 안에서 `event.preventDefault()`를 사용했다면 포커스를 잃는 것을 막을 수 없게 된다.

---
:warning: **자바스크립트에 의한 포커스 손실**

다양한 원인에 의해 포커스를 잃을 수 있다.

그 중 자바스크립트에 의해 포커스를 잃는 예시는 다음과 같다.
- `alert`은 포커스를 자신에게로 이동시키므로 요소는 포커스를 잃게 된다(`blur` 이벤트). 그리고 나서 `alert`이 해제되면 포커스가 다시 요소에 돌아온다(`focus` 이벤트).
- 포커스를 가지고 있던 요소가 DOM에서 제거되면 포커스를 잃는다. 이후에 요소가 다시 삽입되어도 포커스가 다시 돌아오지는 않는다.

이 특징은 가끔 `focus`나 `blur` 핸들러를 의도치 않게 트리거한다. 

따라서 `focus`, `blur` 이벤트를 사용할 때는 조심할 필요가 있다. 사용자에 의한 포커스 손실만을 추적하려면 자바스크립트에 의한 포커스 손실을 피해야 한다.

---

## tabindex를 사용해 요소 포커싱하기
대다수의 요소는 기본적으로 포커싱을 지원하지 않는다.

포커싱을 지원하지 않는 요소 목록은 브라우저마다 다르다. 하지만 `<button>`, `<input>`, `<select>`, `<a>`와 같이 사용자가 웹 페이지와 상호작용 할 수 있게 도와주는 요소는 `focus`, `blur`를 지원한다는 것은 확실하다.

반면 `<div>`, `<span>`, `<table>`같이 무언가를 표시하는 용도로 사용하는 요소들은 포커싱을 지원하지 않는다. 따라서 이런 요소에는 `elem.focus()` 메서드가 동작하지 않고 `focus`, `blur`이벤트도 트리거되지 않는다.

그럼에도 불구하고 포커싱 하고 싶다면 HTML 속성 `tabindex`을 사용하면 된다.

`tabindex` 속성이 있는 모든 요소는 포커스가 가능해진다. 이 속성의 값은 숫자인데, 이 숫자는 탭 키를 누를 때 요소 사이를 이동하는 순서가 된다.

`tabindex`가 `1`인 요소부터 시작해 점점 큰 숫자가 매겨진 요소로 이동하고, 그다음에는 `tabindex`가 없는 요소(평범한 `<input>` 요소 등)로 이동한다.

`tabindex`가 없는 요소들은 문서 내의 순서에 따라 포커스가 이동한다(기본 순서).

`tabindex`를 사용할 때 주의할 것이 있다.
- `tabindex`가 `0`인 요소는 `tabindex` 속성이 없는 것처럼 동작한다. 따라서 포커스를 이동시킬 때 `tabindex`가 `0`인 요소는 `tabindex`가 `1` 이상인 요소보다 나중에 포커스를 받는다.

  `tabindex="0"`은 포커스 순서는 그대로 유지하면서 요소를 포커싱 가능하게 만들 때 사용한다. 요소의 포커스 우선 순위를 일반 `<input>`과 같게 만든다.

- `tabindex`가 `-1`인 요소는 스크립트로만 포커싱하고 싶은 요소에 사용한다. 탭 키를 사용하면 이 요소는 무시되지만 `elem.focus()` 메서드로 포커싱이 가능하다.

예시:
```html
<ul>
  <li tabindex="1">1</li>
  <li tabindex="0">0</li>
  <li tabindex="2">2</li>
  <li tabindex="-1">-1</li>
</ul>

<style>
  li { cursor: pointer; }
  :focus { outline: 1px dashed green; }
</style>
```
포커스는 `tabindex`가 `1`, `2`, `0`인 요소로 차례로 이동한다. `<li>`는 기본적으로 포커스 할 수 없는 요소이지만 `tabindex`를 사용해서 포커싱되게 만들었다.

---
:information_source: **`elem.tabIndex` 프로퍼티를 사용해도 된다.**

자바스크립트를 사용해 `elem.tabIndex` 프로퍼티를 추가해주면 `tabindex` 속성을 사용한 것과 동일한 효과를 볼 수 있다.

---

## focusin과 focusout을 사용해 이벤트 위임하기
`focus`와 `blur` 이벤트는 버블링되지 않는다.

예시에서 `<form>`에 `onfocus`를 추가해 강조 효과를 주려고 하는데 의도한 대로 동작하지 않는다.
```html
<!-- 폼 안에서 포커스가 된 경우 클래스를 추가한다. -->
<form onfocus="this.className='focused'">
  <input type="text" name="surname" value="성">
  <input type="text" name="name" value="이름">
</form>

<style> .focused { outline: 1px solid red; } </style>
```
그 이유는 사용자가 `<input>`을 포커스해도 `focus` 이벤트는 해당 입력 필드에서만 트리거되기 때문이다. `focus` 이벤트는 버블링되지 않는다. 따라서 `form.onfocus`는 절대 트리거되지 않는다.

해결법은 두 가지가 있다.

첫 번째 방법은 `focus`와 `blur`는 버블링되지 않지만 캡처링은 된다는 점을 이용하는 것이다.

예시:
```html
<form id="form">
  <input type="text" name="surname" value="성">
  <input type="text" name="name" value="이름">
</form>

<style> .focused { outline: 1px solid red; } </style>

<script>
  // 캡쳐링 단계에서 핸들러가 트리거되도록 한다(마지막 인수가 true).
  form.addEventListener('focus', () => form.classList.add('focused'), true);
  form.addEventListener('blur', () => form.classList.remove('focused'), true);
</script>
```
두 번째 방법은 `focusin`과 `focusout`을 이용하는 것입니다. 두 이벤트는 `focus`, `blur`와 동일하지만 버블링이 된다.

`focusin`과 `focusout`는 `on<event>` 방식으로 핸들러를 추가하면 안 되고 `elem.addEventListener` 방식으로 핸들러를 추가해야 한다.

예시:
```html
<form id="form">
  <input type="text" name="surname" value="성">
  <input type="text" name="name" value="이름">
</form>

<style> .focused { outline: 1px solid red; } </style>

<script>
  form.addEventListener('focusin', () => form.classList.add('focused'));
  form.addEventListener('focusout', () => form.classList.remove('focused'));
</script>
```

## 요약
`focus`와 `blur` 이벤트는 요소가 포커스를 받을 때, 잃을 때 발생한다.

두 이벤트를 사용할 때는 다음을 유의해야 한다.
- `focus`와 `blur` 이벤트는 버블링되지 않는다. 대신 캡처링이나 `focusin`, `focusout`을 사용할 수 있다.
- 대부분의 요소는 기본적으로 포커스를 지원하지 않는다. 하지만 `tabindex`를 사용하면 포커스가 가능해진다.

현재 포커스된 요소는 `document.activeElement`로 확인할 수 있다.
