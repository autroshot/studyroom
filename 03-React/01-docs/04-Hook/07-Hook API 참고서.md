# Hook API 참고서

## 기본 Hook

### `useContext`

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

---

:information_source: **팁**

Hook 이전의 컨텍스트 API에 익숙하다면 `useContext(MyContext)`는 클래스의 `static contextType = MyContext`, 또는 `<MyContext.Consumer>`와 동일하다고 보면 된다.

`useContext(MyContext)`는 컨텍스트를 읽고 변경 사항을 구독하는 것만 가능하다. 컨텍스트의 값을 공급하기 위해서는 여전히 트리 상단에 `<MyContext.Provider>`가 필요하다.

---

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

### `useRef`

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

DOM에 접근하는 방법으로 Ref를 많이 사용했을 것이다. `<div ref={myRef} />`로 React에 Ref 객체를 건네주면, React는 노드가 변경될 때마다 객체의 `.current` 프로퍼티를 해당 DOM 노드로 설정할 것이다.

하지만 `useRef()`가 `ref` 속성보다 더 유용하다. 클래스에서 인스턴스 필드를 사용하는 방법과 유사하게 `useRef()`는 어떤 변경 가능한 값을 유지하는 데에 편리하다(관련 내용은 [자주 묻는 질문](https://ko.reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables)에서 확인).

이것은 `useRef()`가 일반적인 JavaScript 객체를 생성하기 때문이다. `useRef()`는 직접 `{ current: ... }` 객체를 만드는 것과 유사하다. `useRef()`는 매 렌더링마다 동일한 Ref 객체를 제공한다는 것이 유일한 차이점이다.

`useRef`는 콘텐츠가 변경되어도 알려주지 않는다. `.current` 프로퍼티를 변경해도 리렌더링이 일어나지 않는다. React가 DOM 노드에 Ref를 붙이거나 뗄 때 어떤 코드를 실행하고 싶다면 [콜백 Ref](https://ko.reactjs.org/docs/hooks-faq.html#how-can-i-measure-a-dom-node)을 이용한다.