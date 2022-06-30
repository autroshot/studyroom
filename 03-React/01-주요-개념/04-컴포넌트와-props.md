# 컴포넌트와 props
컴포넌트를 사용하면 UI를 독립적이고 재사용 가능한 독립된 조각으로 나누고, 각 조각을 개별적으로 살펴볼 수 있다. 자세한 컴포넌트 API 레퍼런스는 [이곳](https://ko.reactjs.org/docs/react-component.html)에서 확인할 수 있다.

컴포넌트는 개념적으로 JavaScript 함수와 유사하다. 컴포넌트는 어떤 입력(props)을 받고, 그에 따라 화면에 무엇이 표시되는지를 기술하는 React 요소를 반환한다.

## 함수 컴포넌트와 클래스 컴포넌트
컴포넌트를 정의하는 가장 간단한 방법은 JavaScript 함수를 작성하는 것이다.

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

`Welcome` 함수는 데이터가 들어 있는 하나의 props(프로퍼티의 줄임말) 객체 인자를 받고 React 요소를 반환한다. 따라서 이 함수는 유효한 React 컴포넌트이다.

이러한 컴포넌트를 **함수 컴포넌트**라고 부른다.

또는 다음과 같이 ES6의 클래스를 사용해 컴포넌트를 정의할 수 있다.

```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

클래스 컴포넌트를 만드는 방법은 다음 섹션에서 알아볼 것이다.

React의 관점에서 볼 때 앞의 두 가지 유형의 컴포넌트는 동일하다.

## 컴포넌트 렌더링하기
React 요소는 일반 DOM 태그가 아닌 사용자 정의 컴포넌트로도 나타낼 수 있다.

```jsx
const element = <Welcome name="Sara" />;
```

React는 사용자 정의 컴포넌트를 나타내는 요소를 만나면, 요소의 JSX 속성과 자식을 하나의 객체(props)로 묶어서 해당 컴포넌트에 전달한다.

다음은 페이지에 `Hello, Sara`를 렌더링하는 예시이다.

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const root = ReactDOM.createRoot(document.getElementById('root'));
const element = <Welcome name="Sara" />;

root.render(element);
```

이 예시에서는 다음과 같은 일들이 일어난다.

1.  `<Welcome name="Sara" />`  요소로 `root.render()`를 호출한다.
2.  React는  `{ name: 'Sara' }`를 props 인수로 넘기며 `Welcome`  컴포넌트를 호출한다.
3.  호출된 `Welcome`  컴포넌트는 `<h1>Hello, Sara</h1>`  요소를 반환한다.
4.  React DOM은  `<h1>Hello, Sara</h1>`와 일치하도록 DOM을 효율적으로 업데이트한다.

> :warning: **주의: 컴포넌트의 이름은 항상 대문자로 시작한다.**
>
> React는 소문자로 시작하는 컴포넌트를 DOM 태그로 인식한다. 예를 들어  `<div />`는 HTML div 태그를 나타내지만,  `<Welcome />`은 컴포넌트를 나타내며 스코프 범위 안에  `Welcome`이 있어야 한다.
>
> 이 규칙에 대한 자세한 내용은  [이곳](https://ko.reactjs.org/docs/jsx-in-depth.html#user-defined-components-must-be-capitalized)에서 확인할 수 있다.
>

## 컴포넌트 합성하기
컴포넌트는 자신의 출력에서 다른 컴포넌트를 참조할 수 있다. 이는 모든 세부 단계에서 동일한 추상 컴포넌트를 사용할 수 있음을 의미한다. React 앱에서는 버튼, 폼, 다이얼로그, 화면 등의 모든 것들이 흔히 컴포넌트로 표현된다.

예를 들어 `Welcome`을 여러 번 렌더링하는 `App` 컴포넌트를 만들 수 있다.

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

일반적으로 새로운 React 앱은 최상위에 `App` 컴포넌트가 있다. 하지만 기존 앱에 React를 통합하는 경우에는 `Button`과 같은 작은 컴포넌트부터 시작해서 뷰(view) 계층의 상단으로 올라갈 수 있다.

## 컴포넌트 추출하기
***컴포넌트를 여러 개의 작은 컴포넌트로 나누는 것을 두려워하지 말아야 한다.***

다음 `Comment` 컴포넌트를 살펴보겠다.

```jsx
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

이 컴포넌트는 `author`(객체), `text`(문자열) 및 `date`(날짜)를 props로 받아서 소셜 미디어 웹 사이트의 코멘트를 표시한다.

이 컴포넌트는 중첩 구조로 이루어져 있어서 변경이 어려우며, 각 부분을 재사용하기도 힘들다.

먼저 컴포넌트에서 `Avatar`를 추출해 보겠다.

```jsx
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  );
}
```

`Avatar` 는 자신이 `Comment` 안에서 렌더링 된다는 것을 알 필요가 없다. 따라서 props의 이름을 `author`에서 `user`로 일반화했다.

**props의 이름은 사용될 컨텍스트(context)가 아닌 컴포넌트 자체의 관점에서 짓는 것이 좋다.**

다음으로 `UserInfo` 컴포넌트를 추출하겠다.

```jsx
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}
```

이제 `Comment`가 훨씬 단순해졌다.

```jsx
function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

처음에는 컴포넌트를 추출하는 작업이 지루해 보일 수 있다. 하지만 재사용 가능한 컴포넌트를 만들어 놓는 것은 큰 앱을 만들 때 두각을 나타낸다. UI 일부가 여러 번 사용되거나(`Button`, `Panel`, `Avatar`), 복잡한 UI가 있는 경우(`App`, `FeedStory`, `Comment`)에는 별도의 컴포넌트로 추출하는 게 좋다.

## props는 읽기 전용이다
**함수 컴포넌트나 클래스 컴포넌트는 자신의 props를 수정하면 안 된다.**

다음 `sum` 함수를 살펴보겠다.

```js
function sum(a, b) {
  return a + b;
}
```

이런 함수를  [순수 함수](https://en.wikipedia.org/wiki/Pure_function)라고 부른다. 입력값을 바꾸려 하지 않고, 동일한 입력값에 대해 항상 동일한 결과를 반환하기 때문이다.

반면에 다음 함수는 자신의 입력값을 변경하기 때문에 순수 함수가 아니다.

```js
function withdraw(account, amount) {
  account.total -= amount;
}
```

**모든 React 컴포넌트는 자신의 props를 다룰 때 반드시 순수 함수처럼 동작해야 한다.**

물론 애플리케이션 UI는 동적이며 시간에 따라 변화한다. 다음 장에서 배우는 상태()를 이용하면 컴포넌트는 위 규칙을 위반하지 않으면서 사용자 행동, 네트워크 응답에 따라 자신의 출력값을 변경할 수 있다.
