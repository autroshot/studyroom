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
|<백분율>|부모 요소를 기준으로 너비나 높이 값을 백분율(`%`)로 지정한다.|

## display 속성 - 화면 배치 방법 결정하기
display 속성을 사용하면 블록 요소를 인라인 요소로 바꾸거나 인라인 요소를 블록 요소로 바꿀 수 있다.

### block
해당 요소를 블록 요소로 지정한다.

### inline
해당 요소를 인라인 요소로 지정한다. 인라인 요소는 너비나 높이, 패딩, 마진 등의 속성을 지정해도 무시된다.

다음과 같이 목록으로 수평 내비게이션을 만들 때 활용할 수 있다.
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
# 2. 테두리 관련 속성들
## border-style 속성 - 테두리 스타일 지정하기
|속성 값|설명|
|---|---|
|none|기본값. 테두리가 나타나지 않는다.|
|hidden|테두리가 나타나지 않는다. `border-collapse: collapse`일 경우, 다른 테두리도 표시되지 않는다.|
|solid|테두리를 실선으로 표시한다.|
|dashed|테두리를 짧은 선으로 표시한다.|
|dotted|테두리를 점선으로 표시한다.|
|double|테두리를 이중선으로 표시한다. 두 선 사이의 간격은 border-width 속성으로 지정한다.|
|groove|테두리를 창에 조각한 것처럼 표시한다. 홈이 파인 듯 입체적으로 보인다.|
|ridge|테두리를 창에서 튀어나온 것처럼 표시한다.|
|inset|`border-collapse: separate`일 경우, 전체 박스 테두리가 창에 박혀 있는 것처럼 표시되고 `border-collapse: collapse`일 경우, `groove`와 똑같이 표시된다.|
|outset|`border-collapse: separate`일 경우, 전체 박스 테두리가 창에서 튀어나온 것처럼 표시되고 `border-collapse: collapse`일 경우, `ridge`와 똑같이 표시된다.|

`border-collapse` 속성은 09-3장에서 자세히 배운다.

## border-width 속성 - 테두리 두께 지정하기
```css
border-top-width: <크기> | thin | medium | thick
border-right-width: <크기> | thin | medium | thick
border-bottom-width: <크기> | thin | medium | thick
border-left-width: <크기> | thin | medium | thick
border-width: <크기> | thin | medium | thick
```

`border-width` 속성에 값을 두 개 지정하면 위아래와 좌우의 테두리 두께가 지정되며, 값을 네 개 지정하면 시계 방향(top, right, bottom, left)으로 지정한다.
```css
.box1 {
  border-width: 2px;
}

.box2 {
  border-width: thick thin;
}

.box3 {
  border-width: 5px 10px 15px 20px;
}
```

## border-color 속성 - 테두리 색상 지정하기
```css
border-top-color: <색상>
border-right-color: <색상>
border-bottom-color: <색상>
border-left-color: <색상>
border-color: <색상>
```

## border 속성 - 테두리 스타일 묶어 지정하기
```css
border-top: <두께> <색상> <스타일>
border-right: <두께> <색상> <스타일>
border-bottom: <두께> <색상> <스타일>
border-left: <두께> <색상> <스타일>
border: <두께> <색상> <스타일>
```

```css
h1 {
  border-bottom: 3px solid #ccc;
}

p {
  border: 3px dotted black;
}
```

## border-radius 속성 - 박스 모서리 둥글게 만들기
```css
border-top-left-radius: <크기> | <백분율>
border-top-right-radius: <크기> | <백분율>
border-bottom-right-radius: <크기> | <백분율>
border-bottom-left-radius: <크기> | <백분율>
border-radius: <크기> | <백분율>
```
`border-radius` 속성은 `border-width` 속성과 사용법이 유사하다. 속성 값 네 개를 지정하면 왼쪽 위부터 시계 방향으로 지정한다.

|속성 값|설명|
|---|---|
|<크기>|둥글게 처리할 반지름 크기를 `px`이나 `em` 같은 단위와 함께 수치로 표시한다.|
|<백분율>|현재 요소의 크기를 기준으로 둥글게 처리할 반지름 크기를 `%`로 지정한다.|

### 타원 형태로 둥글게 만들기
```css
border-top-left-radius: <가로 크기> <세로 크기>
border-top-right-radius: <가로 크기> <세로 크기>
border-bottom-right-radius: <가로 크기> <세로 크기>
border-bottom-left-radius: <가로 크기> <세로 크기>
border-radius: <가로 크기> <세로 크기>
```
`border-radius` 속성에서는 가로 크기와 세로 크기 사이에 `/`을 넣어 구분한다.

## box-shadow 속성 - 선택한 요소에 그림자 효과 내기
```css
box-shadow: none | <그림자 값> [, <그림자 값>];
            <그림자 값> = <수평 거리> <수직 거리> <흐림 정도> <번짐 정도> <색상> inset
```

|속성 값|설명|
|---|---|
|<수평 거리>|필숫값. 그림자의 수평 offset 값이다. 양수 값이 요소의 오른쪽이다.|
|<수직 거리>|필숫값. 그림자의 수직 offset 값이다. 양수 값이 요소의 아래쪽이다.|
|<흐림 정도>|그림자의 흐림 정도(blur radius)를 지정한다. 기본값은 `0`으로 가장 진한 그림자를 표시한다. 값이 커질수록 부드러운 그림자를 표시한다.|
|<번짐 정도>|그림자가 번지는 정도를 지정한다. 양수 값을 사용하면 그림자가 모든 방향으로 퍼져 나간다. 음수 값을 사용할 수 있으며 기본값은 `0`이다.|
|<색상>|그림자의 색상을 지정한다. 여러 개의 색상을 지정할 수 있으며 기본값은 현재 글자의 색이다.|
|inset|그림자를 박스 안쪽으로 그린다.|

<br></br>
# 3. 여백을 조절하는 속성들
## margin 속성 - 요소 주변 여백 설정하기
```css
margin-top: <크기> | <백분율> | auto
margin-right: <크기> | <백분율> | auto
margin-bottom: <크기> | <백분율> | auto
margin-left: <크기> | <백분율> | auto
margin: <크기> | <백분율> | auto
```
`margin` 속성은 `border-width` 속성과 사용법이 유사하다.

|속성 값|설명|
|---|---|
|<크기>|너비나 높이 값을 `px`이나 `cm` 같은 단위와 함께 수치로 지정한다. 기본값은 `0`이다.|
|<백분율>|부모 요소를 기준으로 너비나 높이 값을 백분율(`%`)로 지정한다.|
|auto|`display` 속성에서 지정한 값에 맞게 적절한 값을 자동으로 지정한다.|

`margin-left`와 `margin-right` 속성을 `auto`로 지정하면 요소를 중앙에 정렬할 수 있다.

## 마진 중첩(margin overlap)
요소를 세로로 배치할 때 마진과 마진이 만나면 마진 값이 큰 쪽으로 겹쳐진다. 이런 현상을 마진 중첩 또는 마진 상쇄(margin collapse)라고 부른다.

여러 요소를 세로로 배치할 때 맨 위나 맨 아래 마진에 비해 중간의 마진들이 너무 커지는 것을 방지하기 위해 이렇게 설계되었다.

가로 마진에 대해서는 마진 중첩이 발생하지 않는다.

## padding 속성 - 콘텐츠 영역과 테두리 사이의 여백 설정하기
```css
padding-top: <크기> | <백분율> | auto
padding-right: <크기> | <백분율> | auto
padding-bottom: <크기> | <백분율> | auto
padding-left: <크기> | <백분율> | auto
padding: <크기> | <백분율> | auto
```
margin 속성과 사용법이 유사하다.
