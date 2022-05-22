# submit 이벤트와 메서드
`submit` 이벤트는 폼을 제출할 때 트리거 되는데, 주로 폼을 서버로 전송하기 전에 유효성 검사를 하거나 폼 전송을 취소할 때 사용한다.

한편 `form.submit()` 메서드는 자바스크립트로 폼 전송을 할 때 사용한다. 동적으로 폼을 생성하고 서버에 제출할 때 사용한다.

## submit 이벤트
폼을 전송하는 방법은 크게 두 가지가 있다.
1. `<input type="submit">`이나 `<input type="image">` 클릭하기
2. input 필드에서 엔터 키 누르기

두 방법 모두 폼의 `submit` 이벤트를 트리거한다. 이벤트 핸들러에서는 데이터를 체크하고, 데이터에 문제가 있으면 에러를 출력하고 `event.preventDefault()`를 호출하곤 한다. 이렇게 되면 폼은 서버에 전송되지 않는다.

---
:information_source: **`submit`과 `click`의 관계**
input 필드에서 엔터 키를 눌러 폼을 전송하면 `<input type="submit">`에 있는 `click` 이벤트가 트리거된다. 클릭을 하지 않았는데도 `click` 이벤트가 트리거되는 것이다.
```html
<form onsubmit="return false">
 <input type="text" size="30" value="여기에 포커스를 준 다음에 Enter 키 누르기">
 <input type="submit" value="제출" onclick="alert('클릭 이벤트가 트리거 되었습니다!')">
</form>
```
---

## submit 메서드
`form.submit()`을 호출하면 폼을 서버에 수동으로 제출할 수 있다.

이때 `submit` 이벤트는 생성되지 않는다. 개발자가 `form.submit()`을 호출했다면 스크립트에서 이미 필요한 모든 조치를 끝내서 이벤트가 필요없다고 가정하기 때문이다.

이런 submit 메서드의 특징은 다음과 같이 폼을 직접 만들고 전송하고 싶을 때 사용할 수 있다.
```js
let form = document.createElement('form');
form.action = 'https://google.com/search';
form.method = 'GET';

form.innerHTML = '<input name="q" value="테스트">';

// 폼을 제출하려면 반드시 폼이 문서 안에 있어야 한다.
document.body.append(form);

form.submit();
```
