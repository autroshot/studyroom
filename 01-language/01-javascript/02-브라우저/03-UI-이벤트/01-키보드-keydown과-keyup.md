# 키보드: `keydown`과 `keyup`

키보드에 대해 알아보기 전에 최신 기기에는 무언가를 입력하는 다른 방법이 있다는 점에 유의하세요. 예를 들어 음성 인식(특히 모바일 장치에서)을 사용하거나 마우스로 복사, 붙여넣기를 사용할 수 있습니다.

따라서 `<input>` 필드에 대한 입력을 추적하려면 키보드 이벤트만으로는 충분하지 않습니다. 모든 수단에 의한 `<input>` 필드의 변화를 추적할 수 있는 `input`이라는 이벤트가 별도로 존재합니다. 그리고 `input` 이벤트가 더 나은 선택이 될 수 있습니다. 이에 대해서는 나중에 [이벤트: change, input, cut, copy, paste](https://ko.javascript.info/events-change-input) 장에서 다룰 것입니다.

키보드 이벤트는 키보드 동작(가상 키보드 포함)을 처리할 때 사용해야 합니다. 예를 들어 화살표 키 `Up`과 `Down`, 또는 단축키(키 조합 포함)에 반응할 수 있습니다.


## 시험대

키보드 이벤트를 더 잘 이해하기 위해 [시험대](https://ko.javascript.info/article/keyboard-events/keyboard-dump/)를 사용할 수 있습니다.

텍스트 필드에서 여러 키 조합을 시도해 보세요.


## `Keydown`과 `keyup`

`keydown` 이벤트는 키를 눌렀을 때 발생하고, `keyup` 이벤트는 키를 놓을 때 발생합니다.

### `event.code`와 `event.key`

이벤트 객체의 `key` 프로퍼티는 문자를 가져올 수 있는 반면, 이벤트 객체의 `code` 프로퍼티는 **물리적 키 코드**를 가져올 수 있습니다.

예를 들어 `Z`를 `Shift`를 사용하거나 사용하지 않고 누를 수 있습니다. 그러면 소문자 `z`와 대문자 `Z`, 두 가지 문자가 나옵니다.

`event.key`는 문자와 정확히 일치하며 대소문자를 구분합니다. 반면 `event.code`는 대소문자를 구분하지 않습니다.

| 키 | `event.key` | `event.code` |
| --------- | --------------- | ------------ |
| `Z` | `z` (소문자) | `KeyZ` |
| `Shift+Z` | `Z` (대문자) | `KeyZ` |

사용자가 다른 언어를 사용한다면 `Z` 대신 완전히 다른 문자가 됩니다. 그 문자가 `event.key`의 값이 됩니다. `event.code`는 `KeyZ`로 항상 동일합니다.

`KeyZ`와 기타 키 코드

모든 키는 키보드 위치에 의존하는 코드를 갖습니다. 키 코드는 [UI 이벤트 코드 명세서](https://www.w3.org/TR/uievents-code/)에서 확인할 수 있습니다.

예를 들어:
- 문자 키는 `"Key<letter>"` 코드를 갖음 - `KeyA`, `KeyB` 등
- 숫자 키는 `"Digit<number>"` 코드를 갖음 - `Digit0`, `Digit1` 등
- 특수 키는 자신의 이름으로 코딩됨 - `Enter`, `Backspace`, `Tab` 등

키보드 레이아웃은 여러 가지가 존재하며 각 레이아웃에 대한 키 코드를 명세서에서 확인할 수 있습니다.

자세한 코드는 [명세서의 영숫자 섹션](https://www.w3.org/TR/uievents-code/#key-alphanumeric-section)을 읽거나, 위의 [시험대](#keyboard-test-stand)에서 키를 눌러서 확인하세요.

대소문자 문제: `keyZ`가 아니라 `KeyZ`

당연해 보이지만 개발자들이 자주 하는 실수입니다.

오타를 주의하세요. `keyZ`가 아니라 `KeyZ`입니다. `event.code=="keyZ"`와 같은 검사는 작동하지 않습니다. `Key`의 첫 글자는 대문자여야 합니다.

`Shift`나 `F1` 같이 문자가 아닌 키는 어떨까요? 이 키들은 `event.key`가 `event.code`와 거의 동일합니다.

| 키 | `event.key` | `event.code` |
|--------------|-------------|--------------|
| `F1` | `F1` | `F1` |
| `Backspace` | `Backspace` | `Backspace` |
| `Shift` | `Shift` | `ShiftRight` or `ShiftLeft` |

`event.code`는 어떤 키가 눌렸는지 정확히 알려줍니다. 예를 들어 대부분의 키보드에는 `Shift`가 두 개(왼쪽 `Shift`과 오른쪽 `Shift`) 있습니다. `event.code`는 정확히 어떤 것이 눌렸는지 알려주고, `event.key`는 키의 **의미**(`Shift`)만을 알려줍니다.

예를 들어 단축키 `Ctrl+Z`(맥은 `Cmd+Z`)를 처리하고 싶다고 가정해 보겠습니다. 대부분의 텍스트 편집기는 '실행 취소' 작업을 수행합니다. `keydown`에 리스너를 설정하고 어떤 키가 눌렸는지 확인할 수 있습니다.

여기에는 딜레마가 있습니다. 리스너에서 `event.key`와 `event.code` 중에서 어떤 값을 확인해야 할까요?

`event.key`의 값은 문자이고 언어에 따라 변합니다. 방문자가 OS에서 여러 언어를 사용하고 그 사이를 전환하는 경우, 동일한 키가 다른 문자를 제공합니다. 따라서 항상 동일한 값을 주는 `event.code`를 확인하는 것이 합리적입니다.

예시:

```js
document.addEventListener('keydown', function(event) {
  if (event.code == 'KeyZ' && (event.ctrlKey || event.metaKey)) {
    alert('Undo!')
  }
});
```

그러나 `event.code`에는 문제가 있습니다. 키보드 레이아웃이 다르면 동일한 키가 다른 문자를 가질 수 있습니다.

예를 들어 다음은 미국 레이아웃(QWERTY)과 독일어 레이아웃(QWERTZ)의 모습입니다.

keyboard image

동일한 키에 대해 미국 레이아웃은 `Z`, 독일어 레이아웃은 `Y`(문자가 교환됨)입니다.

독일어 레이아웃에서는 `Y`를 누를 때 `event.code`가 `KeyZ`일 것입니다.

코드에서 `event.code == 'KeyZ'`를 확인한다면, 독일어 레이아웃에서는 `Y`를 누를 때 이 테스트를 통과할 것입니다.

정말 이상하게 들립니다. [명세서](https://www.w3.org/TR/uievents-code/#table-key-code-alphanumeric-writing-system)에 이 동작이 명시되어 있습니다.

따라서 `event.code`는 예기치 않은 레이아웃에 대해 잘못된 문자와 일치할 수 있습니다. 다른 레이아웃의 동일한 문자가 다른 물리적 키에 매핑되어 다른 코드로 이어질 수 있습니다. 다행히 이런 동작은 (위에서 봤듯이) `keyA`, `keyQ`, `keyZ`와 같은 일부 코드에서만 발생하며, `Shift`와 같은 특수 키에서는 발생하지 않습니다. [명세서](https://www.w3.org/TR/uievents-code/#table-key-code-alphanumeric-writing-system)에서 목록을 확인할 수 있습니다.

레이아웃에 종속된 문자를 안정적으로 추적하고 싶다면 `event.key`가 더 나은 방법일 수 있습니다.

반면 `event.code`는 물리적 키 위치에 바인딩되어 방문자가 언어를 바꿔도 항상 동일하게 유지된다는 이점이 있습니다. 따라서 `event.code`에 의존하는 단축키는 언어를 바꿔도 잘 작동합니다.

레이아웃에 따라 키를 처리하고 싶나요? `event.key`를 사용하세요.

아니면 언어를 바꾼 후에도 단축키가 작동하기를 원하나요? `event.code`가 더 나을 수 있습니다.

## 자동 반복

키를 충분히 오랫동안 누르고 있으면 **자동 반복(auto-repeat)**이 시작됩니다. 자동 반복은 `keydown`을 반복적으로 트리거하고 키를 놓으면 마침내 `keyup`이 발생합니다. 따라서 여러 개의 `keydown`과 하나의 `keyup`이 발생하는 것이 일반적입니다.

자동 반복으로 트리거된 이벤트의 경우, 이벤트 객체의 `event.repeat` 프로퍼티가 `true`로 설정됩니다.


## 기본 동작

키보드로 시작할 수 있는 동작이 많기 때문에 키보드 기본 동작은 다양합니다.

예를 들어:

- 문자가 화면에 나타남 (가장 명확한 결과)
- 문자가 삭제됨 (`Delete` 키)
- 페이지가 스크롤됨 (`PageDown` 키)
- 브라우저가 '페이지 저장' 대화상자를 엶 (`Ctrl+S`)
- ...등등

`keydown`의 기본 동작을 막으면 OS 기반의 특수 키를 제외한 대부분의 동작을 취소할 수 있습니다.

OS 기반의 특수 키의 예로는 윈도우에서의 `Alt+F4`가 있습니다. 이는 현재 브라우저 창을 닫습니다. 그리고 자바스크립트에서 이 기본 동작을 막는 방법은 없습니다.

다음의 `<input>`은 전화번호를 예상하므로 숫자, `+`, `()`, `-`를 제외한 키는 받지 않습니다.

```html
<script>
function checkPhoneKey(key) {
  return (key >= '0' && key <= '9') || key == '+' || key == '(' || key == ')' || key == '-';
}
</script>
<input *!*onkeydown="return checkPhoneKey(event.key)"*/!* placeholder="Phone, please" type="tel">
```

`Backspace`, `Left`, `Right`, `Ctrl+V`와 같은 특수 키가 입력에서 작동하지 않습니다. 이는 `checkPhoneKey`가 너무 엄격해서 생기는 부작용입니다.

약간 느슨하게 만들면 다음과 같습니다.


```html
<script>
function checkPhoneKey(key) {
  return (key >= '0' && key <= '9') || key == '+' || key == '(' || key == ')' || key == '-' ||
    key == 'ArrowLeft' || key == 'ArrowRight' || key == 'Delete' || key == 'Backspace';
}
</script>
<input onkeydown="return checkPhoneKey(event.key)" placeholder="Phone, please" type="tel">
```

이제 화살표와 삭제가 잘 작동합니다.

하지만 여전히 마우스의 '우클릭 + 붙여넣기'를 사용하여 무엇이든 입력할 수 있습니다. 필터를 100% 신뢰할 수 없습니다.

대부분의 경우에는 문제가 없기 때문에 그대로 둘 수도 있습니다. 혹은 `input` 이벤트(수정 후에 트리거 됨)를 추적하는 것이 대안이 될 수 있습니다. `input` 이벤트에서 새 값을 확인하고 유효하지 않다면 해당 값을 강조하거나 수정할 수 있습니다.

## 레거시

과거에는 `keypress` 이벤트와 이벤트 객체의 `keyCode`, `charCode`, `which` 프로퍼티가 존재했습니다.

이것들은 브라우저 비호환 문제가 너무 많았기 때문에 명세서 개발자는 모든 것을 폐지하고 (이 장의 앞에서 설명한) 새롭고 현대적인 이벤트를 만들었습니다. 브라우저가 계속 지원하므로 이전 코드는 여전히 작동하지만, 이전 이벤트와 프로퍼티는 더 이상 사용할 필요가 전혀 없습니다.

## 요약

키를 누르면 기호 키나 특수 키(`Shift`, `Ctrl` 등)에 해당하는 키보드 이벤트가 항상 생성됩니다. 유일한 예외는 노트북 키보드에 가끔 존재하는 `Fn` 키입니다. OS보다 낮은 수준에서 구현되는 경우가 많기 때문에 해당하는 키보드 이벤트가 없습니다.

키보드 이벤트:

- `keydown` - 키를 누를 때 (키를 오래 누르면 자동 반복됨)
- `keyup` - 키를 놓을 때

주요 키보드 이벤트 프로퍼티:

- `code` - 키 코드(`KeyA`, `ArrowLeft` 등). 키보드 키의 물리적 위치에 따라 다름
- `key` - 문자(`A`, `a` 등). `Esc`와 같이 문자가 아닌 키는 보통 `code`와 동일한 값을 갖음

과거에는 키보드 이벤트가 폼 필드에서 사용자 입력을 추적하는 데 사용되기도 했습니다. 입력이 다양한 소스에서 올 수 있기 때문에 이 방식은 신뢰할 수 없습니다. 모든 입력을 처리하기 위한 `input`과 `change` 이벤트가 있습니다. (나중에 [이벤트: change, input, cut, copy, paste](https://ko.javascript.info/events-change-input) 장에서 다룸) 이 이벤트는 복사, 붙여넣기 또는 음성 인식을 포함한 모든 종류의 입력 후에 트리거됩니다.

정말 키보드가 필요할 때 키보드 이벤트를 사용해야 합니다. 단축키나 특수 키에 반응하는 것이 그 예입니다.