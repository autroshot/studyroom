# 1. HTML과의 첫 만남
HTML(HyperText Markup Language)

태그(tag) - 마크업의 수단
```html
<!DOCTYPE html> // 문서 유형
<html lang="ko"> // 문서의 시작과 끝
<head> // 브라우저에게 정보를 줌
  <meta charset="UTF-8"> // 인코딩 방식
  <meta http-equiv="X-UA-Compatible" content="IE=edge"> // Explorer 호환
  <meta name="viewport" content="width=device-width, initial-scale=1.0"> // 모바일 기기 호환
  <title>내가 처음 만드는 HTML 문서</title> // 문서 제목으로 브라우저의 제목 표시줄과 즐겨찾기의 이름이 된다. 텍스트로만 구성되어야 하며 길고 서술적이어야 한다.
</head>
<body> // 모든 콘텐츠가 들어가는 곳
  <h1>시간이란...</h1>
  <p>내일 죽을 것처럼 오늘을 살고<br>
  영원히 살 것처럼 내일을 꿈꾸어라</p>
  <img src="images/first.jpg">
</body>
</html>
```
특수 문자나 특수 기호를 표시하려면 [엔티티 코드](https://dev.w3.org/html5/html-author/charref)를 사용한다. 한자 키를 이용한 특수문자로도 입력 가능하다.

## 주석
- HTML - \<!-- 주석 내용 -->
- CSS - /* 주석 내용 */

# 2. 텍스트 관련 태그들
## 블록 태그

|태그|설명|
|---|---|
|\<h*n*>|제목 표시. `<h1>` ~ `<h6>`|
|\<p>|단락|
|\<br>|줄 바꿈|
|\<hr>|수평 줄|
|\<pre>|코드 그대로 표시|

## 인라인 태그

|태그|설명|
|---|---|
|\<strong>, \<b>|굵게 표시|
|\<em>, \<i>|이탤릭체|
|\<mark>|형광펜 효과|
|\<span>|영역 묶기|
|\<small>|작게 표시|
|\<s>|취소선|
|\<u>|밑줄|

## 목록 태그
### 순서 없는 목록
```html
<ul>
  <li>내용</li>
  <li>내용</li>
  ...
</ul>
```
### 순서 있는 목록
```html
<ol>
  <li>내용</li>
  <li>내용</li>
  ...
</ol>
```
### 설명 목록
```html
<dl>
  <dt>제목</dt>
  <dd>설명</dd>
  <dd>설명</dd>
  <dt>제목</dt>
  <dd>설명</dd>
  <dd>설명</dd>
  ...
</ol>
```

## 표 태그
```html
<table>
  <thead>
    <tr>
      <th>제목 셀1</th>
      <th>제목 셀2</th>
      <th>제목 셀3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>내용 1행 1열</th>
      <th>내용 1행 2열</th>
      <th>내용 1행 3열</th>
    </tr>
    <tr>
      <th>내용 2행 1열</th>
      <th>내용 2행 2열</th>
      <th>내용 2행 3열</th>
    </tr>
  </tbody>
  <tfoot>
    ...
  </tfoot>
</table>
```

# 3. 이미지와 하이퍼링크
## 이미지 태그
```html
<img src="경로" [alt="대체 텍스트" width="너비" height="높이"]>
```

## 링크 태그
```html
<a href="링크할 주소">텍스트<a>
<a href="링크할 주소"><img src="파일 경로"><a>
```
`target="_blank"`을 이용해 링크가 새 창에서 열리게 할 수 있다.

## 앵커
앵커를 이용해 화면을 원하는 위치로 이동시킬 수 있다.
```html
<태그 id="앵커 이름">텍스트 또는 이미지</태그>
...
<a href="#앵커 이름">텍스트 또는 이미지</a>
```

# 4. 폼 관련 태그
## 폼 태그
```html
<form name="register" method="get" action="register">여러 폼 요소</form>
```

## 레이블 태그
`<label>`로 폼 요소에 이름표를 붙인다.
```html
<label for="id 이름">레이블</label>
<input id="id 이름">
```

## 입력 태그
```html
<input type="유형">
```
자주 쓰는 유형값 - hidden, text, search, tel, email, password, number, reset, button

자주 쓰는 속성 - value, autofocus, placeholder, readonly, required, disabled, size

## 드롭다운 목록
```html
<select name="cars" id="cars" size="화면에 표시될 목록 개수">  
  <option value="volvo" selected>Volvo</option>  
  <option value="saab">Saab</option>  
  <option value="mercedes">Mercedes</option>  
  <option value="audi">Audi</option>  
</select>
```

- 여러 줄 텍스트 입력 태그: `<textarea>`

## 버튼 태그
- `type` 속성값: submit, reset, button
- `type` 속성은 항상 명시한다.
