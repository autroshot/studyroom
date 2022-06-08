# Hook 소개

Hook은 React 16.8에서 새로 추가되었다. Hook을 이용하면 class 없이 상태와 기타 React 기능을 사용할 수 있다.

```js
import React, { useState } from 'react';

function Example() {
  // "count"라는 새로운 상태 변수를 선언한다.
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

## 기존의 틀을 깨는 변경점은 없다

Hook은 다음과 같은 특징을 지닌다.

- **완전히 선택 사항이다.** 기존의 코드를 다시 작성할 필요 없이 일부 컴포넌트에서 Hook을 사용할 수 있다. 그러나 원하지 않으면 당장 Hook을 배우거나 사용할 필요는 없다.
- **이전 버전과 100% 호환된다.** Hook은 기존의 틀을 깨지 않는다.
- **React에서 클래스를 제거할 계획은 없다.**
- **Hook은 기존의 React 개념들을 대체하지 않는다.** 대신 Hook은 기존의 props, 상태, 컨텍스트, refs, 생명주기와 같은 React 개념에 대한 직접적인 API를 제공한다. 또한 Hook은 이 개념들을 엮는 새롭고 강력한 방법을 제공한다.

## Hook이 만들어진 동기

- 컴포넌트 간에 상태 로직의 재사용이 어렵다.
- 복잡한 컴포넌트는 이해하기 어렵다.
- 클래스는 사람과 기계 모두를 혼란스럽게 만든다.
