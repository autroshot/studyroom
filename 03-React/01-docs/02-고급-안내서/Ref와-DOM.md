# Ref와 DOM

Ref는 render 메서드에서 생성된 DOM 노드나 React 요소에 접근하는 방법을 제공한다.

일반적인 React 데이터 흐름에서 props는 부모 컴포넌트가 자신의 자식과 상호 작용할 수 있는 유일한 수단이다. 자식을 수정하려면 새로운 props로 자식을 리렌더링해야 한다.

그러나 일반적인 데이터 흐름에서 벗어나 자식을 직접 수정해야 하는 경우도 존재한다. 수정할 자식은 React 컴포넌트의 인스턴스이거나 DOM 요소일 수 있다. React는 두 경우를 위한 해결책을 제공한다.

### Ref가 필요한 경우

Ref의 좋은 유스 케이스는 다음과 같다.

- 포커스, 텍스트 셀렉션, 미디어의 재생을 관리하기
- 애니메이션 직접 트리거하기
- 서드 파티 DOM 라이브러리와 통합하기

선언적으로 수행할 수 있는 것에 Ref를 사용하는 것은 피해야 한다.

예를 들어 `Dialog` 컴포넌트에서 `open()`과 `close()` 메서드를 노출하는 대신 컴포넌트에 `isOpen` prop을 건네주는 것이다.

### Ref를 남용해서는 안 된다

앱에 뭔가를 일어나게 만드는 방법으로 Ref을 먼저 떠올릴 수 있다. 이때 잠시 시간을 내어 컴포넌트 계층에서 상태를 가져야 하는 것이 어떤 것인지를 비판적으로 고민할 필요가 있다. 계층 구조상 높은 곳에 있는 컴포넌트가 상태를 소유하는 것이 적절하다는 결론을 많이 내리게 된다. 이에 대한 예시는 [상태 끌어올리기](../01-주요-개념/10-상태-끌어올리기.md)에서 확인할 수 있다.

### Ref 생성하기

Ref는 `React.createRef()`로 생성되고 `ref` 속성으로 React 요소에 부착된다. 일반적으로 Ref는 컴포넌트 전체에서 참조할 수 있도록 컴포넌트가 만들어질 때 인스턴스 프로퍼티에 할당된다.

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
    
  render() {
    return <div ref={this.myRef} />;
  }
}
```

### Ref에 접근하기

`render`에서 ref가 요소에게 전달되었을 때, ref의 `current` 속성을 이용해 노드에 대한 참조에 접근할 수 있다.

```jsx
const node = this.myRef.current;
```

ref 값은 노드의 유형에 따라 다르다.

- `ref` 속성이 HTML 속성에서 사용되었다면, 생성자에서 `React.createRef()`로 생성된 `ref`는 `current` 프로퍼티 값으로 해당 DOM 요소를 받게 된다.

- `ref` 속성이 커스텀 클래스 컴포넌트에서 사용되었다면, `ref` 객체는 `current`로 마운트된 컴포넌트의 인스턴스를 받는다.

- 함수 컴포넌트는 인스턴스가 없기 때문에 ref 속성을 사용할 수 없다.

아래의 예시들은 그 차이점을 보여준다.

#### DOM 요소에 Ref 추가하기

이 코드는 DOM 노드에 대한 참조를 저장하기 위해 `ref`를 사용한다.

```jsx
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    // textInput DOM 요소를 저장할 Ref를 생성한다.
    this.textInput = React.createRef();
    this.focusTextInput = this.focusTextInput.bind(this);
  }

  focusTextInput() {
    // 기본 DOM API를 사용해 명시적으로 textInput을 포커스한다.
    // 주의: DOM 노드를 얻기 위해 'current'에 접근하고 있다.
    this.textInput.current.focus();
  }

  render() {
   // React에게 생성자에서 만든 textInput과 <input> Ref를 연결하고 싶다고 말한다.
    return (
      <div>
        <input
          type="text"
          ref={this.textInput} />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

React는 컴포넌트가 마운트될 때 `current` 프로퍼티에 DOM 요소를 할당하고 언마운트될 때 `null`로 되돌린다. `ref` 업데이트는 생명주기 메서드 `componentDidMount`나 `componentDidUpdate` 이전에 일어난다.

#### 클래스 컴포넌트에 ref 추가하기

마운트 직후에 클릭되는 것을 모방하기 위해 `CustomTextInput`을 래핑하고 싶다면, Ref를 이용해 `CustomTextInput`에 접근하고 `focusTextInput` 메서드를 수동으로 호출할 수 있다.

```jsx
class AutoFocusTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }

  componentDidMount() {
    this.textInput.current.focusTextInput();
  }

  render() {
    return (
      <CustomTextInput ref={this.textInput} />
    );
  }
}
```

이 코드는 `CustomTextInput`가 클래스로 선언되었을 때만 작동한다는 점을 주의해야 한다.

```jsx
class CustomTextInput extends React.Component {
  // ...
}
```

#### Ref와 함수 컴포넌트

기본적으로 함수 컴포넌트는 인스턴스가 없기 때문에 ref 속성을 사용할 수 없다.

```jsx
function MyFunctionComponent() {
  return <input />;
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }
    
  render() {
    // 작동하지 않는다.
    return (
      <MyFunctionComponent ref={this.textInput} />
    );
  }
}
```

함수 컴포넌트에서 `ref`를 사용하려면 [`useImperativeHandle`](https://ko.reactjs.org/docs/hooks-reference.html#useimperativehandle)와 [`forwardRef`](https://ko.reactjs.org/docs/forwarding-refs.html)를 사용하거나 컴포넌트를 클래스로 변환해야 한다.

그러나 DOM 요소나 클래스 컴포넌트를 참조하는 것을 원하면, 함수 컴포넌트에서 `ref` 속성을 사용할 수 있다.

```jsx
function CustomTextInput(props) {
  // Ref가 textInput 요소를 참조하려면 textInput이 이곳에서 선언되어야 한다.
  const textInput = useRef(null);
  
  function handleClick() {
    textInput.current.focus();
  }

  return (
    <div>
      <input
        type="text"
        ref={textInput} />
      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  );
}
```

### 콜백 Ref

React는 Ref를 설정하는 또 다른 방법인 콜백 Ref를 지원한다. 이 방법은 Ref가 설정되고 해제되는 때를 세밀하게 제어할 수 있다.

`createRef()`로 생성한 `ref` 속성 대신 함수를 전달한다. 함수는 다른 곳에서 저장하고 접근할 수 있는 React 컴포넌트 인스턴스나 HTML DOM 요소를 인수로 받는다.

아래 예시에서는 `ref` 콜백을 사용해 DOM 노드에 대한 참조를 인스턴스 프로퍼티에 저장한다.

```jsx
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);

    this.textInput = null;

    this.setTextInputRef = element => {
      this.textInput = element;
    };

    this.focusTextInput = () => {
      // 기본 DOM API를 사용해 textInput을 포커스한다.
      if (this.textInput) this.textInput.focus();
    };
  }

  componentDidMount() {
    // 마운트될 때 input을 자동으로 포커스한다.
    this.focusTextInput();
  }

  render() {
    // Ref 콜백을 이용해 인스턴스 필드(this.textInput)에
    // textInput DOM 요소에 대한 참조를 저장한다.
    return (
      <div>
        <input
          type="text"
          ref={this.setTextInputRef}
        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

React는 컴포넌트가 마운트될 때 DOM 요소로 `ref` 콜백을 호출하고 언마운트될 때 `null`로 `ref` 콜백을 호출한다. Ref는 `componentDidMount` 또는 `componentDidUpdate`가 실행되기 전에 최신 상태임이 보장된다.

`React.createRef()`로 생성한 객체 Ref처럼 콜백 Ref도 컴포넌트 간에 건네줄 수 있다.

```jsx
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }
}
```

위의 예시에서 `Parent`는 자신의 콜백 ref를 `inputRef` prop으로서 `CustomTextInput`에게 전달합니다. 그리고 `CustomTextInput`은 전달받은 함수를 `<input>`에게 `ref` 어트리뷰트로서 전달합니다. 결과적으로 `Parent`에 있는 `this.inputElement`는 `CustomTextInput`의 `<input>` 엘리먼트에 대응하는 DOM 노드가 됩니다.

위의 예시에서 `Parent`는 자신의 콜백 Ref를 `CustomTextInput`에게 `inputRef` prop로 건네준다. 그리고 `CustomTextInput`은 받은 함수를 `<input>`에게 `ref` 속성으로 건네준다. 그 결과 `Parent`의 `this.inputElement`는 `CustomTextInput`의 `<input>` 요소에 해당하는 DOM 노드로 설정될 것이다.

### 콜백 Ref 관련 주의 사항

`ref` 콜백이 인라인 함수로 선언되었다면, 콜백은 업데이트 동안 두 번 호출될 것이다. 처음은 `null`, 두 번째는 DOM 요소로 호출된다.

이는 매 렌더링마다 함수의 새로운 인스턴스가 생성되서 React가 오래된 Ref를 제거하고 새 Ref를 설정해야 하기 때문이다. `ref` 콜백을 클래스에 바인딩된 메서드로 정의해서 이를 피할 수 있지만, 대부분의 경우에는 이 현상이 문제가 되지 않는다.