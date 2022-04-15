# 1. HTML5 문서
**시맨틱 태그는 의미가 있는 태그**를 말한다.

의미가 없는 태그의 대표적인 예로는 `<div>`, `<span>`가 있다. 반면 `<form>`, `<table>`, `<article>`는 모두 의미가 있는 태그다.

## 시맨틱 태그가 사용된 HTML5 문서
다음은 HTML5의 시맨틱 태그로 만든 문서의 예시이다.
```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="utf-8">
  <title>내가 처음 만드는 HTML 문서</title>
</head>
<body>
  <header>
    <h1>애완견 종류</h1>
    <nav>
      <ul>
        <li><a href="#">애완견 종류</a></li>
        <li><a href="#">입양하기</a></li>
        <li><a href="#">건강돌보기</a></li>
        <li><a href="#">더불어살기</a></li>
      </ul>
    </nav>
  </header>
  <section>
    <h2>활달한 강아지</h2>
    <article>
      <h3>요크셔테리어</h3>
      <p>생기있고 활달한 성질을 가지고 있으며...</p>
      ...
    </article>
  </section>
  <footer>
    <p>published by funnycom</p>
  </footer>
</body>
</html>
```

## 왜 시맨틱 태그로 레이아웃을 만들어야 할까?
시맨틱 태그를 사용한 문서와 사용하지 않은 문서는 겉보기에 똑같다. 하지만 실제로 웹 브라우저에서 처리할 때는 큰 차이가 존재한다.

시맨틱 태그로 작성된 문서는 태그만 보고도 어느 부분이 제목인지, 메뉴인지, 내용인지 쉽게 알 수 있다. 따라서 사이트를 검색할 때 필요한 내용을 빠르고 정확하게 찾을 수 있다.

웹 접근성 측면에서도 시맨틱 태그는 중요한 역할을 한다. 또한 어떤 장치에서든 문서를 똑같이 해석할 수 있다는 장점이 있다.
<br></br>
# 2. 문서 구조를 위한 HTML5 시맨틱 태그
시맨틱 태그로 만든 문서의 구조는 보통 다음과 같다.
![시맨틱 태그](https://user-images.githubusercontent.com/95019875/163502884-481b4f78-beb5-47f1-b075-f579c7824e1a.png)

## \<header> - 머리말
헤더는 주로 페이지 맨 위쪽이나 왼쪽에 위치한다. 헤더의 내용으로는 `<form>`를 사용해 검색 창을 넣거나 `<nav>`를 사용해 사이트 메뉴를 넣는다.

`<head>`와 혼동하기 쉬우므로 주의해야 한다.

## \<nav> - 문서를 연결하는 내비게이션 링크
내비게이션 메뉴뿐만 아니라 푸터의 사이트 링크 모음에서도 많이 사용된다.

## \<section> - 주제별 콘텐츠 영역
콘텐츠를 주제별로 묶을 때 사용한다.

## \<article> - 콘텐츠 내용
실제 내용이 이 안에 들어간다.

## \<aside> - 본문 이외의 내용
```html
<!DOCTYPE html>
<html>
<head>
  <style>
  aside {
    width: 30%;
    padding-left: 15px;
    margin-left: 15px;
    float: right;
    font-style: italic;
    background-color: lightgray;
  }
</style>
</head>
<body>
  <p>My family and I visited...</p>
  <aside>
    <p>The Epcot center is a theme park...</p>
  </aside>
  <p>My family and I visited...</p>
  <p>My family and I visited...</p>
</body>
</html>
```

## \<iframe> - 외부 문서 삽입하기
`<iframe>`는 **인라인 프레임을 삽입하는 태그**이다. **인라인 프레임(inline frame)은 웹 문서 안에 다른 웹 문서를 가져와 표시하는 것**을 말한다.
```html
<iframe src="삽입할 문서 주소" [속성="속성 값"]></iframe>
```

|속성|설명|
|---|---|
|src|프레임에 표시할 문서의 주소를 지정한다.|
|width|인라인 프레임의 너비이다. 픽셀이나 백분율 값으로 지정한다.|
|height|인라인 프레임의 너비이다. 픽셀이나 백분율 값으로 지정한다.|
|name|인라인 프레임의 이름이다.|
|seamless|프레임의 테두리를 없애 마치 본문의 일부처럼 보이게 만든다. 속성 값은 없다.|

## \<footer> 제작 정보와 저작권 정보
주로 웹 문서의 마지막에 위치한다.

## \<address> - 사이트 제작자 정보, 연락처 정보
보통 `<footer>` 안에서 사용된다.
```html
<address>  
Written by <a href="mailto:webmaster@example.com">Jon Doe</a>.<br>  
Visit us at:<br>  
Example.com<br>  
Box 564, Disneyland<br>  
USA  
</address>
```

## \<div>는 언제 사용할까?
HTML5에서 `<div>`를 사용하면 안 되는 것은 아니다. 여전히 CSS를 적용할 때 많이 사용된다.
<br></br>
# 3. IE8 이하 버전에서 시맨틱 태그 사용하기
## 새로운 시맨틱 태그 지원 상황
[caniuse 사이트](https://caniuse.com/)에서 브라우저별 지원 태그나 기능을 확인할 수 있다.

## IE8 이하에서 시맨틱 태그 지원하기
가장 일반적인 방법은 html5shiv를 이용하는 것이다.

### 1. CSS에서 블록 레벨로 정의하기
브라우저는 자신이 인식하지 못하는 태그를 만나면 인라인 태그로 취급한다. 따라서 다음과 같이 해당 태그를 블록 태그로 설정한다.
```html
header, section, nav, article, footer {
  display: block;
}
```

### 2. 시맨틱 태그 직접 정의하기
자바스크립트를 이용해 직접 태그를 정의할 수 있다.
```html
<script>
document.createElement('article');
document.createElement('section');
document.createElement('aside');
...
</script>
```

### 3. HTML5 Shiv 사용하기
[링크](https://github.com/aFarkas/html5shiv)에서 html5shiv.js 파일을 받아 원하는 폴더에 넣어서 사용한다.

```html
<!--[if lt IE 9]>
  <script src="js/html5shiv.js"></script>
<![endif]-->
```
위 코드는 브라우저가 IE9 이전 버전일 경우 해당 스크립트를 실행하라는 뜻이다.
