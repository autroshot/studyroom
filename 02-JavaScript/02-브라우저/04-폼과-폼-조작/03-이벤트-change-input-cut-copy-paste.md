# 이벤트: change, input, cut, copy, paste
이번 챕터에서는 데이터가 변경될 때 실행되는 다양한 이벤트에 대해 다룰 것이다.

## 이벤트: change
`change` 이벤트는 요소 변경이 끝나면 발생한다.

텍스트 입력 요소인 경우에는 요소 변경이 끝날 때가 아니라 포커스를 잃을 때 이벤트가 발생한다.

텍스트 입력 요소 예시를 살펴보겠다. 텍스트 입력 필드에 글자를 입력하는 동안에는 이벤트가 발생하지 않는다. 하지만 버튼을 클릭하는 등의 동작을 통해 포커스를 다른 곳으로 옮기는 순간 `change` 이벤트가 발생한다.
```html
<input type="text" onchange="alert(this.value)">
<input type="button" value="버튼">
```
`select`와 `input type=checkbox`, `input type=radio`는 선택이 바뀌면 바로 이벤트가 발생한다. 
```html
<select onchange="alert(this.value)">
  <option value="">선택하세요.</option>
  <option value="1">옵션 1</option>
  <option value="2">옵션 2</option>
  <option value="3">옵션 3</option>
</select>
```

## 이벤트: input
`input` 이벤트는 사용자가 값을 수정할 때마다 발생한다.

키보드 이벤트와 달리 `input` 이벤트는 어떤 방법으로든 값을 변경할 때 발생한다. 마우스를 사용하여 글자를 붙여 넣거나 음성인식 기능을 사용해 글자를 입력할 때처럼 키보드가 아닌 다른 수단을 사용하여 값을 변경할 때도 `input` 이벤트가 발생한다.

예시:
```html
<input type="text" id="input"> oninput: <span id="result"></span>
<script>
  input.oninput = function() {
    result.innerHTML = input.value;
  };
</script>
```
수정이 일어날 때마다 이벤트를 실행하고 싶다면 `<input>` 이벤트가 가장 적절하다.

한편, `input` 이벤트는 화살표 키와 같이 값을 변경시키지 않는 키보드 입력이나 동작에는 반응하지 않는다.

---
:information_source: **그 무엇도 `oninput`를 막을 수 없다.**

`input` 이벤트는 값이 수정되자마자 발생한다.

이 이벤트는 `event.preventDefault()`을 사용해도 아무 효과가 없다.

---

## 이벤트: cut, copy, paste
`cut`, `copy`, `paste` 이벤트는 각각 값을 잘라내기·복사·붙여넣기할 때 발생한다.

세 이벤트는 [ClipboardEvent](https://www.w3.org/TR/clipboard-apis/#clipboard-event-interfaces) 클래스의 하위 클래스이며 잘라내기·복사·붙여넣기한 데이터에 접근할 수 있게 해준다.

`event.preventDefault()`를 사용해 작업을 중단할 수 있다. 이렇게 하면 아무것도 복사·붙여넣기되지 않는다.

예를 들어 다음 코드는 `cut/copy/paste` 이벤트를 모두 막고 잘라내기·복사·붙여넣기를 시도했던 텍스트를 보여준다.
```html
<input type="text" id="input">
<script>
  input.onpaste = function(event) {
    alert('paste: ' + event.clipboardData.getData('text/plain'));
    event.preventDefault();
  };

  input.oncut = input.oncopy = function(event) {
    alert(event.type + '-' + document.getSelection());
    event.preventDefault();
  };
</script>
```
`cut`과 `copy` 이벤트 핸들러 안에서 `event.clipboardData.getData(...)`을 사용하면 빈 문자열이 반환된다. 데이터가 아직 클립보드에 없기 때문이다. `event.preventDefault()`을 사용하면 아무것도 복사되지 않는다.

따라서 위의 예시에서는 `document.getSelection()`을 이용해 선택된 텍스트를 가져온다. 문서 선택에서 대한 자세한 내용은 [선택과 범위](https://javascript.info/selection-range) 챕터에서 확인할 수 있다.

텍스트뿐만 아니라 모든 것을 복사·붙여넣기 할 수 있다. 예를 들어 OS 파일 매니저에서 파일을 복사해 붙여넣을 수 있다.

이것이 가능한 이유는 `clipboardData`가 드래그 앤 드랍과 복사·붙여넣기에 많이 사용되는 `DataTransfer` 인터페이스를 구현하기 때문이다. 범위를 조금 벗어나지만 이 메서드는 [DataTransfer 명세서](https://html.spec.whatwg.org/multipage/dnd.html#the-datatransfer-interface)에서 찾을 수 있다.

또한 클립보드에 접근할 수 있는 비동기 API `navigator.clipboard`가 있다. Firefox에서는 지원하지 않는다. 자세한 내용은 명세서 [Clipboard API and events](https://www.w3.org/TR/clipboard-apis/)에서 확인 가능하다.

### 안전을 위한 제한
클립보드는 전역 OS 레벨이다. 사용자는 다양한 프로그램 사이를 전환하며 다른 항목을 복사·붙여넣기 할 수 있고, 브라우저 페이지는 이 모든 것을 볼 수 없다.

따라서 대부분의 브라우저는 복사·붙여넣기와 같은 특정 사용자 작업의 범위에서만 클립보드에 대한 원활한 읽기·쓰기 접근을 허용한다.

Firefox를 제외한 모든 브라우저에서 `dispatchEvent`로 '커스텀' 클립보드 이벤트를 만드는 것을 금지하고 있다. 그리고 명세서에도 이런 '인조' 이벤트가 클립보드에 대한 접근을 제공해서는 안 된다고 명시되어 있다.

`event.clipboardData`를 이벤트 핸들러에 저장하고 나중에 접근하는 것은 불가능하다.

다시 말하자면 `event.clipboardData`는 사용자에 의한 이벤트 핸들러 컨텍스트에서만 동작한다.

반면 [navigator.clipboard](https://www.w3.org/TR/clipboard-apis/#h-navigator-clipboard)는 최신 API로, 모든 컨텍스트에서 사용할 수 있다. 필요한 경우 사용자 권한을 요청한다. Firefox에서는 지원되지 않는다.

## 요약
데이터가 변경과 관련된 이벤트는 다음과 같다.

| 이벤트 | 설명 | 특이사항 |
|---------|----------|-------------|
| `change` | 값이 변경될 때 이벤트 발생 | 텍스트 입력의 경우 포커스를 잃을 때 실행 |
| `input` | 텍스트가 입력될 때마다 이벤트 발생 | `change`와 달리 즉시 실행 |
| `cut`, `copy`, `paste` | 잘라내기·복사·붙여넣기 할 때 이벤트 발생 | 브라우저 기본 동작을 막아 이벤트 실행을 막을 수 있음. `event.clipboardData` 프로퍼티를 사용하면 클립보드에 저장된 데이터를 읽고 쓸 수 있음 |
