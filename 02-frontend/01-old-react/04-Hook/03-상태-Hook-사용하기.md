# 상태 Hook 사용하기

[Hook 소개](https://github.com/autroshot/studyroom/blob/main/03-React/01-docs/02-Hook/01-Hook%20%EC%86%8C%EA%B0%9C.md#hook-%EC%86%8C%EA%B0%9C)에서 다음 예시를 봤었다.

```jsx
import React, { useState } from 'react';

function Example() {
  // "count"라는 새로운 상태 변수를 선언한다.
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

이 코드를 같은 기능을 하는 클래스 예시와 비교하며 Hook에 대해 알아볼 것이다.

## 같은 기능을 하는 클래스 예시

React에서 클래스를 사용해 봤다면 아래의 코드가 익숙할 것이다.

```jsx
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}
```

상태는 `{ count: 0 }`으로 시작해서 사용자가 버튼을 클릭할 때 `this.setState()`를 호출해서 `state.count`를 증가시킨다. 이 클래스를 계속 예시로 사용할 것이다.

## Hook과 함수 컴포넌트

참고로 React의 함수 컴포넌트는 이렇게 생겼다.

```jsx
const Example = (props) => {
  // 여기서 Hook을 사용할 수 있다.
  return <div />;
}
```

또는 이렇게 생겼다.

```jsx
function Example(props) {
  // 여기서 Hook을 사용할 수 있다.
  return <div />;
}
```

이전에는 이 컴포넌트를 '무상태 컴포넌트'로 알았을 것이다. 이제 이들은 React 상태를 사용하는 능력을 얻기 때문에 '함수 컴포넌트'라는 이름이 어울린다.

Hook은 클래스 안에서 동작하지 않는다. 그러나 클래스를 작성하는 대신 사용할 수 있다.

## Hook은 무엇인가?

새로운 예시는 React에서 `useState` Hook을 가져오면서 시작한다.

```jsx
import React, { useState } from 'react';

function Example() {
  // ...
}
```

**Hook은 무엇인가?** Hook은 React 기능을 연결해주는(hook into) 특별한 함수이다. 예를 들어 `useState`는 함수 컴포넌트에 React 상태를 추가해주는 Hook이다.

**Hook은 언제 사용할까?** 함수 컴포넌트를 작성하다 보면 상태의 필요성을 느낄 때가 있다. 이전에는 함수를 클래스로 변환했어야 했다. 이제는 기존의 함수 컴포넌트에서 Hook을 이용할 수 있게 되었다.

---

:information_source: **알림**

컴포넌트에서 Hook을 사용할 수 있는 위치에 대한 몇 가지 특별한 규칙이 있다. [Hook 규칙]()에서 알아볼 것이다.

---

## 상태 변수 선언하기

클래스에서는 생성자 안의 `this.state = { count: 0 }`으로 `count` 상태를 `0`으로 초기화했다.

```jsx
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }
```

함수 컴포넌트에는 `this`가 없기 때문에 `this.state`로 값을 할당하거나 읽을 수 없다. 대신 컴포넌트 안에서 `useState` Hook을 직접 호출한다.

```jsx
import React, { useState } from 'react';

function Example() {
  // "count"라는 새로운 상태 변수를 선언한다.
  const [count, setCount] = useState(0);
```

### `useState` 호출은 무슨 일을 할까?

'상태 변수'를 선언한다. 변수명은 `count`가 아닌 어떤 것도 가능하다. 이것은 함수 호출 간에 특정한 값을 보존하는 방법이다. `useState`는 클래스의 `this.state`와 동일한 기능을 제공한다.

일반적으로 함수가 종료되면 변수는 사라진다. 그러나 상태 변수는 React에 의해 보존된다.

### `useState`의 인수로 어떤 것을 넘겨줘야 할까?
`useState()` Hook의 유일한 인수는 초기 상태이다. 클래스와 달리 객체 이외의 값도 상태에 넣을 수 있다. 숫자나 문자열로 충분하다면 해당 타입의 값을 할당하면 된다.

위의 예시에서는 사용자가 클릭을 몇 번 했는지 알려면 숫자로 충분하다. 그래서 변수의 초기 상태로 `0`을 건네준 것이다.

만약 상태에 두 개의 다른 값을 저장하고 싶다면 `useState()`를 두 번 호출하면 된다.

### `useState`는 무엇을 반환할까?

현재 상태와 이를 업데이트하는 함수를 반환한다. 그래서 `const [count, setCount] = useState()`라고 작성한 것이다. 한 쌍으로 얻는다는 점만 제외하면 클래스의 `this.state.count`, `this.setState`와 비슷하다.

이제 `useState` Hook이 무엇을 하는지 알았으니 예시가 잘 이해될 것이다.

```jsx
import React, { useState } from 'react';

function Example() {
  // "count"라는 새로운 상태 변수를 선언한다.
  const [count, setCount] = useState(0);
```

`count`라는 상태 변수를 선언하고 `0`으로 설정한다. React는 상태의 현재 값을 리렌더링 간에 기억하고 최신 값을 함수에게 알려준다. 현재 `count`를 업데이트하려면 `setCount`를 호출하면 된다.

> :information_source: **알림**
>
> 왜 `createState`가 아닌 `useState`로 이름을 지었는지 궁금할 수 있다.
>
> 상태는 컴포넌트가 처음 렌더링될 때만 만들어지기 때문에 'create'는 적절하지 않다.
>
> 다음 렌더링 동안 `useState`는 현재 상태를 제공한다. 그러지 않으면 상태라는 이름을 붙일 이유가 없을 것이다.
>
> Hook의 이름이 항상 `use`로 시작하는 이유도 있다. 자세한 이유는 [Hook 규칙]()에서 알아볼 것이다.
>

## 상태 읽기

클래스에서는 현재 `count`를 표시하려면 `this.state.count`을 읽는다.

```jsx
  <p>You clicked {this.state.count} times</p>
```

함수에서는 직접 `count`를 사용할 수 있다.

```jsx
  <p>You clicked {count} times</p>
```

## 상태 업데이트하기

클래스에서는 `count` 상태를 업데이트하려면 `this.setState()`가 필요했다.

```jsx
  <button onClick={() => this.setState({ count: this.state.count + 1 })}>
    Click me
  </button>
```

함수에서는 이미 `setCount`와 `count`가 있기 때문에 `this`가 필요 없다.

```jsx
  <button onClick={() => setCount(count + 1)}>
    Click me
  </button>
```

## 요약

다음 코드를 한 줄씩 살펴보며 지금까지 배운 내용을 요약해 보겠다.

```jsx
import React, { useState } from 'react'; // (1)

function Example() {
  const [count, setCount] = useState(0); // (2)

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}> // (3)
        Click me
      </button>
    </div>
  );
}
```

1. React에서 `useState` Hook을 가져온다. `useState` Hook은 함수 컴포넌트에 지역 상태를 만들어 준다.

2. `Example` 컴포넌트 안에서 `useState` Hook을 호출해 새로운 상태 변수를 선언한다. Hook은 한 쌍의 값을 반환한다. 버튼 클릭 횟수를 저장할 것이므로 변수에는 `count`라는 이름을 붙인다. `useState`의 유일한 인수에 `0`을 넘겨줘서 상태를 0으로 초기화한다.

    두 번째 반환 값은 함수이다. 이 함수는 `count`를 업데이트하는 기능을 제공하므로 `setCount`라는 이름을 붙인다.

3. 사용자가 클릭하면 새로운 값과 함께 `setCount`가 호출된다. 그리고 나면 React는 새 `count` 값을 건네주며 `Example` 컴포넌트를 리렌더링할 것이다.

### 팁: 복수의 상태 변수 사용하기

`[something, setSomething]`와 같이 상태 변수를 선언하는 것은 유용하다. 여러 개의 상태 변수를 사용할 때 쉽게 다른 이름을 붙일 수 있다.

```jsx
function ExampleWithManyStates() {
  // 복수의 상태 변수를 선언한다.
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
```

위의 컴포넌트에는 `age`, `fruit`, `todos`라는 지역 변수가 있고 개별적인 업데이트가 가능하다.

```jsx
function handleOrangeClick() {
  // this.setState({ fruit: 'orange' })와 비슷하다.
  setFruit('orange');
}
```

**여러 개의 상태 변수를 사용하지 않아도 된다.** 상태 변수는 객체나 배열도 가질 수 있으므로 연관된 데이터를 묶는 것이 여전히 가능하다. 그러나 클래스의 `this.setState`와 달리 상태 변수를 업데이트하는 것은 병합이 아니라 항상 대체해 버린다.

독립적인 상태 변수를 분리하는 것에 대한 추가적인 제안은 [자주 묻는 질문](https://ko.reactjs.org/docs/hooks-faq.html#should-i-use-one-or-many-state-variables)에서 확인할 수 있다.
