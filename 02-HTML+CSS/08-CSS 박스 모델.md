# 1. CSS와 박스 모델
## 블록 요소와 인라인 요소
블록 요소는 혼자 한 줄을 차지한다. 한 줄을 차지한다는 것은 해당 요소의 너비가 100%인 것을 의미하며 요소의 왼쪽이나 오른쪽에 다른 요소가 올 수 없다. 너비, 마진, 패딩 등을 이용해 크기나 위치를 지정하려면 블록 레벨 요소여야 한다. `<div>`와 `<p>`가 대표적인 블록 요소이다.

인라인 요소는 줄을 차지하지 않는다. 화면에 표시되는 콘텐츠만큼만 영역을 차지하고 나머지 공간에는 다른 요소가 올 수 있다. 따라서 한 줄에 여러 개의 인라인 레벨 요소를 표시할 수 있다. `<img>`와 `<strong>`가 대표적인 인라인 요소이다.

블록 요소와 인라인 요소의 분류는 다음과 같다.
|종류|해당 태그|
|---|---|
|블록 레벨 태그|`<p>`, `<h1>~<h6>`, `<ul>`, `<ol>`, `<div>`, `<blockquote>`, `<form>`, `<hr>`, `<table>`, `<fieldset>`, `<address>`|
|인라인 레벨 태그|`<img>`, `<object>`, `<br>`, `<sub>`, `<sup>`, `<span>`, `<input>`, `<textarea>`, `<label>`, `<button>`|

***인라인 요소 안에는 블록 요소를 넣을 수 없다.***

## 박스 모델(box model) - 박스 형태의 콘텐츠
***블록 요소는 모두 박스 모델 요소이다.***

박스 모델은 실제 콘텐츠 영역, 박스와 콘텐츠 영역 사이의 여백인 패딩(padding), 박스의 테두리(border), 여러 박스 모델 사이의 여백인 마진(margin)로 구성된다. 각각은 상하좌우 네 방향으로 나뉘어져 있어 네 방향의 스타일을 따로 설정할 수 있다.

<img width="708" alt="box-model" src="https://user-images.githubusercontent.com/95019875/162885541-dbbd3c4f-b116-4283-8ef9-f7e69319f29b.png">

## width, height 속성 - 콘텐츠 영역의 크기
|속성 값|설명|
|---|---|
|auto|기본값. 너비와 높이 값이 콘텐츠 양에 따라 자동으로 결정된다.|
|<크기>|너비나 높이 값을 `px`이나 `cm` 같은 단위와 함께 수치로 지정한다.|
|<백분율>|부모 요소를 기준으로 너비나 높이 값을 백분율(%)로 지정한다.|

## display 속성 - 화면 배치 방법 결정하기
display 속성을 사용하면 블록 요소를 인라인 요소로 바꾸거나 인라인 요소를 블록 요소로 바꿀 수 있다.

### block
해당 요소를 블록 요소로 지정한다.

### inline
해당 요소를 인라인 요소로 지정한다. 목록으로 수평 내비게이션을 만들 때 활용할 수 있다.
```html
<style>
nav ul li {
  display: inline;
}
</style>

<nav>
  <ul>
    <li><a href="#">애완견 종류</a></li>
    <li><a href="#">입양하기</a></li>
    <li><a href="#">건강 돌보기</a></li>
  </ul>
</nav>
```

### inline-block
요소를 인라인으로 배치하면서 블록 속성을 지정할 수 있다. 즉, 요소에 너비나 높이, 마진, float 같은 속성을 적용할 수 있다.
```html
<style>
nav ul li {
  display: inline-block;
  margin: 20px;
}
</style>

<nav>
  <ul>
    <li><a href="#">애완견 종류</a></li>
    <li><a href="#">입양하기</a></li>
    <li><a href="#">건강 돌보기</a></li>
  </ul>
</nav>
```

### none
해당 요소는 화면에서 아예 표시되지 않는다. `visibility: hidden;`도 비슷한 역할을 하는데 `visibility` 속성은 화면에서 감추기만 할 뿐 요소가 있는 공간은 그대로 차지하지만 `display: none;`은 아예 공간조차 차지하지 않는다.

### 기타 속성 값들
|속성 값|설명|
|---|---|
|inherit|상위 요소의 display 속성을 상속받는다.|
|table|블록 레벨의 표로 만든다.|
|inline-table|`<table>`와 같은 효과|
|table-row|`<tr>`와 같은 효과|
|table-row-group|`<tbody>`와 같은 효과|
|table-header-group|`<thead>`와 같은 효과|
|table-footer-group|`<tfoot>`와 같은 효과|
|table-column|`<col>`와 같은 효과|
|table-column-group|`<colgroup>`와 같은 효과|
|table-cell|`<td>`나 `<th>`와 같은 효과|
|table-caption|`<caption>`와 같은 효과|
|list-item|`<li>`와 같은 효과|
<br></br>
