# 1. CSS 포지셔닝과 주요 속성들
## box-sizing 속성 - 박스 너비 기준 정하기
|속성 값|설명|
|---|---|
|content-box|기본값. width 속성 값을 콘텐츠 영역 너비 값으로 사용한다.|
|border-box|width 속성 값을 콘텐츠 영역에 테두리까지 포함한 박스 모델 전체 너비 값으로 사용한다.|

![box-sizing](https://user-images.githubusercontent.com/95019875/163221527-4fc7f888-c783-483e-b9e3-003ea92c2064.png)

## float 속성 - 왼쪽이나 오른쪽으로 배치하기
`float` 속성을 이용해 요소가 어느 곳에 떠 있게 할지를 지정할 수 있다.

|속성 값|설명|
|---|---|
|left|해당 요소를 문서의 왼쪽으로 배치한다.|
|right|해당 요소를 문서의 오른쪽으로 배치한다.|
|none|좌우 어느 쪽으로도 배치하지 않는다.|

이미지와 텍스트를 나란히 표시할 때 많이 사용한다.

다음과 같이 CSS를 지정해 여러 요소를 나란히 표시하는 것도 가능하다.
```css
div {
  float: left;
  padding: 15px; 
}

.div1 {
  background: red;
}

.div2 {
  background: yellow;
}

.div3 {
  background: green;
}
```

## clear 속성 - float 속성 해제하기
```css
clear: none | left | right | both
```
`float` 속성을 지정하면 이후의 요소에도 자동으로 같은 속성이 전달된다. `float` 속성이 전달되는 것을 막기 위해서는 `clear` 속성을 사용해야 한다.

`float: left`를 사용했다면 `clear: left`로 종료하고, `float: right`를 사용했다면 `clear: right`로 종료한다. `clear: both`는 `float` 속성 값에 상관없이 종료한다.
```css
.box1{
  background: #ffd800;
  float: left;
}

// box1에 이어서 배치된다.
.box2 {
  background: #0094ff;
  float: left;
}

// float 속성을 지정하지 않아서 box1, box2와 겹쳐서 표시된다.
.box3 {
  background: #00ff21;
}

// clear 속성을 사용했기 때문에 다음 줄에 표시된다.
.box4 {
  background: #a874ff;
  clear: both;
}
```

## position 속성 - 배치 방법 지정하기
|속성 값|설명|기준|
|---|---|---|
|static|기본값. 요소를 문서의 흐름에 맞춰 배치한다. 위치 조절은 `float` 속성을 사용하면 가능하다.||
|relative|이전 요소에 자연스럽게 연결해 배치하며 위치를 지정할 수 있다.|`static`일 때의 원래 위치|
|absolute|원하는 위치에 지정해 배치한다.|`position: relative`인 가장 가까운 부모 요소|
|fixed|지정한 위치에 고정해 배치한다. 화면에서 요소가 잘릴 수도 있다.|브라우저 창|

`static`을 제외한 나머지 속성 값에서는 좌표를 이용해 요소의 위치를 지정할 수 있다. 위치는 `top`, `bottom`, `left`, `right` 속성으로 지정하며 양수, 음수 모두 가능하다.
```css
div.fixed {  
  position: fixed;  
  bottom: 0;  
  right: 0;  
  width: 300px;  
  border: 3px solid #73AD21;  
}
```

## visibility 속성 - 요소를 보이지 않게 하거나 겹치기
|속성 값|설명|
|---|---|
|visible|기본값. 화면에 요소를 표시한다.|
|hidden|화면에서 요소를 감춘다. 하지만 크기는 그대로 유지하기 때문에 배치에 영향을 미친다.|
|collapse|표의 행, 열, 행 그룹, 열 그룹 등에서 지정하면 서로 겹치도록 조절한다. 그 외의 요소에서 사용하면 `hidden`과 동일한 기능을 한다.|

## z-index 속성 - 요소 쌓는 순서 정하기
```css
z-index: <숫자>
```
`z-index` 값이 클수록 위에 쌓인다.

`z-index` 값을 따로 지정하지 않을 경우, 맨 처음 요소는 `1`의 값을 가지며 그 이후의 요소는 값이 점점 커진다.
<br></br>
# 2. 다단으로 편집하기
다단 관련 속성은 브라우저별 접두사를 붙여야 한다.

## column-width - 단의 너비 고정하고 다단 구성하기
단의 너비를 고정하는 방법은 화면이 커지면 단의 개수가 많아지고 화면이 좁아지면 단의 개수가 줄어든다.

|속성 값|설명|
|---|---|
|auto|기본값. `column-count` 같은 다른 속성에 따라 단의 너비가 자동 계산된다.|
|<크기>|단 너비를 직접 지정한다.|

## column-count 속성 - 단의 개수 고정하고 다단 구성하기
브라우저 창의 너비에 따라 단의 너비가 달라진다.

|속성 값|설명|
|---|---|
|auto|기본값. `column-width` 같은 다른 속성에 따라 단의 개수가 자동 계산된다.|
|<크기>|단의 개수를 직접 지정한다. 자연수를 사용한다.|

## column-gap 속성 - 단과 단 사이 여백 지정하기
구분선을 넣으면 구분선도 이 여백 안에 들어간다.

|속성 값|설명|
|---|---|
|normal|기본값. 여백을 자동으로 지정한다. W3C에서 권장하는 여백은 `1em`이다.|
|<크기>|단과 단 사이의 여백을 숫자로 지정한다.|

## column-rule 속성 - 구분선의 색상, 스타일, 너비 지정하기
```html
column-rule-color: <색상>
column-rule-style: none | hidden | dotted | dashed | solid | double | groove | ridge | inset | outset
column-rule-rule-width: <크기> | thin | medium | thick
column-rule: <너비> <스타일> <색상>
```

`column-rule-style` 속성에서 사용할 수 있는 값은 08-2장의 `border-style` 속성의 값과 동일하다.

## break-after 속성 - 다단 위치 지정하기
```html
break-before: column | avoid-column
break-inside: column | avoid-column
break-after: column | avoid-column
```

|속성|단을 나누는 위치|
|---|---|
|break-before|해당 요소의 앞|
|break-inside|해당 요소의 안|
|break-after|해당 요소의 뒤|

|속성 값|설명|
|---|---|
|column|단을 나눈다.|
|avoid-column|단을 나누지 않는다.|

## column-span 속성 - 여러 단을 하나로 합치기
|속성 값|설명|
|---|---|
|none|기본값. 단을 합치지 않는다.|
|all|전체 단을 하나로 합친다.|

단을 일부만 합치는 것은 불가능하다.
<br></br>
# 3. 표 스타일
## caption-side 속성 - 표 제목 위치 지정하기
|속성 값|설명|
|---|---|
|top|기본값. 캡션을 표의 윗부분에 표시한다.|
|bottom|캡션을 표의 아랫부분에 표시한다.|

## border 속성 - 표 테두리 스타일 지정하기
`border` 속성에 대해서는 08-2장에서 자세한 내용을 확인할 수 있다.

## border-collapse 속성 - 테두리 통합, 분리하기
`<table>`와 `<td>`에서 `border` 속성을 사용하면 두 테두리가 모두 표시된다. `border-collapse` 속성을 사용하면 두 테두리를 하나로 합칠 수 있다.

|속성 값|설명|
|---|---|
|separate|기본값. 테두리를 따로 표시한다.|
|collapse|테두리를 하나로 합쳐 표시한다.|

```css
table, td, th {
  border: 3px solid red;
}

#table1 {
  border-collapse: collapse;
  border-color: blue;
}
```

## border-spacing 속성 - 인접한 셀 테두리 사이 거리 지정하기
해당 속성은 `border-collapse: separate;`일 경우에만 적용된다.
```css
border-spacing: <크기>
```
속성 값으로 하나를 지정하면 수평 거리와 수직 거리가 한꺼번에 지정되고, 값이 두 개이면 각각 수평 거리와 수직 거리가 지정된다.

## empty-cells 속성 - 빈 셀의 표시 여부 지정하기
해당 속성은 `border-collapse: separate;`일 경우에만 적용된다.
|속성 값|설명|
|---|---|
|show|기본값. 빈 셀 주위에 테두리를 그려 빈 셀을 표시한다.|
|hide|빈 셀에 테두리를 그리지 않고 비워둔다.|

## width, height 속성 - 표 너비와 높이 지정하기
너비나 높이를 따로 지정하지 않으면 셀 안의 내용에 따라 너비와 높이가 자동으로 결정된다.

## table-layout 속성 - 콘텐츠에 맞게 셀 너비 지정하기
|속성 값|설명|
|---|---|
|auto|기본값. 셀 내용에 따라 셀 너비가 달라진다.|
|fixed|셀 너비를 고정한다.|

`table-layout: fixed`로 설정했을 때, 셀 내용이 너비보다 길어지면 내용이 셀 밖으로 튀어나오는 문제가 발생한다. 이때, `word-break: break-all`을 사용하면 이 문제를 해결할 수 있다.

## text-align 속성 - 셀 안에서 수평 정렬하기
06-3장의 일반 텍스트 정렬과 동일하다.

## vertical-align 속성 - 셀 안에서 수직 정렬하기
`inline`이나 `inline-block`으로 배치한 요소의 세로 정렬 방법을 지정한다.
|속성 값|설명|
|---|---|
|baseline|기본값. 기준선을 부모 요소의 기준선에 맞춘다.|
|sub|기준선을 부모 요소의 아래 첨자 위치에 맞춘다.|
|super|기준선을 부모 요소의 위 첨자 위치에 맞춘다.|
|top|요소의 윗부분을 부모 요소의 윗부분에 맞춘다.|
|middle|요소의 중앙 부분을 부모 요소의 기준선에서 소문자 x 높이의 반만큼 올려서 맞춘다.|
|bottom|요소의 아랫부분을 부모 요소의 아랫부분에 맞춘다.|
|text-top|요소의 윗부분을 부모 요소 글꼴의 윗부분에 맞춘다.|
|text-bottom|요소의 아랫부분을 부모 요소 글꼴의 아랫부분에 맞춘다.|
|<길이 값>|`0px`를 기준으로 값만큼 기준선을 옮긴다. 양수면 기준선 위로, 음수면 기준선 아래로 옮긴다.|
|<백분율 값>|`0%`를 기준으로 line-height의 백분율 값만큼 옮긴다. <길이 값>과 동일하게 음수도 가능하다.|

표의 셀에서는 `top`, `middle`, `bottom`을 많이 사용한다.
