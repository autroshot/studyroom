# 부작용 Hook 사용하기

부작용(effect) Hook을 사용하면 함수 컴포넌트에서 부작용(side effect)을 수행할 수 있다.

```js
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // componentDidMount, componentDidUpdate와 유사하게
  useEffect(() => {
    // 브라우저 API를 이용해 문서 제목을 업데이트한다.
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

위의 코드는 이전 페이지의 [카운터 예시](./03-%EC%83%81%ED%83%9C%20Hook%20%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0.md#%EC%83%81%ED%83%9C-hook-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)를 바탕으로 새로운 기능을 추가했다. 문서 제목을 클릭 횟수를 포함한 커스텀 메시지로 지정하는 기능이다.

React 컴포넌트에서 네트워크 요청, 구독, 수동으로 DOM을 바꾸는 작업들이 모두 부작용의 예시이다. 이 작업들을 '부작용'이라고 부르는 것이 익숙하지 않을지라도 컴포넌트에서 수행해 본 적이 있을 것이다.

---

:information_source: **팁**

React 클래스의 생명주기 메서드가 익숙하다면 `useEffect` Hook을 `componentDidMount`, `componentDidUpdate`, `componentWillUnmount`가 합쳐진 것으로 봐도 된다.

---

React 컴포넌트의 부작용은 정리의 필요 여부에 따라 구분된다.

## 정리가 필요 없는 부작용

React가 DOM을 업데이트한 이후에 추가 코드를 실행하고 싶을 때가 있다. 네트워크 요청, DOM 수동 조작, 로깅은 정리가 필요하지 않은 대표적인 예이다. 이 작업들은 실행하고 잊어버려도 된다. 클래스와 Hook이 이런 부작용을 표현하는 방법을 비교해 보겠다.

### 클래스를 사용하는 예시

React 클래스 컴포넌트에서 `render` 메서드 자체는 부작용을 일으키지 않는다. 그리고 이때는 너무 이르다.

일반적으로 React가 DOM을 업데이트한 뒤에 부작용을 수행하기를 원한다. 이것이 React 클래스에서 부작용을 `componentDidMount`와 `componentDidUpdate`에 넣는 이유이다.

예시로 돌아와서, React가 DOM을 변경한 직후에 문서 제목을 업데이트하는 클래스 컴포넌트 카운터를 살펴보겠다.

```js
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }

  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
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

두 개의 생명주기 메서드에 **중복된 코드**가 있는 것을 주목해야 한다.

대부분의 경우에는 컴포넌트가 방금 마운트된 것인지, 업데이트된 것인지와는 상관없이 동일한 부작용을 수행하고 싶어 한다. 모든 렌더링 후에 작업을 수행하기를 원하는 것이다. 하지만 React 클래스 컴포넌트에는 이에 해당되는 메서드가 없다. 별개의 메서드로 분리할 수는 있겠지만 여전히 두 곳에서 호출해야 한다.

이제 `useEffect`  Hook으로 동일한 작업을 수행하는 방법을 알아보겠다.

### Hook을 사용하는 예시

이 예시는 위에서 이미 봤던 것이지만 좀 더 자세히 살펴보겠다.

```js
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
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

#### `useEffect`는 무슨 일은 할까?

React에게 컴포넌트가 렌더링 이후에 어떤 작업을 해야 한다고 알려주는 것이다. React는 건네받은 함수를 기억해뒀다가 DOM이 업데이트된 이후에 함수를 호출한다. 이 예시에서는 부작용에서 문서의 제목을 지정하지만, 네트워크 요청이나 다른 명령형(imperative) API를 호출하는 것도 가능하다.

#### `useEffect`가 컴포넌트 내부에서 호출되는 이유는 무엇일까?

부작용이 `count` 상태 변수를 비롯해 어떤 props에도 접근할 수 있게 된다. 이미 함수 스코프 안에 있으므로 상태에 접근하기 위한 특별한 API가 필요하지 않다. Hook은 React 전용 API를 도입하는 대신 JavaScript의 클로저를 활용했다.

#### `useEffect`는 매 렌더링 이후에 실행되는 것일까?

그렇다. 기본값으로 첫 렌더링 이후, 매 업데이트 이후에 실행된다. 이것을 커스터마이징 하는 방법은 나중에 알아볼 것이다.

마운팅과 업데이트의 관점에서 생각하는 대신, 렌더링 이후에 부작용이 일어난다고 생각하는 것이 더 이해하기 쉬울 수 있다. 부작용이 실행되는 시점에는 DOM이 반드시 업데이트되어 있다.

### 자세한 설명

```js
function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
}
```

`count` 상태 변수를 선언하고 나서 React에게 부작용을 사용해야 한다고 말한다. `useEffect` Hook에 함수를 건네주는데, 이 함수가 부작용이다.

부작용에서는 `document.title` 브라우저 API를 이용해 문서의 제목을 지정한다. 부작용은 함수 스코프 안에 있으므로 최신 `count`를 읽을 수 있다.

컴포넌트가 렌더링될 때, React는 건네받은 부작용을 기억해뒀다가 DOM 업데이트 이후에 부작용을 실행한다. 이 과정은 첫 렌더링을 포함한 모든 렌더링마다 일어난다.

숙련된 JavaScript 개발자라면 `useEffect`에 건네준 함수가 매 렌더링마다 달라진다는 것을 알아챘을 것이다. 이는 의도된 것으로, 오래된 `count` 값을 얻는다는 걱정 없이 부작용 내부에서 `count` 값을 읽을 수 있게 해 준다. 리렌더링할 때마다 이전 부작용을 대체하는 '다른' 부작용을 스케줄링한다.

어떤 면에서는 부작용이 렌더링 결과의 일부처럼 작동하게 만든다. 이는 각 부작용이 특정 렌더링에 속하다는 것을 의미한다. 이것이 왜 유용한지는 [뒤](#%EC%84%A4%EB%AA%85-%EB%B6%80%EC%9E%91%EC%9A%A9%EC%9D%B4-%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8%EB%A7%88%EB%8B%A4-%EC%8B%A4%ED%96%89%EB%90%98%EB%8A%94-%EC%9D%B4%EC%9C%A0)에서 살펴보겠다.

---

:information_source: **팁**

`componentDidMount`, `componentDidUpdate`와 달리 `useEffect`로 스케줄링되는 부작용은 브라우저가 화면을 업데이트하는 것을 막지 않는다. 이것은 앱의 반응성을 향상시킨다. 대부분의 부작용은 동기적으로 실행될 필요가 없다. 레이아웃 측정과 같이 동기적 실행이 필요한 경우에는 별도의 [`useLayoutEffect`](https://ko.reactjs.org/docs/hooks-reference.html#uselayouteffect) Hook을 사용한다. 사용법은 동일하다.

---

## 정리가 필요한 부작용

몇몇 부작용은 정리가 필요하다. 예를 들어 어떤 외부 데이터 소스에 구독을 설정하고 싶을 수 있다. 이 경우에는 메모리 누수가 발생하지 않도록 정리하는 것이 중요하다.

클래스를 이용하는 방법과 Hook을 이용하는 방법을 비교해 보겠다.

### 클래스를 사용하는 예시

React 클래스에서는 흔히 `componentDidMount`에서 구독을 설정한 뒤 `componentWillUnmount`에서 이를 정리한다. 예를 들어 친구의 온라인 상태를 구독할 수 있는 ChatAPI 모듈이 있다고 가정해 보겠다. 클래스를 이용해 상태를 구독하고 표시하는 방법은 다음과 같다.

```js
class FriendStatus extends React.Component {
  constructor(props) {
    super(props);
    this.state = { isOnline: null };
    this.handleStatusChange = this.handleStatusChange.bind(this);
  }

  componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  handleStatusChange(status) {
    this.setState({
      isOnline: status.isOnline
    });
  }

  render() {
    if (this.state.isOnline === null) {
      return 'Loading...';
    }
    return this.state.isOnline ? 'Online' : 'Offline';
  }
}
```

`componentDidMount`와 `componentWillUnmount`는 코드가 거의 동일한다. 두 곳의 코드는 개념상 같은 부작용과 연관되어 있음에도 생명주기 메서드가 이 로직을 강제로 분리하게 만든다.

---

:information_source: **알림**

눈썰미가 좋은 개발자는 이 예시가 완전해지기 위해서는 `componentDidUpdate` 메서드가 필요하다는 사실을 눈치챘을 것이다. 지금은 이것을 무시하고 넘어갈 것이다. 이후의 [섹션](#%EC%84%A4%EB%AA%85-%EB%B6%80%EC%9E%91%EC%9A%A9%EC%9D%B4-%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8%EB%A7%88%EB%8B%A4-%EC%8B%A4%ED%96%89%EB%90%98%EB%8A%94-%EC%9D%B4%EC%9C%A0)에서 자세히 다룰 것이다.

---

### Hook을 사용하는 예시

정리를 실행하는 별도의 부작용이 필요할 것이라고 생각할 수 있다. 하지만 구독의 추가와 제거를 위한 코드는 매우 밀접하게 연관되어 있으므로 `useEffect`는 이것을 한꺼번에 다룬다.

부작용이 함수를 반환하면 React는 그 함수를 정리가 필요할 때 실행할 것이다.

```js
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // 이 부작용 이후에 어떻게 정리할지 명시한다.
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

#### 부작용에서 함수를 반환하는 이유는 무엇일까?

부작용의 정리 메커니즘은 선택 사항이다. 모든 부작용은 이후의 정리를 위한 함수를 반환할 수 있다. 이를 통해 구독의 추가와 제거 로직을 한 곳에 둘 수 있게 된다. 구독의 추가와 제거는 같은 부작용의 일부이다.

#### React가 부작용을 정리하는 시점은 정확히 언제일까?

React는 컴포넌트가 언마운트될 때 정리를 수행한다. 하지만 앞에서 배웠듯이 부작용은 한 번이 아니라 매 렌더링마다 실행된다. 따라서 React는 다음 부작용이 실행되기 전에 이전 렌더링의 부작용을 정리한다. 이런 작동 방식이 [버그를 피하는 데에 어떤 도움을 주는지](#%EC%84%A4%EB%AA%85-%EB%B6%80%EC%9E%91%EC%9A%A9%EC%9D%B4-%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8%EB%A7%88%EB%8B%A4-%EC%8B%A4%ED%96%89%EB%90%98%EB%8A%94-%EC%9D%B4%EC%9C%A0), 그리고 [성능 문제가 생기는 경우에 이 동작을 해제하는 방법](#%ED%8C%81-%EB%B6%80%EC%9E%91%EC%9A%A9%EC%9D%84-%EA%B1%B4%EB%84%88%EB%9B%B0%EC%96%B4%EC%84%9C-%EC%84%B1%EB%8A%A5-%EC%B5%9C%EC%A0%81%ED%99%94%ED%95%98%EA%B8%B0)은 나중에 알아볼 것이다.

---

:information_source: **알림**

부작용에서 반드시 이름이 있는 함수를 반환해야 하는 것은 아니다. 예시에서는 `cleanup`이라는 이름을 사용했지만 다른 이름을 붙이거나 화살표 함수를 반환하는 것도 가능하다.

---

## 요약

`useEffect`를 사용하면 컴포넌트 렌더링 이후에 다양한 종류의 부작용을 표현할 수 있다는 것을 배웠다. 일부 부작용은 정리가 필요하므로 함수를 반환한다.

```js
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
```

정리 단계가 필요하지 않은 부작용은 아무것도 반환하지 않는다.

```js
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
```

부작용 Hook은 두 가지 유스 케이스를 하나의 API로 통합한다.

## 부작용 사용 팁

숙련된 React 사용자가 궁금증을 가질 만한 `useEffect`의 몇 가지 측면을 깊게 살펴보겠다.

### 팁: 관심사를 분리하려면 복수의 부작용을 사용한다

Hook이 탄생한 [동기](https://ko.reactjs.org/docs/hooks-intro.html#complex-components-become-hard-to-understand)에서 설명한 문제 중 하나는, 클래스의 생명주기 메서드에 종종 관련 없는 로직이 포함되고 관련 있는 로직은 여러 개의 메서드로 쪼개진다는 것이다.

다음은 이전 예시의 친구 상태 표시 로직과 카운터를 결합한 컴포넌트이다.

```js
class FriendStatusWithCounter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0, isOnline: null };
    this.handleStatusChange = this.handleStatusChange.bind(this);
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  handleStatusChange(status) {
    this.setState({
      isOnline: status.isOnline
    });
  }
  // ...
```

`document.title`을 설정하는 로직이 `componentDidMount`와 `componentDidUpdate`로 쪼개진 것을 볼 수 있다. 구독 로직도 `componentDidMount`와 `componentWillUnmount`에 흩어져 있다. 그리고 `componentDidMount`에는 두 작업의 코드가 모두 들어있다.

그렇다면 Hook은 이 문제를 어떻게 해결할 수 있을까? [상태 Hook을 여러 번 사용할 수 있는 것](https://github.com/autroshot/studyroom/blob/main/03-React/01-docs/02-Hook/03-%EC%83%81%ED%83%9C%20Hook%20%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0.md#%ED%8C%81-%EB%B3%B5%EC%88%98%EC%9D%98-%EC%83%81%ED%83%9C-%EB%B3%80%EC%88%98-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)처럼 부작용도 여러 번 사용할 수 있다. 이렇게 하면 관련 없는 로직을 다른 부작용으로 분리할 수 있다.

```js
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
  // ...
}
```

Hook을 이용하면 생명주기 메서드 이름이 아닌 **어떤 동작을 하는가**에 따라 코드를 나눌 수 있다. React는 명시된 순서에 따라 컴포넌트의 모든 부작용을 적용할 것이다.

### 설명: 부작용이 업데이트마다 실행되는 이유

클래스에 익숙하다면 왜 부작용 정리 단계가 언마운트될 때 한 번만 실행되는 게 아니라 모든 리렌더링 이후에 실행되는 건지 궁금할 것이다. 이런 디자인이 왜 버그가 적은 컴포넌트를 만드는 데에 도움을 주는 것인지 실용적인 예시를 통해 알아보겠다.

위의 [예시]()에서 친구의 온라인 여부를 표시하는 `FriendStatus` 컴포넌트를 소개한 바 있다. 클래스는 `this.props`에서 `friend.id`를 읽어서, 컴포넌트 마운트 이후에 친구 상태를 구독하고 언마운트 이후에 구독을 해지한다.

```js
  componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
```

그런데 컴포넌트가 화면에 표시되는 동안 `friend` prop이 변한다면 어떤 일이 벌어질까? 컴포넌트는 다른 친구의 온라인 상태를 계속 표시할 것이다. 이것은 버그이다. 구독 해지 호출이 잘못된 친구의 ID를 사용한다면 언마운트를 할 때 메모리 누수나 충돌이 발생할 수 있다.

클래스 컴포넌트에서는 이런 경우를 처리하기 위해 `componentDidUpdate`를 추가해야 한다.

```js
  componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentDidUpdate(prevProps) {
    // 이전 friend.id로 구독을 해지한다.
    ChatAPI.unsubscribeFromFriendStatus(
      prevProps.friend.id,
      this.handleStatusChange
    );
    // 다음 friend.id로 구독한다.
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
```

`componentDidUpdate`을 적절하게 처리하는 걸 깜빡하는 것은 React 애플리케이션에서 발생하는 버그의 흔한 원인이다.

이제 Hook을 사용하는 컴포넌트를 살펴보겠다.

```js
function FriendStatus(props) {
  // ...
  useEffect(() => {
    // ...
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
```

어떤 변경도 하지 않았지만 이 컴포넌트는 버그에 시달리지 않는다.

`useEffect`가 '기본값'으로 업데이트를 처리하기 때문에 별도의 특별한 코드가 필요하지 않다. 다음 부작용을 적용하기 전에 이전의 부작용을 정리한다.

다음은 컴포넌트가 시간에 따라 생성할 수 있는 구독과 구독 해지 호출의 시퀀스이다.

```js
// { friend: { id: 100 } } props로 마운트한다.
ChatAPI.subscribeToFriendStatus(100, handleStatusChange);     // 첫 부작용을 실행한다.

// { friend: { id: 200 } } props로 업데이트한다.
ChatAPI.unsubscribeFromFriendStatus(100, handleStatusChange); // 이전 부작용을 정리한다.
ChatAPI.subscribeToFriendStatus(200, handleStatusChange);     // 다음 부작용을 실행한다.

// { friend: { id: 300 } } props로 업데이트한다.
ChatAPI.unsubscribeFromFriendStatus(200, handleStatusChange); // 이전 부작용을 정리한다.
ChatAPI.subscribeToFriendStatus(300, handleStatusChange);     // 다음 부작용을 실행한다.

// 언마운트한다.
ChatAPI.unsubscribeFromFriendStatus(300, handleStatusChange); // 마지막 부작용을 정리한다.
```

이런 작동 방식은 기본값에 의한 일관성을 보장한다. 그리고 클래스 컴포넌트에서 자주 발생하는 버그의 원인인 업데이트 로직을 빠뜨리는 걸 방지한다.

### 팁: 부작용을 건너뛰어서 성능 최적화하기

어떤 경우에는 모든 렌더링 이후에 부작용을 정리하거나 적용하는 것이 성능 문제를 일으킨다.

클래스 컴포넌트에서는 `componentDidUpdate`에서 `prevProps`나 `prevState`를 비교하는 코드를 작성해서 문제를 해결할 수 있다.

```js
componentDidUpdate(prevProps, prevState) {
  if (prevState.count !== this.state.count) {
    document.title = `You clicked ${this.state.count} times`;
  }
}
```

이 요구 사항은 꽤 흔해서 `useEffect` Hook API에 해당 기능이 내장되었다.

React에게 특정 값이 리렌더링 간에 변하지 않았다면 부작용을 적용하는 걸 건너뛰라고 명령할 수 있다. `useEffect`의 선택 사항인 두 번째 인수에 배열을 건네주는 것이다.

```js
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // count가 바뀔 때만 부작용을 재실행한다.
```

위의 예시에서 `[count]`를 두 번째 인수로 넘겨준다. 이전 렌더링과 다음 렌더링의 `[count]`가 동일하면 부작용을 건너뛰어서 최적화가 된다.

값을 비교할 때는 일치 연산자(`===`)를 사용하며, 배열의 여러 항목 중 하나라도 다르면 부작용을 재실행한다.

정리 단계가 있는 부작용도 동일하게 동작하다.

```js
useEffect(() => {
  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
  return () => {
    ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  };
}, [props.friend.id]); // props.friend.id가 바뀔 때만 재구독한다.
```

미래에는 빌드 중 변환에 의해 두 번째 인수가 자동으로 추가될 수 있다.

---

:information_source: **알림**

이 최적화를 사용한다면 시간에 따라서 바뀌고 부작용에서 사용하는 컴포넌트 스코프의 모든 값(props, 상태)이 반드시 배열에 포함되야 한다. 그렇지 않으면 코드가 이전 렌더링의 오래된 값을 참조하게 될 것이다.  [함수를 다루는 방법](https://ko.reactjs.org/docs/hooks-faq.html#is-it-safe-to-omit-functions-from-the-list-of-dependencies)과 [배열이 너무 자주 바뀌는 경우에는 무엇을 해야 하는가](https://ko.reactjs.org/docs/hooks-faq.html#what-can-i-do-if-my-effect-dependencies-change-too-often)에서 자세한 내용을 확인할 수 있다.

부작용의 실행과 정리를 한 번씩만 실행하고 싶다면 빈 배열 `[]`을 두 번째 인수로 넘겨주면 된다. 이는 React에게 부작용이 pros나 상태의 어떤 값에도 의존하지 말라고 말하는 것이다. 그 결과 부작용은 재실행될 필요가 없어진다. 이 방법은 의존성 배열의 동작 방식을 그대로 따르므로 특별한 방법이 아니다.

빈 배열을 넘겨주면 부작용 내부의 props와 상태는 항상 초기값을 가지게 된다. 이 방법은 익숙한 `componentDidMount`와 `componentWillUnmount` 멘탈 모델(mental model)에 가깝지만, 부작용을 너무 자주 재실행하는 것을 피하는 일반적이고 좋은 방법은 위의 링크에 있다. 또한 React는 브라우저가 다 그려질 때까지 `useEffect`의 실행을 지연하기 때문에, 추가 작업을 하는 것은 큰 문제가 되지 않는다.

[`exhaustive-deps`](https://github.com/facebook/react/issues/14920) 규칙을 [`eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks#installation) 패키지의 일부로 사용하는 것을 추천한다. 이 규칙은 의존성이 올바르지 않으면 경고하고 수정 사항을 제안한다.

---
