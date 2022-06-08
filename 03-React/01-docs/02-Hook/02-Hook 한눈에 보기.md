# Hook 한눈에 보기

Hook은 클래스를 작성하지 않아도 상태와 기타 React 기능을 사용할 수 있게 해준다.

## 📌 상태 Hook

이 예시는 카운터를 렌더링한다. 버튼을 클릭하면 값이 증가한다.

```js
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

여기서 `useState`가 바로 Hook이다. 함수 컴포넌트 안에서 `useState`를 호출하면 컴포넌트에 지역 상태가 추가된다. 리렌더링되어도 이 상태는 보존된다. `useState`는 현재 상태값과 상태를 업데이트할 수 있는 함수를 반환한다. 이벤트 핸들러를 비롯한 다양한 곳에서 이 함수를 사용할 수 있다. `useState`는 클래스의 `this.setState`와 비슷하지만 이전 상태와 새로운 상태를 병합하지 않는다는 차이가 있다.

`useState`의 유일한 인수는 초기 상태이다. 위의 예시에서 카운터는 0부터 시작하므로 인수에 `0`을 넣었다. `this.state`와 달리 Hook의 상태는 객체 이외의 값도 허용한다. 초기 상태 인수는 첫 렌더링에서만 사용된다.

#### 복수의 상태 변수 선언하기

하나의 컴포넌트 안에서 여러 개의 상태 Hook을 사용할 수 있다.

```js
function ExampleWithManyStates() {
  // 복수의 상태 변수를 선언한다.
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
  // ...
}
```

[배열 구조 분해(destructuring)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#%EB%B0%B0%EC%97%B4_%EA%B5%AC%EC%A1%B0_%EB%B6%84%ED%95%B4)  문법을 이용하면 `useState`로 선언한 상태 변수에 다른 이름을 붙일 수 있다. 이 이름은 `useState` API의 일부가 아니다. 대신 React는 `useState`를 여러 번 호출하면 모든 렌더링 중에 동일한 순서로 호출한다고 가정한다.

#### Hook은 무엇인가?

Hook은 함수 컴포넌트에 React의 상태와 생명주기 기능을 연결하는(hook into) 함수이다. Hook은 클래스 안에서 동작하지 않는다. Hook을 이용하면 클래스 없이 React를 사용할 수 있다.

기존의 컴포넌트를 밤을 새가며 다시 작성하는 것은 권장하지 않는다. 원한다면 새 컴포넌트에 Hook을 사용할 수 있다.

React는  `useState`  같은 내장 Hook을 몇 가지 제공한다. 컴포넌트 간에 상태 관련 로직을 재사용하기 위해 자신만의 Hook을 만드는 것도 가능하다.

## ⚡️ 부작용(effect) Hook

React 컴포넌트에서 네트워크 요청을 하거나, 구독을 하거나, DOM을 직접 조작해 봤을 것이다.  이 작업들을 부작용(side effect)이라고 부른다. 다른 컴포넌트에 영향을 줄 수 있고 렌더링 중에는 수행될 수 없기 때문이다.

부작용 Hook인 `useEffect`는 함수 컴포넌트에 부작용을 수행할 수 있는 능력을 준다. React 클래스의 `componentDidMount`, `componentDidUpdate`, `componentWillUnmount`와 같은 기능을 제공하지만 하나의 API로 통합되었다.

예시의 컴포넌트는 React가 DOM을 업데이트한 후에 문서의 제목을 설정한다.

```js
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // componentDidMount, componentDidUpdate와 비슷하다.
  useEffect(() => {
    // 브라우저 API를 이용해 문서의 제목을 업데이트한다.
    document.title = `You clicked ${count} times`;
  });

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

`useEffect`를 호출하는 것은 React에게 DOM에 변경 사항을 적용한 뒤에 부작용 함수를 실행하라고 명령을 내리는 것이다. 부작용 함수는 컴포넌트 내부에서 선언되었으므로 props와 상태에 접근할 수 있다. 기본적으로 React는 매 렌더링(첫 렌더링 포함) 이후에 부작용 함수를 실행한다.

부작용 함수의 반환 함수에서 이후에 어떻게 정리할지(clean up) 지정할 수 있다. 이는 선택 사항이다.

예시의 컴포넌트는 부작용 함수에서 친구의 접속 상태를 구독하고, 정리 단계에서 구독을 해지한다.

```js
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);

    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

이 예시에서 React는 컴포넌트가 언마운트(unmount)될 때, 그리고 후속 렌더링 때문에 부작용 함수를 다시 실행하기 전에 `ChatAPI`에서 구독을 해지할 것이다. `ChatAPI`에게 건네준 `props.friend.id`가 바뀌지 않았을 때 재구독을 건너뛰게 만드는 것도 가능하다.

`useState`와 동일하게 컴포넌트 안에서 복수의 부작용 함수를 사용할 수 있다.

```js
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }
  // ...
```

Hook을 사용하면 구독을 하고 해지하는 로직 같이 서로 관련 있는 조각들을 컴포넌트의 부작용 함수에 정리할 수 있다. 클래스 컴포넌트에서는 생명주기 메서드에 기반해 강제로 나눠졌던 것과는 대조된다.

## ✌️ Hook 사용 규칙

Hook은 JavaScript 함수이지만 다음 두 가지 규칙을 지켜야 한다.

- Hook은 <strong>최상위(at the top level)</strong>에서만 호출한다. 반복문, 조건문, 중첩 함수 내부에서는 Hook을 호출하지 않는다.
- Hook은 **React 함수 컴포넌트**에서만 호출한다. 일반 JavaScript 함수에서는 Hook을 호출하지 않는다. 예외로 커스텀 Hook에서는 가능하다.

이 규칙을 자동으로 적용하는 [linter 플러그인](https://www.npmjs.com/package/eslint-plugin-react-hooks)이 제공된다. 처음에는 규칙들이 제한적이고 혼란스러울 수 있다. 하지만 이 제약들은 Hook이 잘 작동하게 만들어준다.

## 💡 나만의 Hook 만들기

컴포넌트 간에 상태 관련 로직을 재사용하고 싶을 때가 종종 있다. 전통적으로 이 문제에 대한 유명한 해결책이 두 가지 있었다. [higher-order components](https://ko.reactjs.org/docs/higher-order-components.html)와 [render props](https://ko.reactjs.org/docs/render-props.html)이다. 커스텀 Hook을 이용하면 문제 해결을 위해 트리에 새 컴포넌트를 추가하지 않아도 된다.

위 예시에서 친구의 접속 상태를 구독하기 위해 `useState`와 `useEffect` Hook을 호출하는 `FriendStatus` 컴포넌트를 소개한 바 있다. 이 구독 로직을 다른 컴포넌트에서도 재사용하고 싶다고 가정해 보겠다.

먼저 이 로직을 커스텀 Hook, `useFriendStatus`로 추출한다.

```js
import React, { useState, useEffect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

이 Hook은  `friendID`를 인수로 받아 친구의 접속 상태를 반환한다.

이제 이 Hook을 두 컴포넌트에서 사용할 수 있다.

```js
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

```js
function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

각 컴포넌트의 상태는 완전히 독립적이다. Hook은 상태 그 자체가 아니라 상태 관련 로직을 재사용하는 방법이다. 실제로 각각의 Hook 호출은 완전히 독립된 상태를 갖는다. 따라서 하나의 컴포넌트에서 동일한 커스텀 Hook을 두 번 사용하는 것도 가능하다.

커스텀 Hook은 기능보다는 컨벤션에 가깝다. 함수의 이름이 `use`로 시작하며 내부에서 다른 Hook을 호출하면, 우리는 그 함수를 커스텀 Hook이라고 부른다. linter 플러그인은 이 `useSomething` 네이밍 컨벤션을 이용해 Hook을 쓰는 코드에서 버그를 찾는다.

폼 핸들링, 애니메이션, 선언적 구독(declarative subscriptions), 타이머 등 많은 유스케이스를 다루는 커스텀 Hook을 만들 수 있다.

## 🔌 기타 내장 Hook

자주 사용되지는 않지만 유용하다고 생각할 만한 내장 Hook이 몇 가지 있다.

[`useContext`](https://ko.reactjs.org/docs/hooks-reference.html#usecontext)를 이용하면 중첩 없이도 React 컨텍스트를 구독할 수 있다.

```js
function Example() {
  const locale = useContext(LocaleContext);
  const theme = useContext(ThemeContext);
  // ...
}
```

[`useReducer`](https://ko.reactjs.org/docs/hooks-reference.html#usereducer)을 이용하면 복잡한 컴포넌트들의 상태를 reducer로 관리할 수 있다.

```js
function Todos() {
  const [todos, dispatch] = useReducer(todosReducer);
  // ...
```
