# 도큐사우루스

## 문서 범주 추가하기

새로운 문서 범주를 추가할 때 갱신해야 하는 설정은 다음과 같다.

- `docusaurus.config.js`의 `navbar`와 `footer`
- `sidebars.js`의 `sidebars`

## 메타데이터

문서의 메타데이터는 문서의 처음에 작성한다.

```
---
sidebar_position: 2
sidebar_label: Easy
---

# Easy Tutorial

This is the easy tutorial!
```

폴더 메타데이터는 해당 폴더의 `_category_.yml` 파일에 작성한다.

```
position: 2.5 # 부동 위치 지원
label: 'Tutorial'
```

## 코드 블록

### 제목

````
```jsx title="/src/components/HelloCodeTitle.js"
function HelloCodeTitle(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
````

### 줄 강조

````
```jsx {1,4-6,11}
import React from 'react';

function MyComponent(props) {
  if (props.isBar) {
    return <div>Bar</div>;
  }

  return <div>Foo</div>;
}

export default MyComponent;
```
````

## 준수 사항

```
:::note 참고

Some **content** with _Markdown_ `syntax`. Check [this `api`](#).

:::

:::tip

Some **content** with _Markdown_ `syntax`. Check [this `api`](#).

:::

:::info

Some **content** with _Markdown_ `syntax`. Check [this `api`](#).

:::

:::caution

Some **content** with _Markdown_ `syntax`. Check [this `api`](#).

:::

:::danger

Some **content** with _Markdown_ `syntax`. Check [this `api`](#).

:::
```

