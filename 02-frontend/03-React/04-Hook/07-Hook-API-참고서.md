# Hook API 참고서

## 기본 Hook

### useState

```tsx
const [state, setState] = useState(initialState);
```

유상태 값과 값을 업데이트하는 함수를 반환한다.

초기 렌더링에서 반환되는 상태(`state`)는 첫 번째 인수(`initialState`)로 전달된 값과 동일하다.

`setState` 함수는 상태를 업데이트하는 데 사용된다. 새 상태 값을 받아 컴포넌트의 리렌더링을 대기열에 넣는다.

```tsx
setState(newState);
```

이후의 리렌더링에서, `useState`에서 반환되는 첫 번째 값은 항상 업데이트가 적용된 가장 최신 상태가 된다.

> **참고**
>
> 리액트는 `setState` 함수의 동일성이 안정적이고 리렌더링 시에도 변경되지 않음을 보장한다. 이것이 `useEffect`나 `useCallback`의 종속성 목록에 이 함수를 포함하지 않아도 되는 이유이다.

#### 함수적 업데이트

새 상태가 이전 상태를 사용하여 계산되는 경우에는 `setState`에 함수를 전달할 수 있다. 함수는 이전 값을 받고 업데이트된 값을 반환한다.

다음은 `setState`의 두 가지 형식을 모두 사용하는 카운터 컴포넌트의 예시이다.

```tsx
function Counter({initialCount}) {
  const [count, setCount] = useState(initialCount);
  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>Reset</button>
      <button onClick={() => setCount(prevCount => prevCount - 1)}>-</button>
      <button onClick={() => setCount(prevCount => prevCount + 1)}>+</button>
    </>
  );
}
```

`+`와 `-` 버튼은 업데이트된 값이 이전 값을 기반으로 하기 때문에 함수 형식을 사용한다. 그러나 `Reset` 버튼은 항상 카운트를 초깃값으로 다시 설정하기 때문에 일반 형식을 사용한다.

만약 업데이트 함수가 현재 상태와 정확히 동일한 값을 반환하면 후속 재렌더링은 완전히 건너뛴다.

> **참고**
>
> 클래스 컴포넌트의 `setState` 메서드와 달리 `useState`는 업데이트 객체를 자동으로 병합하지 않는다. 함수 업데이터 형식과 객체 스프레드 문법을 결합하여 이 동작을 따라할 수 있다.
>
> ```tsx
> const [state, setState] = useState({});
> setState(prevState => {
>   // Object.assign도 가능하다.
>   return {...prevState, ...updatedValues};
> });
> ```
>
> 또 다른 옵션으로 `useReducer`가 있으며, 이는 여러 하위 값을 가지는 상태 객체를 관리하는 데 적합하다.

#### 지연된 초기 상태

`initialState` 인수는 초기 렌더링 중에 사용되는 상태이다. 후속 렌더링에서는 무시된다. 초기 상태가 복잡한 계산의 결과인 경우에는, 대신 초기 렌더링에서만 실행되는 함수를 제공할 수 있다.

```tsx
const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props);
  return initialState;
});
```

#### 상태 업데이트 벗어나기

상태 훅을 현재 상태와 같은 값으로 업데이트하면 리액트는 자식을 렌더링하거나 부작용를 만들지 않고 벗어난다. 참고로 리액트는 [`Object.is` 비교 알고리즘](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/is)을 사용한다.

리액트는 벗어나기 전에 특정 컴포넌트를 다시 렌더링해야 할 수도 있다. 리액트가 불필요하게 트리로 깊이 들어가지 않기 때문에 걱정할 필요가 없다. 렌더링 동안 비용이 많이 드는 계산을 수행하는 경우에는 `useMemo`로 최적화가 가능하다.

#### 상태 업데이트 일괄 처리하기

리액트는 성능을 향상시키기 위해 여러 상태 업데이트를 단일 재렌더링으로 그룹화할 수 있다. 이 방식은 일반적으로 성능을 향상시키며 앱의 동작에 영향을 주지 않는다.

리액트 18 이전에는 리액트 이벤트 핸들러 내부의 업데이트만 일괄 처리되었다. 리액트 18부터는 [기본적으로 모든 업데이트에 대해 일괄 처리가 활성화된다](https://reactjs.org/blog/2022/03/08/react-18-upgrade-guide.html#automatic-batching). 리액트는 여러 다른 사용자에 의한 이벤트(예: 버튼을 두 번 클릭)의 업데이트가 항상 개별적으로 처리되고 일괄 처리되지 않도록 한다. 이것은 논리적 실수를 방지한다.

DOM 업데이트를 강제로 동기적으로 처리해야 하는 경우에는 [`flushSync`](https://reactjs.org/docs/react-dom.html#flushsync)로 감싸면 된다. 그러나 성능이 떨어질 수 있으므로 반드시 필요한 경우에만 이 작업을 수행해야 한다.

### useContext

```jsx
const value = useContext(MyContext);
```

컨텍스트 객체(`React.createContext`에서 반환된 값)를 받아 그 컨텍스트의 현재 컨텍스트 값을 반환한다. 현재 컨텍스트 값은 트리의 호출 컴포넌트 상단에서 가장 가까운 `<MyContext.Provider>`의 `value` prop에 의해 결정된다.

컴포넌트 위의 가장 가까운  `<MyContext.Provider>`가 업데이트되면, 이 Hook은 `MyContext` 공급자에게 전달된 최신 컨텍스트 `value`로 렌더링을 트리거한다. 조상이 [`React.memo`](https://ko.reactjs.org/docs/react-api.html#reactmemo)나 [`shouldComponentUpdate`](https://ko.reactjs.org/docs/react-component.html#shouldcomponentupdate)를 사용하더라도 `useContext`을 이용하는 컴포넌트 자체에서 여전히 렌더링이 일어난다.

`useContext`의 인수는 컨텍스트 객체여야 하다는 걸 주의해야 한다.

- **맞는 사용:** `useContext(MyContext)`
- **틀린 사용:** `useContext(MyContext.Consumer)`
- **틀린 사용:** `useContext(MyContext.Provider)`

`useContext`를 호출하는 컴포넌트는 컨텍스트 값이 변경되면 항상 리렌더링될 것이다. 컴포넌트의 리렌더링에 비용이 많이 든다면 [메모이제이션](https://github.com/facebook/react/issues/15156#issuecomment-474590693)을 이용해 최적화할 수 있다.

> **팁**
>
> Hook 이전의 컨텍스트 API에 익숙하다면 `useContext(MyContext)`는 클래스의 `static contextType = MyContext`, 또는 `<MyContext.Consumer>`와 동일하다고 보면 된다.
>
> `useContext(MyContext)`는 컨텍스트를 읽고 변경 사항을 구독하는 것만 가능하다. 컨텍스트의 값을 공급하기 위해서는 여전히 트리 상단에 `<MyContext.Provider>`가 필요하다.

**Context.Provider와 같이 사용해야 한다.**

```JSX
const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee"
  },
  dark: {
    foreground: "#ffffff",
    background: "#222222"
  }
};

const ThemeContext = React.createContext(themes.light);

function App() {
  return (
    <ThemeContext.Provider value={themes.dark}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);

  return (
    <button style={{ background: theme.background, color: theme.foreground }}>
      I am styled by theme context!
    </button>
  );
}
```

이 예시는 [고급 안내서 컨텍스트](../02-고급-안내서/컨텍스트.md)의 예시를 Hook을 사용하는 예시로 수정한 것이다.

## 추가 Hook

### useReducer

```jsx
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

[`useState`](https://ko.reactjs.org/docs/hooks-reference.html#usestate)의 대안이다. `(state, action) => newState`의 형태로 Reducer를 받고 `dispatch` 메서드와 짝을 이루는 현재 상태를 반환한다. 리덕스와 유사하게 동작한다.

복수의 하위 값을 포함하는 복잡한 상태 로직이 있거나 다음 상태가 이전 상태에 의존적인 경우에는 `useState`보다 `useReducer`가 더 선호된다. 그리고 `useReducer`를 사용하면 [콜백 대신 `dispatch`를 전달](https://ko.reactjs.org/docs/hooks-faq.html#how-to-avoid-passing-callbacks-down)할 수 있기 때문에 깊은 업데이트를 트리거하는 컴포넌트의 성능을 최적화할 수 있다.

다음은 `useState` 섹션의 카운터 예시를 Reducer를 사용해 다시 작성한 것이다.

```jsx
const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

> **알림**
>
> 리액트는 `dispatch` 함수의 동일성이 안정적이고 리렌더링에서도 변경되지 않음을 보장한다. 이것이 `useEffect`나 `useCallback`의 의존성 목록에서 이 함수를 생략해도 안전한 이유이다. 

#### 초기 상태 지정하기

`useReducer`의 상태를 초기화하는 방법은 두 가지가 있다. 유스 케이스에 따라 둘 중 하나를 선택할 수 있다.

가장 간단한 방법은 초기 상태를 두 번째 인수로 전달하는 것이다.

```jsx
  const [state, dispatch] = useReducer(
    reducer,
    {count: initialCount}
  );
```

> **알림**
>
> 리액트는 리덕스에서 대중화된 인수 컨벤션인 `state = initialState`을 사용하지 않는다. 초기값은 가끔 props에 의존해야 하므로 대신 Hook 호출에서 지정한다.
>
> 이 방식을 굳이 사용하고 싶다면, `useReducer(reducer, undefined, reducer)`를 호출해서 리덕스 방식을 모방할 수 있지만 권장하지는 않는다.

#### 느린 초기화

초기 상태를 느리게 생성할 수도 있다. 이를 위해서는 `init` 함수를 세 번째 인수로 전달한다. 초기 상태는 `init(initialArg)`로 설정될 것이다.

이를 통해 초기 상태를 계산하는 로직을 Reducer 밖으로 추출할 수 있다. 그리고 나중에 작업에 대한 응답으로 상태를 재설정하는 데에도 유용하다.

```jsx
function init(initialCount) {
  return {count: initialCount};
}

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    case 'reset':
      return init(action.payload);
    default:
      throw new Error();
  }
}

function Counter({initialCount}) {
  const [state, dispatch] = useReducer(reducer, initialCount, init);
  return (
    <>
      Count: {state.count}
      <button
        onClick={() => dispatch({type: 'reset', payload: initialCount})}>
        Reset
      </button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

#### Dispatch에서 벗어나기

Reducer Hook에서 현재 상태와 동일한 값을 반환하면 리액트는 자식을 렌더링하거나 부작용을 발생시키지 않고 Dispatch에서 벗어난다. 참고로 리액트는 [`Object.is` 비교 알고리즘](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description)을 사용한다.

리액트는 Dispatch를 벗어나기 전에 여전히 해당 컴포넌트를 렌더링해야 할 수도 있다. 리액트가 불필요하게 트리로 더 깊이 들어가지 않기 때문에 걱정할 필요는 없다. 렌더링에 많은 연산이 든다면 `useMemo`를 사용해 최적화할 수 있다.

### useRef

```jsx
const refContainer = useRef(initialValue);
```

`useRef`는 건네준 인수(`initialValue`)로 초기화된 `.current` 프로퍼티를 가지며 변경 가능한 Ref 객체를 반환한다. 반환된 객체는 컴포넌트의 전체 생애주기 동안 유지된다.

일반적인 유스 케이스는 자식에게 직접 접근하는 것이다.

```jsx
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current`는 마운트된 텍스트 input 요소를 가리킨다.
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

기본적으로 `useRef`는 자신의 `.current` 프로퍼티에 변경 가능한 값을 담을 수 있는 '상자'와 같다.

DOM에 접근하는 방법으로 Ref를 많이 사용했을 것이다. `<div ref={myRef} />`로 리액트에 Ref 객체를 건네주면, 리액트는 노드가 변경될 때마다 객체의 `.current` 프로퍼티를 해당 DOM 노드로 설정할 것이다.

하지만 `useRef()`가 `ref` 속성보다 더 유용하다. 클래스에서 인스턴스 필드를 사용하는 방법과 유사하게 `useRef()`는 어떤 변경 가능한 값을 유지하는 데에 편리하다(관련 내용은 [자주 묻는 질문](https://ko.reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables)에서 확인).

이것은 `useRef()`가 일반적인 자바스크립트 객체를 생성하기 때문이다. `useRef()`는 직접 `{ current: ... }` 객체를 만드는 것과 유사하다. `useRef()`는 매 렌더링마다 동일한 Ref 객체를 제공한다는 것이 유일한 차이점이다.

`useRef`는 콘텐츠가 변경되어도 알려주지 않는다. `.current` 프로퍼티를 변경해도 리렌더링이 일어나지 않는다. 리액트가 DOM 노드에 Ref를 붙이거나 뗄 때 어떤 코드를 실행하고 싶다면 [콜백 Ref](https://ko.reactjs.org/docs/hooks-faq.html#how-can-i-measure-a-dom-node)을 이용한다.