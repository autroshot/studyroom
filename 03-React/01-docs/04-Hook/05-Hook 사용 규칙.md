# Hook 사용 규칙

Hook은 JavaScript 함수이다. 하지만 Hook을 사용할 때는 다음 두 가지 규칙을 지켜야 한다. [linter 플러그인](https://www.npmjs.com/package/eslint-plugin-react-hooks)을 사용하면 이 규칙이 자동으로 적용된다.

### 1. 최상위에서만 Hook을 호출한다

**반복문, 조건문, 중첩 함수 내부에서 Hook을 호출하면 안 된다.** early return 이전, React 함수의 최상위에서 Hook을 사용해야 한다.

이 규칙을 지키면 컴포넌트가 렌더링될 때마다 Hook이 같은 순서로 호출되는 것이 보장된다. 그 결과 복수의 `useState`, `useEffect` 호출 간에 Hook의 상태를 올바르게 보존할 수 있다.

### 2. React 함수에서만 Hook을 호출한다

**일반 JavaScript 함수에서 Hook을 호출하면 안 된다.** 대신 다음 위치에서 Hook을 호출할 수 있다.

-   React 함수 컴포넌트
-   커스텀 Hook

이 규칙을 지키면 컴포넌트의 모든 상태 관련 로직이 소스 코드에서 명확하게 보인다.

## ESLint 플러그인

React는 이 두 가지 규칙을 강제하는  [`eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks)라는 ESLint 플러그인을 지원한다. 원한다면 이 플러그인을 프로젝트에 추가할 수 있다.

이 플러그인은  [Create React App](https://ko.reactjs.org/docs/create-a-new-react-app.html#create-react-app)에 기본적으로 포함되어 있다.

```bash
npm install eslint-plugin-react-hooks --save-dev
```

```json
// ESLint 설정
{
  "plugins": [
    // ...
    "react-hooks"
  ],
  "rules": {
    // ...
    "react-hooks/rules-of-hooks": "error", // Hook 사용 규칙을 확인
    "react-hooks/exhaustive-deps": "warn" // 부작용 의존성을 확인
  }
}
```

## 자세한 설명

**React는 Hook이 호출되는 순서에 의존해서 상태를 보존한다.**

따라서 조건문 안에서 Hook을 호출하면, 호출 순서가 달라져서 버그가 생길 수 있다.

조건부로 부작용을 실행하고 싶다면 다음과 같이 조건문을 Hook 내부에 넣을 수 있다.

```jsx
  useEffect(function persistForm() {
    // 👍 첫 번째 규칙을 어기지 않는다.
    if (name !== '') {
      localStorage.setItem('formData', name);
    }
  });
```
