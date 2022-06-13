# Fragment

React에서 컴포넌트가 복수의 요소를 반환하는 것은 흔한 일이다. Fragment를 이용하면 DOM에 노드를 추가하지 않고도 자식 목록을 그룹화할 수 있다.

```jsx
render() {
  return (
    <React.Fragment>
      <ChildA />
      <ChildB />
      <ChildC />
    </React.Fragment>
  );
}
```

## 동기

컴포넌트가 자식 목록을 반환하는 것은 일반적인 패턴이다.

```jsx
class Table extends React.Component {
  render() {
    return (
      <table>
        <tr>
          <Columns />
        </tr>
      </table>
    );
  }
}
```

렌더링된 HTML이 유효하려면 `<Columns />`가 복수의 `<td>` 요소를 반환해야 한다. `<Columns />`의 `render()` 안에서 부모로 `<div>`를 사용하면 렌더링된 HTML은 유효하지 않다.

```jsx
class Columns extends React.Component {
  render() {
    return (
      <div>
        <td>Hello</td>
        <td>World</td>
      </div>
    );
  }
}
```

`<Table />`의 출력 결과는 다음과 같다.

```jsx
<table>
  <tr>
    <div>
      <td>Hello</td>
      <td>World</td>
    </div>
  </tr>
</table>
```

Fragment는 이 문제를 해결해 준다.

## 사용법

```jsx
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}
```

`<Table />`의 올바른 출력 결과가 나온다.

```jsx
<table>
  <tr>
    <td>Hello</td>
    <td>World</td>
  </tr>
</table>
```

### 단축 문법

Fragment를 선언하는 새롭고 짧은 문법이 있다. 마치 빈 태그처럼 보인다.

```jsx
class Columns extends React.Component {
  render() {
    return (
      <>
        <td>Hello</td>
        <td>World</td>
      </>
    );
  }
}
```

키나 속성을 지원하지 않는다는 점만 제외하면 다른 요소와 동일한 방식으로 `<></>`을 사용할 수 있다.

### 키가 있는 Fragment

명시적으로 `<React.Fragment>` 문법으로 선언한 Fragment는 키를 가질 수 있다.

다음 예시에서는 설명 목록을 만들기 위해 컬렉션을 Fragment 배열에 매핑하고 있다.

```jsx
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        // 키가 없으면 React가 키 경고를 한다.
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```

키는 Fragment에 건네줄 수 있는 유일한 속성이다.

미래에는 이벤트 핸들러와 같은 추가적인 속성이 지원될 수 있다.