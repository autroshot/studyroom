# React

웹을 위한 라이브러리다.

컴포넌트라고 불리는 조각들로 UI를 구성할 수 있다.

## 컴포넌트

```jsx
function MyButton() {
  return (
    <button>I'm a button</button>
  );
}
```

- React 앱은 컴포넌트로 구성된다.
- 컴포넌트는 UI의 일부로 고유한 논리와 모양을 가진다.
- 컴포넌트는 [마크업](https://ko.wikipedia.org/wiki/%EB%A7%88%ED%81%AC%EC%97%85_%EC%96%B8%EC%96%B4)을 반환하는 JavaScript 함수이다.
- 보통 JSX를 사용하여 작성한다.

## 목록 렌더링하기

```jsx
export default function ShoppingList() {
  const products = [
    { title: 'Cabbage', isFruit: false, id: 1 },
    { title: 'Garlic', isFruit: false, id: 2 },
    { title: 'Apple', isFruit: true, id: 3 },
  ];
  
  const listItems = products.map(product =>
    <li
      key={product.id}
      style={{
        color: product.isFruit ? 'magenta' : 'darkgreen'
      }}
    >
      {product.title}
    </li>
  );

  return (
    <ul>{listItems}</ul>
  );
}
```

### `key` 규칙

- 키는 형제 간에 고유해야 한다.
- 키는 렌더링 간에 바뀌면 안 된다. 따라서 배열의 색인은 키에 적합하지 않다.

## 이벤트에 응답하기

```jsx
function MyButton() {
  function handleClick() {
    alert('You clicked me!');
  }

  return (
    <button onClick={handleClick}>
      Click me
    </button>
  );
}
```

함수를 호출하지 않고 전달한다는 점에 유의한다.