# 자신만의 Hook 만들기

자신만의 Hook을 만들면 컴포넌트 로직을 재사용 가능한 함수로 추출할 수 있다.

[부작용 Hook 사용하기](./04-부작용-Hook-사용하기.md#Hook을 사용하는 예시)의 친구의 온라인 여부를 표시하는 채팅 애플리케이션에서 이 컴포넌트를 살펴봤었다.

```jsx
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
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

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

채팅 앱에 연락처 목록이 있고 온라인 사용자의 이름은 초록색으로 렌더링하고 싶다고 가정해 보겠다.

상단의 비슷한 로직을 복사해서 `FriendListItem` 컴포넌트에 붙여넣을 수 있지만, 이상적인 방법은 아니다.

```jsx
import React, { useState, useEffect } from 'react';

function FriendListItem(props) {
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

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

대신 `FriendStatus`와 `FriendListItem` 간에 이 로직을 공유하고 싶다.

전통적으로 React에는 유상태 로직을 컴포넌트 간에 공유하는 두 가지 유명한 방법이 있다. [render props](https://ko.reactjs.org/docs/render-props.html)와 [고차 컴포넌트](https://ko.reactjs.org/docs/higher-order-components.html)이다.

Hook을 이용하면 트리에 컴포넌트를 추가하지 않고도 문제를 해결할 수 있다.

## 커스텀 Hook 추출하기

두 개의 JavaScript 함수에서 로직을 공유하고 싶으면 그 로직을 또 다른 함수로 추출한다. 컴포넌트와 Hook도 함수이므로 같은 방법을 적용할 수 있다.

**커스텀 Hook은 이름이  `use`로 시작하는 JavaScript 함수이다.**

예를 들어 다음 `useFriendStatus` 함수는 커스텀 Hook이다.

```jsx
import { useState, useEffect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

새로운 것은 없다. 로직은 위의 컴포넌트에서 복사해 왔다. 다만 컴포넌트와 동일하게 커스텀 Hook의 최상단에서 조건 없이 다른 Hook을 호출해야 한다.

React 컴포넌트와 달리 커스텀 Hook에는 특별한 것이 필요하지 않다. 인수와 반환값은 마음대로 정할 수 있다. 즉 일반 함수와 동일하다.

[Hook 사용 규칙](./05-Hook%20%EC%82%AC%EC%9A%A9%20%EA%B7%9C%EC%B9%99.md)이 적용된다는 것을 한 눈에 알 수 있도록 함수의 이름은 반드시 `use`로 시작해야 한다.

`useFriendStatus` Hook의 목적은 친구의 상태를 구독하는 것이다. 따라서 `friendID`를 인수로 받고 해당 친구의 온라인 여부를 반환한다.

```jsx
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  // ...

  return isOnline;
}
```

이제 커스텀 Hook을 사용하는 방법을 알아보겠다.

## 커스텀 Hook 사용하기

처음의 목표는 `FriendStatus`와 `FriendListItem` 컴포넌트에서 중복된 논리를 제거하는 것이었다. 두 컴포넌트는 친구의 온라인 여부를 알고 싶다.

이제 이 로직을 `useFriendStatus` Hook으로 추출했으므로 다음과 같이 바로 사용할 수 있다.

```jsx
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) {
    return 'Loading...';
  }
  
  return isOnline ? 'Online' : 'Offline';
}
```

```jsx
function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

#### 기존 예시와 동일한 코드일까?

정확히 같은 방식으로 작동한다. 두 함수의 공통 코드를 별도의 함수로 추출한 것뿐이다. 커스텀 Hook은 React의 특별한 기능이 아니라 Hook의 디자인을 자연스럽게 따르는 컨벤션이다.

#### 커스텀 Hook의 이름은 반드시 `use`로 시작되야 할까?

그래야 한다. 이 컨벤션은 매우 중요하다. 이를 지키지 않으면 [Hook 사용 규칙](./05-Hook-사용-규칙.md) 위반을 자동으로 확인할 수 없다. React는 어떤 함수의 내부에 Hook 호출이 있는지를 모른다.

#### 같은 Hook을 사용하는 두 컴포넌트는 상태를 공유할까?

공유하지 않는다. 커스텀 Hook은 유상태 로직(구독을 설정하고 현재값을 기억하는 것)을 재사용하는 매커니즘이다. 커스텀 Hook을 사용할 때마다 내부의 상태와 부작용은 완전히 독립적이다.

#### 커스텀 Hook은 어떻게 독립된 상태를 얻는 걸까?

Hook에 대한 각 호출은 독립된 상태를 얻는다. `useFriendStatus`를 직접 호출하기 때문에, React의 관점에서는 컴포넌트가 `useState`와  `useEffect`를 호출하는 것과 다름없다. 이전에 배웠듯이 하나의 컴포넌트에서 `useState`와  `useEffect`를 여러 번 호출할 수 있고, 각 상태와 부작용은 독립적이다.

### 팁: Hook 간에 정보 넘겨주기

Hook은 함수이므로 Hook 간에도 정보를 넘겨줄 수 있다.

설명을 위해 가상 채팅 예시의 다른 컴포넌트를 사용할 것이다. 이것은 지금 선택된 친구의 온라인 여부를 표시하는 채팅 메시지 수신자 선택기이다.

```jsx
const friendList = [
  { id: 1, name: 'Phoebe' },
  { id: 2, name: 'Rachel' },
  { id: 3, name: 'Ross' },
];

function ChatRecipientPicker() {
  const [recipientID, setRecipientID] = useState(1);
  const isRecipientOnline = useFriendStatus(recipientID);

  return (
    <>
      <Circle color={isRecipientOnline ? 'green' : 'red'} />
      <select
        value={recipientID}
        onChange={e => setRecipientID(Number(e.target.value))}
      >
        {friendList.map(friend => (
          <option key={friend.id} value={friend.id}>
            {friend.name}
          </option>
        ))}
      </select>
    </>
  );
}
```

`recipientID` 상태 변수에 현재 선택된 친구의 ID를 저장한다. 사용자가 `<select>` 선택기의 다른 친구를 선택하면 상태가 업데이트된다.

`useState` Hook 호출은 `recipientID` 상태 변수의 최신값을 반환하기 때문에 이 값을 `useFriendStatus` 커스텀 Hook에 인수로 건네줄 수 있다.

```js
  const [recipientID, setRecipientID] = useState(1);
  const isRecipientOnline = useFriendStatus(recipientID);
```

이를 통해 현재 선택된 친구의 온라인 여부를 알 수 있다. 다른 친구를 선택하고 `recipientID` 상태 변수를 업데이트하면, `useFriendStatus` Hook은 이전에 선택된 친구의 구독을 해지하고 새로 선택된 친구의 상태를 구독할 것이다.

## `useYourImagination()`

커스텀 Hook은 이전에는 React 컴포넌트에서 불가능했던 로직 공유의 유연성을 제공한다. 폼 처리, 애니메이션, 선언형 구독, 타이머 같은 다양한 유스 케이스를 다루는 커스텀 Hook을 만들 수 있다. 게다가 React 내장 기능만큼 사용하기 쉬운 Hook을 만들 수 있다.

너무 일찍부터 추상화를 하지 않아도 된다. 이제 함수 컴포넌트가 더 많은 걸 할 수 있어서, 코드의 평균적인 함수 컴포넌트의 길이가 길어졌을 것이다. 이것은 정상이므로 즉시 함수를 Hook으로 나눌 필요는 없다.

그러나 커스텀 Hook을 사용하면 간단한 인터페이스 뒤로 복잡한 로직을 숨기거나 지저분한 컴포넌트를 푸는 데 도움이 되는 경우를 찾아내는 것도 좋다.

예를 들어 임기응변으로 관리되는 수많은 지역 상태가 담긴 복잡한 컴포넌트가 있다고 가정해 보겠다. `useState`는 업데이트 로직을 모으는 데에 도움이 되지 않는다. 대신 [Redux](https://redux.js.org/) reducer로 작성하는 것이 좋을 것이다.

```jsx
function todosReducer(state, action) {
  switch (action.type) {
    case 'add':
      return [...state, {
        text: action.text,
        completed: false
      }];
    // ... 다른 액션들 ...
    default:
      return state;
  }
}
```

리듀서는 독립적으로 테스트하고, 복잡한 업데이트 로직을 표현하기 위해 확장하는 것이 매우 편리하다. 필요하다면 리듀서를 더 작은 리듀서로 나누는 것도 가능하다.

그러나 React의 지역 상태를 사용하는 이점을 누리고 싶거나, 다른 라이브러리 설치를 원하지 않을 수도 있다.

리듀서로 컴포넌트의 지역 상태를 관리하는 `useReducer` Hook을 만들 수 있다면 어떨까? 단순화된 버전을 다음과 같다.

```jsx
function useReducer(reducer, initialState) {
  const [state, setState] = useState(initialState);

  function dispatch(action) {
    const nextState = reducer(state, action);
    setState(nextState);
  }

  return [state, dispatch];
}
```

이제 컴포넌트에서 리듀서를 사용하고, 리듀서가 컴포넌트의 상태 관리를 할 수 있게 되었다.

```jsx
function Todos() {
  const [todos, dispatch] = useReducer(todosReducer, []);

  function handleAddClick(text) {
    dispatch({ type: 'add', text });
  }

  // ...
}
```

복잡한 컴포넌트에서 리듀서로 지역 상태를 관리해야 하는 경우가 자주 있어서 React에는 `useReducer` Hook이 내장되어 있다. 이 Hook을 비롯한 여러 내장 Hook을 [Hook API 참고서](https://ko.reactjs.org/docs/hooks-reference.html)에서 찾을 수 있다.
