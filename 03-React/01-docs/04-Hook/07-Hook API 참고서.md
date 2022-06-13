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