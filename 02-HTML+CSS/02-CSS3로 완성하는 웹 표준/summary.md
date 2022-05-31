# 5. CSS 기초
## 스타일과 스타일 시트
스타일(style)은 문서의 겉모습을 지정한다.

스타일은 웹 문서의 내용과 상관없이 디자인만 바꾼다. 또한 사용 기기에 따라 모습이 바뀌는 문서를 만들 수 있다.

### 스타일 형식
```css
선택자 {
  속성: 속성 값;
  속성: 속성 값;
  ...
}
```

### 스타일 주석
```css
/* 한 줄 주석 */
```

### 스타일 시트
스타일 시트는 스타일 규칙들을 한 군데에 모아 놓은 것이다.

#### 내부 스타일 시트
```html
<!DOCTYPE html>
<html>
<head>
  <style>
    body {
      background-color: linen;
    }

    h1 {
      color: maroon;
      margin-left: 40px;
    } 
  </style>
</head>
<body>
  <h1>This is a heading</h1>
  <p>This is a paragraph.</p>
</body>
</html>
```

#### 외부 스타일 시트
외부 스타일 시트는 '.css' 파일 확장자를 사용한다.
```html
<!DOCTYPE html>
<html>
<head>
  <link rel="stylesheet" href="mystyle.css">
</head>
<body>
  <h1>This is a heading</h1>
  <p>This is a paragraph.</p>
</body>
</html>
```

#### 인라인 스타일
```html
<!DOCTYPE html>
<html>
<body>
  <h1 style="color:blue;text-align:center;">This is a heading</h1>
  <p style="color:red;">This is a paragraph.</p>
</body>
</html>
```
가급적 사용하면 안 된다.

## 주요 선택자
|선택자|스타일 적용 대상|
|---|---|
|전체 선택자 `*`|모든 요소|
|태그 선택자 `p`|특정 태그|
|클래스 선택자 `.`|특정 클래스|
|id 선택자 `#`|특정 아이디|
|그룹 선택자 `,`|나열한 선택자들|

## 캐스케이딩 스타일 시트(CSS)
### 캐스케이딩(Cascading)
캐스케이딩은 폭포를 의미하며, CSS는 폭포처럼 위에서 아래로 흐르는 스타일 시트라는 뜻이다.

스타일은 스타일 우선순위와 상속에 의해 결정된다.

### 스타일 우선순위
우선순위는 다음 세 가지에 의해 결정된다.

#### Importance: 중요도
1. 사용자의 시스템에 의해 만들어지는 사용자 스타일 시트
2. 개발자가 만든 스타일 중 `!important`가 붙은 스타일
3. 개발자가 만든 일반 스타일
4. 기본적인 브라우저 스타일 시트

#### Specificity: 적용 범위
스타일 적용 범위가 좁을수록 우선순위가 높다.
1.  인라인 스타일
2.  id 스타일
3.  클래스 스타일
4.  태그 스타일

#### Source Order
중요도와 적용 범위가 동일하다면 우선순위를 정하는 것은 소스에서의 순서이다. 소스에서 뒤에 있는 스타일이 앞에 있는 스타일을 덮어씌운다.

### 스타일 상속
스타일 시트에서는 자식 요소에서 별도로 스타일을 지정하지 않으면 부모 요소의 스타일이 자식 요소로 전달된다. 모든 스타일 속성이 상속되는 것은 아니며 상속이 되는 주요 속성은 다음과 같다.
-   visibility
-   opacity
-   font
-   color
-   line-height
-   text-align
-   white-space

## CSS3와 CSS 모듈
CSS3부터는 배경, 글꼴, 박스 모델 등 여러 기능을 주제별로 규약을 따로 만들었다. 이것을 'CSS 모듈'이라고 한다.

표준 규약이 아닌 속성들은 브라우저에 다른 방식으로 지원되기 때문에 속성 이름 앞에 접두사(prefix)를 붙여 브라우저별로 구분해야 한다.

# 6. 텍스트 관련 스타일
## 글꼴 관련 스타일
### font-family 속성 - 글꼴 지정하기
왼쪽부터 차례대로 사용자의 시스템에 설치된 글꼴을 찾는다.
```css
body {
  font-family: "맑은 고딕", 돋움, 굴림
}
```

### @font-face 속성 - 웹 폰트 사용하기
```css
@import url('https://fonts.googleapis.com/css2?family=Nanum+Gothic&display=swap'); 

.ng-font {
  font-family: "Nanum Gothic", 돋움;
}
```

### font-size 속성 - 글자 크기 조절하기
```css
font-size: <절대 크기> | <상대 크기> | <크기> | <백분율>
```
|속성 값|설명|
|---|---|
|<절대 크기>|브라우저에서 지정한 글자 크기이다. 사용할 수 있는 값은 `xx-small` \| `x-small` \| `small` \| `medium` \| `large` \| `x-large` \| `xx-large`이다.|
|<상대 크기>|부모 요소의 `font-size`를 기준으로 더 크게 표시하거나 작게 표시한다. 사용할 수 있는 값은 `larger` \| `smaller`이다.|
|<크기>|브라우저와 상관없이 글자 크기를 직접 지정한다.|
|<백분율>|부모 요소의 글자 크기를 기준으로 `%`를 지정한다.|

크기 단위는 대부분의 경우에는 rem을 사용하고 필요한 경우에만 em을 사용할 것을 추천한다.

### font-weight 속성 - 글자 굵기 지정하기
```css
font-weight: normal | bold
```

### font-style 속성 - 글자 스타일 지정하기
```css
font-style: normal | italic
```

### font 속성 - 글꼴 속성을 한꺼번에 묶어서 표현하기
```css
p.b {
  font: italic small-caps bold 12px/30px Georgia, serif;
}
```
`font-size`와 `line-height`는 `/`로 연결해 함께 사용한다.

## 텍스트 스타일
### color 속성 - 글자 색 지정하기

### text-decoration 속성 - 텍스트에 줄 표시하기
|속성 값|설명|
|---|---|
|none|기본값. 밑줄을 표시하지 않는다.|
|underline|밑줄을 표시한다.|
|overline|영역 위로 선을 그린다.|
|line-through|취소 선을 그린다.|

텍스트 링크에 생기는 밑줄을 없앨 때 다음과 같이 설정한다.
```css
a {
  text-decoration: none;
}
```

### text-shadow 속성 - 텍스트에 그림자 효과 추가하기

### white-space 속성 - 공백 처리하기

### letter-spacing 속성 - 텍스트 간격 조절하기

## 문단 스타일
### text-align 속성 - 텍스트 정렬하기
|속성 값|설명|
|---|---|
|left|왼쪽 정렬|
|right|오른쪽 정렬|
|center|가운데 정렬|
|justify|양쪽 정렬|

### line-height 속성 - 줄 간격 조절하기
```css
line-height: normal | <숫자> | <크기> | <백분율>
```
<숫자>는 글자 크기 대비 몇 배수인지를 지정한다.

## 목록 스타일
- list-style-type 속성 - 목록의 불릿과 번호 스타일 지정하기
- list-style-image 속성 - 불릿 대신 이미지 넣기
- list-style-position 속성 - 목록에 들여 쓰는 효과 내기
- list-style 속성 - 목록 속성 한꺼번에 표시하기

# 7. 색상과 배경을 위한 스타일
## 웹에서 색상 표현하기
### 16진수 표기법
`#` 기호 다음에 6자리의 16진수로 표시하는 것이다. 두 자리씩 묶어서 `#RRGGBB` 형식으로 표시한다.

### rgb와 rgba 표기법
```css
rgb(red 값, green 값, blue 값);
rgba(red 값, green 값, blue 값, alpha 값);
```
a는 알파(alpha)로 불투명도 값을 나타낸다.

rgb는 0~255, a는 0~1이 가능하다.

### hsl과 hsla 표기법
hsl은 차례대로 hue(색상), saturation(채도), lightness(밝기)를 나타낸다.
```css
hsla(360, 100%, 0%, 0.5)
```

### 색상 이름 표기법
사용할 수 있는 색상 이름은 [W3Schools](https://www.w3schools.com/colors/colors_names.asp)에서 확인할 수 있다.

## 배경 색과 배경 이미지
### background-color 속성 - 배경 색 지정하기
기본적으로 모든 웹 문서 요소는 배경이 투명하다.

### background-image 속성 - 웹 요소에 배경 이미지 넣기

# 8. CSS 박스 모델
## CSS와 박스 모델
### 블록 요소와 인라인 요소
블록 요소는 혼자 한 줄을 차지한다. 너비, 마진, 패딩 등을 이용해 크기나 위치를 지정하려면 블록 레벨 요소여야 한다.

인라인 요소는 줄을 차지하지 않는다. 한 줄에 여러 개의 인라인 요소가 올 수 있다. 인라인 요소는 너비나 높이, 패딩, 마진 등의 속성을 지정해도 무시된다.

|종류|해당 태그|
|---|---|
|블록 레벨 태그|`<p>`, `<h1>~<h6>`, `<ul>`, `<ol>`, `<div>`, `<blockquote>`, `<form>`, `<hr>`, `<table>`, `<fieldset>`, `<address>`|
|인라인 레벨 태그|`<img>`, `<object>`, `<br>`, `<sub>`, `<sup>`, `<span>`, `<input>`, `<textarea>`, `<label>`, `<button>`|

***인라인 요소 안에는 블록 요소를 넣을 수 없다.***

### 박스 모델(box model)
***블록 요소는 모두 박스 모델 요소이다.***

박스 모델은 실제 콘텐츠 영역, 박스와 콘텐츠 영역 사이의 여백인 패딩(padding), 박스의 테두리(border), 여러 박스 모델 사이의 여백인 마진(margin)으로 구성된다.

### width, height 속성 - 콘텐츠 영역의 크기

### display 속성 - 화면 배치 방법 결정하기
|속성값|설명|
|---|---|
|`block`|해당 요소를 블록 요소로 지정한다.|
|`inline`|해당 요소를 인라인 요소로 지정한다.|
|`inline-block`|요소를 인라인으로 배치하면서 블록 속성을 지정할 수 있다.|
|`none`|해당 요소는 화면에 표시되지 않는다. `visibility: hidden`과 비슷하지만 공간도 차지하지 않는다는 차이가 있다.|

## 테두리 관련 속성들
### border-style 속성 - 테두리 스타일 지정하기
자주 쓰는 속성값: none, solid, double

### border-width 속성 - 테두리 두께 지정하기
```css
border-top-width: <크기> | thin | medium | thick
border-right-width: <크기> | thin | medium | thick
border-bottom-width: <크기> | thin | medium | thick
border-left-width: <크기> | thin | medium | thick
border-width: <크기> | thin | medium | thick
```
`border-width` 속성에 값을 두 개 지정하면 위아래와 좌우의 테두리 두께가 지정되며, 값을 네 개 지정하면 위에서부터 시계 방향 순서로 지정한다. 이후의 속성에도 동일하게 적용된다.

### border-color 속성 - 테두리 색상 지정하기

### border 속성 - 테두리 스타일 묶어 지정하기

### border-radius 속성 - 박스 모서리 둥글게 만들기
```css
border-top-left-radius: <크기> | <백분율>
border-top-right-radius: <크기> | <백분율>
border-bottom-right-radius: <크기> | <백분율>
border-bottom-left-radius: <크기> | <백분율>
border-radius: <크기> | <백분율>
```
`border-radius` 속성은 `border-width` 속성과 사용법이 유사하다. 속성 값 네 개를 지정하면 왼쪽 위부터 시계 방향으로 지정한다.

### box-shadow 속성 - 선택한 요소에 그림자 효과 내기

## 여백을 조절하는 속성들
### margin 속성 - 요소 주변 여백 설정하기
`margin-left`와 `margin-right` 속성을 `auto`로 지정하면 요소를 중앙에 정렬할 수 있다.

### 마진 중첩(margin overlap)
요소를 세로로 배치할 때 마진과 마진이 만나면 마진 값이 큰 쪽으로 겹쳐진다. 이런 현상을 마진 중첩 또는 마진 상쇄(margin collapse)라고 부른다.

여러 요소를 세로로 배치할 때 맨 위나 맨 아래 마진에 비해 중간의 마진들이 너무 커지는 것을 방지하기 위해 이렇게 설계되었다.

가로 마진에 대해서는 마진 중첩이 발생하지 않는다.

### padding 속성 - 콘텐츠 영역과 테두리 사이의 여백 설정하기

# 9. CSS 레이아웃
## CSS 포지셔닝과 주요 속성들
### box-sizing 속성 - 박스 너비 기준 정하기
|속성 값|설명|
|---|---|
|content-box|기본값. width 속성 값을 콘텐츠 영역 너비 값으로 사용한다.|
|border-box|width 속성 값을 콘텐츠 영역에 테두리까지 포함한 박스 모델 전체 너비 값으로 사용한다.|

### float 속성 - 왼쪽이나 오른쪽으로 배치하기
`float` 속성을 이용해 요소가 어느 곳에 떠 있게 할지를 지정할 수 있다.

|속성 값|설명|
|---|---|
|left|해당 요소를 문서의 왼쪽으로 배치한다.|
|right|해당 요소를 문서의 오른쪽으로 배치한다.|
|none|좌우 어느 쪽으로도 배치하지 않는다.|

이미지와 텍스트를 나란히 표시할 때 많이 사용한다.

### clear 속성 - float 속성 해제하기
```css
clear: none | left | right | both
```
`float` 속성을 지정하면 이후의 요소에도 자동으로 같은 속성이 전달된다. `float` 속성이 전달되는 것을 막기 위해서는 `clear` 속성을 사용해야 한다.

`float: left`를 사용했다면 `clear: left`로 종료하고, `float: right`를 사용했다면 `clear: right`로 종료한다. `clear: both`는 `float` 속성 값에 상관없이 종료한다.

### position 속성 - 배치 방법 지정하기
|속성 값|설명|기준|
|---|---|---|
|static|기본값. 요소를 문서의 흐름에 맞춰 배치한다. 위치 조절은 `float` 속성을 사용하면 가능하다.||
|relative|이전 요소에 자연스럽게 연결해 배치하며 위치를 지정할 수 있다.|`static`일 때의 원래 위치|
|absolute|원하는 위치에 지정해 배치한다.|`position: relative`인 가장 가까운 부모 요소|
|fixed|지정한 위치에 고정해 배치한다. 화면에서 요소가 잘릴 수도 있다.|브라우저 창|

`static`을 제외한 나머지 속성 값에서는 좌표를 이용해 요소의 위치를 지정할 수 있다. 위치는 `top`, `bottom`, `left`, `right` 속성으로 지정하며 양수, 음수 모두 가능하다.

### visibility 속성 - 요소를 보이지 않게 하거나 겹치기
|속성 값|설명|
|---|---|
|visible|기본값. 화면에 요소를 표시한다.|
|hidden|화면에서 요소를 감춘다. 하지만 크기는 그대로 유지하기 때문에 배치에 영향을 미친다.|
|collapse|표의 행, 열, 행 그룹, 열 그룹 등에서 지정하면 서로 겹치도록 조절한다. 그 외의 요소에서 사용하면 `hidden`과 동일한 기능을 한다.|

### z-index 속성 - 요소 쌓는 순서 정하기
```css
z-index: <숫자>
```
`z-index` 값이 클수록 위에 쌓인다.

## 다단으로 편집하기
생략

## 표 스타일
### caption-side 속성 - 표 제목 위치 지정하기

### border 속성 - 표 테두리 스타일 지정하기

### border-collapse 속성 - 테두리 통합, 분리하기
`<table>`와 `<td>`에서 `border` 속성을 사용하면 두 테두리가 모두 표시된다. `border-collapse` 속성을 사용하면 두 테두리를 하나로 합칠 수 있다.

separate(기본값), collapse

### border-spacing 속성 - 인접한 셀 테두리 사이 거리 지정하기
해당 속성은 border-collapse: separate;일 경우에만 적용된다.

### empty-cells 속성 - 빈 셀의 표시 여부 지정하기
해당 속성은 border-collapse: separate;일 경우에만 적용된다.

show(기본값), hide

### width, height 속성 - 표 너비와 높이 지정하기

### table-layout 속성 - 콘텐츠에 맞게 셀 너비 지정하기
auto(기본값), fixed

### text-align 속성 - 셀 안에서 수평 정렬하기

### vertical-align 속성 - 셀 안에서 수직 정렬하기
`inline`이나 `inline-block`으로 배치한 요소의 세로 정렬 방법을 지정한다.

baseline(기본값), top, middle, bottom
