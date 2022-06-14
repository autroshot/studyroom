# getElement\*, querySelector\*로 요소 검색하기
## document.getElementById 혹은 id를 사용해 요소 검색하기
요소에 `id` 속성이 있으면 위치에 상관없이 메서드 `document.getElementById(id)`를 이용해 접근할 수 있다.
```html
<div id="elem">
  <div id="elem-content">Element</div>
</div>

<script>
  // 요소 얻기
  let elem = document.getElementById('elem');

  // 배경색 변경하기
  elem.style.background = 'red';
</script>
```
또는 `id` 속성값을 이름으로 갖는 전역 변수를 이용해 접근할 수 있다.
```html
<div id="elem">
  <div id="elem-content">Element</div>
</div>

<script>
  // 변수 elem은 id가 'elem'인 요소를 참조한다.
  elem.style.background = 'red';

  // id가 elem-content인 요소는 중간에 하이픈(-)이 있기 때문에 변수 이름으로 쓸 수 없다.
  // 이때는 대괄호(`[...]`)를 사용해서 window['elem-content']로 접근하면 된다.
</script>
```
그런데 이 전역 변수는 자바스크립트에서 동일한 이름을 가진 변수를 선언하면 덮어씌어진다.
```html
<div id="elem"></div>

<script>
  let elem = 5; // elem은 더이상 <div id="elem">를 참조하지 않고 5가 된다. 

  alert(elem); // 5
</script>
```
---
:warning: **전역 변수를 이용해 요소에 접근하면 안 된다.**

`id`에 대응하는 전역변수는 명세서에 있는 표준이긴 하지만 하위 호환성을 위해 남겨둔 동작이다.

이 방식은 이름이 충돌할 가능성이 있기 때문에 추천하지 않는다. HTML을 보지 않은 상황에서 코드만 보고 변수의 출처를 알기 힘들다는 단점도 있다.

요소의 출처가 명확한 경우에만 간결성을 위해 `id`를 사용해 요소에 직접 접근하는 방법을 사용할 예정이다.

**실무에서는 `document.getElementById`를 사용해야 한다.**

---
:information_source: **id는 중복되면 안 된다.**

`id`는 유일해야 한다. 문서 내 요소의 `id` 속성값은 중복되어선 안 된다.

같은 `id`를 가진 요소가 여러 개 있으면 `document.getElementById`같이 `id`를 이용해 요소를 검색하는 메서드의 동작이 예측 불가능해진다. 검색된 여러 요소 중 어떤 요소를 반환할지 판단하지 못해 임의의 요소가 반환된다.

---
:warning: **`anyNode.getElementById`는 불가능하다.**

`getElementById`는 문서 노드 `document`가 아닌 다른 노드에서 호출할 수 없다.

---

## querySelectorAll
`elem.querySelectorAll(css)`은 다재다능한 요소 검색 메서드이다. 이 메서드는 `elem`의 자식 요소 중 주어진 CSS 선택자에 대응하는 요소 모두를 반환한다.

다음 예시는 마지막 `<li>`요소 모두를 반환한다.
```html
<ul>
  <li>1-1</li>
  <li>1-2</li>
</ul>
<ul>
  <li>2-1</li>
  <li>2-2</li>
</ul>

<script>
  let elements = document.querySelectorAll('ul > li:last-child');

  for (let elem of elements) {
    alert(elem.innerHTML); // "1-2", "2-2"
  }
</script>
```
---
:information_source: **가상 클래스도 사용할 수 있다.**

querySelectorAll에는 `:hover`나 `:active` 같은 CSS 선택자의 가상 클래스(pseudo-class)도 사용할 수 있다. 

`document.querySelectorAll(':hover')`을 사용하면 마우스 포인터가 위에 있는(hover 상태인) 요소 모두를 담은 컬렉션이 반환된다. 이때 컬렉션은 DOM 트리 최상단에 위치한 `<html>`부터 가장 하단의 요소 순으로 채워진다.

---

## querySelector
`elem.querySelector(css)`는 주어진 CSS 선택자에 대응하는 요소 중 첫 번째 요소를 반환한다.

`elem.querySelectorAll(css)[0]`과 결과가 동일하다.  `elem.querySelectorAll(css)[0]`은 선택자에 해당하는 모든 요소를 검색해 첫 번째 요소만을 반환하고,  `elem.querySelector`는 해당하는 요소를 찾으면 검색을 멈춘다는 점에서 차이가 있다. 따라서 `elem.querySelector`가 더 빠르다. 또한 `querySelector`는 코드의 길이가 짧다는 장점도 있다.

## matches
[elem.matches(css)](http://dom.spec.whatwg.org/#dom-element-matches)는 요소 `elem`이 주어진 CSS 선택자와 일치하는지 여부를 판단한다. 일치하면 `true`, 아니라면 `false`를 반환한다.

요소가 담겨있는 배열 등을 순회해 원하는 요소만 걸러내고자 할 때 유용하다.
```html
<a href="http://example.com/file.zip">...</a>
<a href="http://ya.ru">...</a>

<script>
  for (let elem of document.body.children) {
    if (elem.matches('a[href$="zip"]')) {
      alert('CSS 선택자와 일치하는 요소: ' + elem.href);
    }
  }
</script>
```

## closest
부모 요소, 부모 요소의 부모 요소 등 DOM 트리에서 특정 요소의 상위에 있는 요소들은 <strong>조상(ancestor)</strong> 요소라고 한다.

메서드  `elem.closest(css)`는  `elem`  자기 자신을 포함하여 CSS 선택자와 일치하는 가장 가까운 조상 요소를 찾을 수 있게 도와준다.

`closest`메서드는 해당 요소부터 시작해 DOM 트리를 한 단계씩 거슬러 올라가면서 원하는 요소를 찾는다. CSS 선택자와 일치하는 요소를 찾으면, 검색을 중단하고 해당 요소를 반환한다.
```html run
<h1>목차</h1>
<div class="contents">
  <ul class="book">
    <li class="chapter">1장</li>
    <li class="chapter">2장</li>
  </ul>
</div>

<script>
  let chapter = document.querySelector('.chapter'); // LI

  alert(chapter.closest('.book')); // UL
  alert(chapter.closest('.contents')); // DIV

  alert(chapter.closest('h1')); // null(h1은 li의 조상 요소가 아니다)
</script>
```

## getElementsBy*
태그나 클래스 등을 이용해 원하는 노드를 찾아주는 메서드도 있다.

`querySelector`를 이용하는 것이 더 편리하고 문법도 짧아서, 요즘에는 이런 메서드를 쓰지 않는다. 오래된 스크립트에서 이 메서드를 볼 수 있다.
-   `elem.getElementsByTagName(tag)`: 주어진 태그에 해당하는 요소를 찾고, 대응하는 요소를 담은 컬렉션을 반환한다. 매개변수  `tag`에  `"*"`이 들어가면, 모든 태그가 검색된다.
-   `elem.getElementsByClassName(className)`: class 속성값을 기준으로 요소를 찾고, 대응하는 요소를 담은 컬렉션을 반환한다.
-   `document.getElementsByName(name)`: 아주 드물게 쓰이는 메서드로, 문서 전체를 대상으로 검색을 수행한다. 검색 기준은  `name`  속성값이고, 이 메서드 역시 검색 결과를 담은 컬렉션을 반환한다.

---
:warning: **`elements` 끝의 's'를 빠뜨리지 말아야 한다.**

`getElementsByTagName`를 실수로 `getElementByTagName`로 작성하곤 한다.

---
:warning: **요소 하나가 아닌 컬렉션을 반환한다.**

```js
// 동작하지 않는 코드
document.getElementsByTagName('input').value = 5;
```
input 요소 전체를 담은 컬렉션에 5를 할당하는 위 코드는 동작하지 않는다. 본래 의도는 컬렉션 내 요소에 값을 할당하는 것이었다. 원래 의도대로 코드를 수정하면 다음과 같다.
```js
// 아래 코드는 잘 동작한다.
document.getElementsByTagName('input')[0].value = 5;
```
---

## 살아있는 컬렉션
`'getElementsBy'`로 시작하는 모든 메서드는 **살아있는** 컬렉션을 반환한다. 문서에 변경이 있을 때마다 컬렉션이 자동 갱신되어 최신 상태를 유지한다.

다음 예시에서 컬렉션의 길이가 자동 갱신되는 것을 확인할 수 있다.
```html
<div>첫 번째 div</div>

<script>
  let divs = document.getElementsByTagName('div');
  alert(divs.length); // 1
</script>

<div>두 번째 div</div>

<script>
  alert(divs.length); // 2
</script>
```
반면, `querySelectorAll`은 **정적인** 컬렉션을 반환합니다. 컬렉션이 한 번 확정되면 더는 변경되지 않는다.
```html run
<div>첫 번째 div</div>

<script>
  let divs = document.querySelectorAll('div');
  alert(divs.length); // 1
</script>

<div>두 번째 div</div>

<script>
  alert(divs.length); // 1
</script>
```

## 요약
DOM에서 원하는 노드를 검색하게 해주는 주요 메서드는 다음과 같다.
<table>
<thead>
<tr>
<td>메서드</td>
<td>검색 기준</td>
<td>호출 대상이 요소가 될 수 있는지에 대한 여부</td>
<td>컬렉션 갱신 여부</td>
</tr>
</thead>
<tbody>
<tr>
<td><code>querySelector</code></td>
<td>CSS 선택자</td>
<td>✔</td>
<td>-</td>
</tr>
<tr>
<td><code>querySelectorAll</code></td>
<td>CSS 선택자</td>
<td>✔</td>
<td>-</td>
</tr>
<tr>
<td><code>getElementById</code></td>
<td><code>id</code></td>
<td>-</td>
<td>-</td>
</tr>
<tr>
<td><code>getElementsByName</code></td>
<td><code>name</code></td>
<td>-</td>
<td>✔</td>
</tr>
<tr>
<td><code>getElementsByTagName</code></td>
<td>태그나 <code>'*'</code></td>
<td>✔</td>
<td>✔</td>
</tr>
<tr>
<td><code>getElementsByClassName</code></td>
<td>class</td>
<td>✔</td>
<td>✔</td>
</tr>
</tbody>
</table>

실무에서는 `querySelector`나 `querySelectorAll`을 가장 많이 사용할 것이다. `getElementBy`로 시작하는 메서드는 보통 오래된 스크립트에서 만날 수 있는데, 일부 이 메서드가 꼭 필요한 상황에서 쓰이는 경우도 있다.

이 외에 알아두면 좋을 만한 메서드는 다음과 같다.

- `elem.matches(css)`: `elem`이 해당 CSS 선택자와 일치하는지 여부를 검사한다.
- `elem.closest(css)`: 해당 CSS 선택자와 일치하는 가장 가까운 조상 요소를 탐색한다. 이때, `elem` 자기 자신도 검색 대상에 포함된다.

노드의 부모-자식 관계를 확인할 수 있도록 도와주는 유용한 메서드도 있다.
- `elemA.contains(elemB)`는 `elemB`가 `elemA`의 후손이거나 `elemA == elemB`일 때, `true`을 반환한다.
