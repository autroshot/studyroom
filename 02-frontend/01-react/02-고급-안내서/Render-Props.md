# Render Props

render prop은 값이 함수인 prop를 이용해 React 컴포넌트 간에 코드를 공유하는 테크닉이다.

render props가 있는 컴포넌트는 자체 렌더링 로직을 구현하는 대신, React 요소를 반환하고 이를 호출하는 함수를 사용한다.

```jsx
<DataProvider render={data => (
  <h1>Hello {data.target}</h1>
)}/>
```

## 횡단 관심사(Cross-Cutting Concerns)에 render props 사용하기

컴포넌트는 React에서 코드 재사용의 기본 단위이다. 하지만 한 컴포넌트가 캡슐화한 상태나 동작을 동일한 상태가 필요한 다른 컴포넌트에게 공유하는 방법은 명확하지 않다.

예를 들어 다음 컴포넌트는 웹 앱에서 마우스 위치를 추적한다.

```jsx
class MouseTracker extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>
        <h1>Move the mouse around!</h1>
        <p>The current mouse position is ({this.state.x}, {this.state.y})</p>
      </div>
    );
  }
}
```

커서가 화면을 이동할 때 컴포넌트는 (x, y) 좌표를 `<p>`에 표시한다.

이제 다음과 같은 질문을 던질 수 있다. 이 동작을 다른 컴포넌트에서 어떻게 재사용할 수 있을까? 즉 다른 컴포넌트가 커서 위치에 대해 알아야 할 때, 공유가 쉽도록 해당 동작을 캡슐화할 수 있을까?

React에서 컴포넌트는 코드 재사용의 기본 단위이다. 따라서 다른 곳에서 재사용해야 하는 동작을 캡슐화하는 `<Mouse>` 컴포넌트를 사용하도록 코드를 약간 리팩터링해 보겠다.

```jsx
// <Mouse> 컴포넌트는 우리에게 필요한 동작을 캡슐화한다.
class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>

        {/* 하지만 <p>가 아닌 다른 것에 렌더링하려면 어떻게 해야 할까? */}
        <p>The current mouse position is ({this.state.x}, {this.state.y})</p>
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <>
        <h1>Move the mouse around!</h1>
        <Mouse />
      </>
    );
  }
}
```

이제 `<Mouse>` 컴포넌트는 이벤트 수신과 커서의 (x, y) 위치를 저장과 관련된 모든 동작을 캡슐화한다. 하지만 아직은 재사용이 불가능하다.

예를 들어 화면의 마우스를 쫓는 고양이의 이미지를 렌더링하는 컴포넌트가 있다고 가정해 보겠다. `<Cat mouse={{x, y}}>` prop로 컴포넌트에게 마우스 좌표를 알려줘 화면에서 이미지가 있을 위치를 알 수 있을 것이다.

첫 번째 단계로, `<Mouse>`의 렌더링 메서드에서 `<Cat>`을 렌더링해 볼 수 있다.

```jsx
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    );
  }
}

class MouseWithCat extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>

        {/*
          여기서 <p>을 <Cat>으로 바꿀 수 있다.
          그러나 그때마다 별도의 <MouseWithSomethingElse> 컴포넌트를 만들어야 한다.
          아직은 재사용이 불가능하다.
        */}
        <Cat mouse={this.state} />
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <MouseWithCat />
      </div>
    );
  }
}
```

이 접근 방식은 특정 유스 케이스에서만 작동할 뿐, 동작을 재사용 가능하게 캡슐화한다는 목표는 달성하지 못했다. 이제 다른 유스 케이스에서 마우스 위치가 필요할 때마다, 해당 유스 케이스만을 위한 새로운 컴포넌트를 만들어야 한다.

여기서 render prop을 사용할 수 있다. `<Mouse>` 컴포넌트 안에 `<Cat>`을 하드 코딩해서 렌더링 출력을 변경하는 대신, 렌더링 대상을 동적으로 결정하는 데 사용하는 함수 prop(render prop)을 `<Mouse>`에 제공하는 것이다.

```jsx
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    );
  }
}

class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>

        {/*
          <Mouse>가 렌더링하는 것을 정적으로 표현하는 대신,
          동적으로 결정하려면 'render' prop을 이용한다.
        */}
        {this.props.render(this.state)}
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}
```

이제 특정한 유스 케이스를 해결하기 위해 컴포넌트를 복제하고 `render` 메서드에 다른 것을 하드 코딩하는 대신, 렌더링 대상을 동적으로 결정하는 데 사용할 `render` prop를 제공한다.

구체적으로 말하면, **render prop은 컴포넌트에게 렌더링 대상을 알려주는 함수 prop이다.**

이 테크닉은 '동작'을 공유가 쉽게 만들어 준다. 해당 동작을 얻으려면, 커서의 현재 (x, y)로 무엇을 렌더링할지를 알려주는 `render` prop과 함께 `<Mouse>`를 렌더링하면 된다.

render props에 대한 한 가지 흥미로운 점은 일반적인 컴포넌트를 이용해 대부분의 [고차 컴포넌트](https://ko.reactjs.org/docs/higher-order-components.html)(HOC)를 구현할 수 있다는 것이다.

예를 들어 `<Mouse>` 컴포넌트 대신 `withMouse` HOC 사용하고 싶다면, 렌더링 prop이 있는 일반 `<Mouse>`를 이용해 쉽게 만들 수 있다.

```jsx
function withMouse(Component) {
  return class extends React.Component {
    render() {
      return (
        <Mouse render={mouse => (
          <Component {...this.props} mouse={mouse} />
        )}/>
      );
    }
  }
}
```

따라서 render prop을 사용하면 두 패턴 중 하나를 사용할 수 있다.

## `render` 이외의 Props 사용하기

패턴의 이름이 'render props'이라고 이 패턴을 사용하려면 prop 이름이 render인 것만 사용해야 하는 것은 아니다. 컴포넌트가 무엇을 렌더링할지 알기 위해 사용하는 모든 함수 prop은 render prop이다.

위 예시에서는 `render`를 사용했지만 쉽게 `children` prop을 사용해도 된다.

```jsx
<Mouse children={mouse => (
  <p>The mouse position is {mouse.x}, {mouse.y}</p>
)}/>
```

그리고 `children` prop이 JSX 요소의 속성 목록에 있어야 하는 것은 아니다. 대신 요소 안에 직접 넣어도 된다.

```jsx
<Mouse>
  {mouse => (
    <p>The mouse position is {mouse.x}, {mouse.y}</p>
  )}
</Mouse>
```

이 테크닉은 조금 특이하기 때문에 이런 API를 설계할 때는 `children`이 함수여야 한다고 `propTypes`에 명시하는 것이 좋다.

```jsx
Mouse.propTypes = {
  children: PropTypes.func.isRequired
};
```