# JSX 소개
JSX는 JavaScript를 확장한 문법으로 React 요소(element)를 생성한다.

## JSX란?
React에서는 본질적으로 렌더링 로직이 UI 로직(이벤트, state)과 묶여있다고 본다.

React는 마크업과 로직을 모두 포함하는 **컴포넌트**라는 느슨하게 연결된 유닛으로 SoC(관심사의 분리)를 실현한다.

React에서 JSX를 반드시 사용해야 하는 것은 아니다. 하지만 대부분의 사람들은 JavaScript 코드 안에서 UI 관련 작업을 할 때 JSX가 시각적으로 더 도움이 된다고 생각한다. 또한 JSX를 사용하면 React는 도움이 되는 에러나 경고 메시지를 표시할 수 있다.

## JSX에 표현식 포함하기
아래 예시에서는 `name`이라는 변수를 중괄호로 감싸 JSX 안에 사용하였다.
```js
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;
```
JSX의 중괄호 안에는 모든 JavaScript 표현식을 넣을 수 있다. 예를 들어 `2 + 2`, `user.firstName`, `formatName(user)` 등이 가능하다.

```js
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);
```
가독성을 좋게 하기 위해 JSX를 여러 줄로 나눴다. 이때  자동 세미콜론 삽입을 피하기 위해 **괄호로 묶는 것**을 권장한다.

## JSX도 표현식이다
컴파일이 끝나면 JSX 표현식은 JavaScript **객체**로 인식된다.

따라서 JSX를 `if`문이나 `for` 반복문 안에서 사용하거나, 변수에 할당하거나, 인자로 넣거나, 함수의 반환값이 될 수 있다.

```js
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

## JSX로 속성 지정하기
따옴표를 사용해 문자열 리터럴을 속성으로 지정할 수 있다.

```js
const element = <a href="https://www.reactjs.org"> link </a>;
```

중괄호를 사용해 속성에 JavaScript 표현식을 넣을 수 있다.

```js
const element = <img src={user.avatarUrl}></img>;
```

속성에 JavaScript 표현식을 삽입할 때 중괄호를 따옴표로 감싸면 안 된다. 하나의 속성에 따옴표와 중괄호 모두를 사용할 수 없다.

---
:warning: **경고**

JSX는 HTML보다 JavaScript에 가깝기 때문에, React DOM은 HTML 속성 이름 대신 **카멜 표기법**을 사용한다.

예를 들어, `class`는 `className`가 되고 tabindex는 `tabIndex`가 된다.

---

## JSX로 자식 지정하기
태크 내부가 비어있다면 XML처럼 `/>` 를 이용해 바로 닫아줘야 한다.

```js
const element = <img src={user.avatarUrl} />;
```

 JSX 태그는 자식을 포함할 수 있다.
 
```js
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
);
```

## JSX는 주입 공격(injection attack)을 방지한다
JSX에 사용자 입력을 넣는 것은 **안전**하다.

```js
const title = response.potentiallyMaliciousInput;
// 이것은 안전하다.
const element = <h1>{title}</h1>;
```

기본적으로 React DOM은 JSX에 삽입된 모든 값을 렌더링하기 전에 이스케이프 처리한다. 이런 특성 덕분에  [XSS (cross-site-scripting)](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%9D%B4%ED%8A%B8_%EA%B0%84_%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8C%85)  공격을 방지할 수 있다.

## JSX는 객체를 나타낸다
Babel은 JSX를  `React.createElement()`  호출로 컴파일한다.

다음 두 예시는 동일하다.

```js
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```js
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

`React.createElement()`는 버그가 없는 코드를 작성하는 데 도움이 되는 몇 가지 검사를 수행한다. 하지만 기본적으로 하는 일은 다음과 같은 객체를 생성하는 것이다.

```js
// 이 구조는 단순화된 것이다.
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

이 객체를 <strong>React 요소(React element)</strong>라고 부른다. 화면에서 보고 싶은 것에 대한 설명이라고 볼 수 있다. React는 이 객체를 읽어서 DOM을 구성하고 최신 상태로 유지하는 데 사용한다.

---
:information_source: 팁

ES6 및 JSX 코드가 올바르게 표시되도록 편집기에 [Babel 언어 설정](https://babeljs.io/docs/en/next/editors)을 사용하는 것을 권장한다.

---

## JSX는 문법 설탕이다
각 JSX 요소는 `React.createElement(component, props, ...children)`를 호출하기 위한 문법 설탕(sugar syntax)이다. 따라서 JSX로 할 수 있는 모든 것은 순수 JavaScript로도 할 수 있다.
