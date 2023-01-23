# 질의

## 질의 기초

질의(query)는 **고유 키**에 연결된 데이터의 비동기 소스에 대한 선언적 종속입니다. 프라미스 기반 메서드(GET 및 POST 메서드 포함)와 함께 질의를 사용하여 서버에서 데이터를 가져올 수 있습니다. 메서드가 서버의 데이터를 수정하는 경우에는 대신 [변형](https://tanstack.com/query/latest/docs/react/guides/mutations)을 사용하는 것이 좋습니다.

컴포넌트 또는 사용자 정의 훅에서 쿼리를 구독하려면 최소한 다음과 함께 `useQuery` 훅을 호출합니다.

- **질의 고유 키**
- 다음의 프라미스를 반환하는 함수
   - 데이터를 이행(resolve)하거나
   - 오류를 발생시킴

```tsx
import { useQuery } from '@tanstack/react-query'

function App() {
  const info = useQuery({ queryKey: ['todos'], queryFn: fetchTodoList })
}
```

제공한 **고유 키**는 앱 전체에서 질의를 다시 가져오고, 캐싱하고, 공유하는 데에 내부적으로 사용됩니다.

`useQuery`에서 반환된 질의 결과에는 템플릿 및 기타 데이터 사용에 필요한 질의에 대한 모든 정보가 포함되어 있습니다.

```tsx
const result = useQuery({ queryKey: ['todos'], queryFn: fetchTodoList })
```

`result` 객체에는 생산성을 높이기 위해 알아야 할 매우 중요한 상태가 몇 가지 포함되어 있습니다. 질의는 항상 다음 상태 중 하나만 가능합니다.

- `isLoading` 또는 `status === 'loading'` - 질의에 아직 데이터가 없음
- `isError` 또는 `status === 'error'` - 질의에 오류가 발생함
- `isSuccess` 또는 `status === 'success'` - 쿼리가 성공했으며 데이터를 사용할 수 있음

이러한 주요 상태 외에도 질의 상태에 따라 다음과 같은 추가 정보를 사용할 수 있습니다.

- `error` - 질의가 `isError` 상태이면 `error` 프로퍼티로 오류를 사용할 수 있습니다.
- `data` - 질의가 `success` 상태이면 `data` 프로퍼티로 데이터를 사용할 수 있습니다.

**대부분**의 질의는 `isLoading` 상태를 확인하고, `isError` 상태를 확인하고, 데이터를 사용할 수 있다고 가정하고 성공 상태를 렌더링하는 것으로 충분합니다.

```tsx
function Todos() {
  const { isLoading, isError, data, error } = useQuery({
    queryKey: ['todos'],
    queryFn: fetchTodoList,
  })

  if (isLoading) {
    return <span>Loading...</span>
  }

  if (isError) {
    return <span>Error: {error.message}</span>
  }

  // 이 시점에서 `isSuccess === true`라고 가정할 수 있습니다.
  return (
    <ul>
      {data.map((todo) => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  )
}
```

불린이 마음에 들지 않으면 항상 `status`도 사용할 수 있습니다.

```tsx
function Todos() {
  const { status, data, error } = useQuery({
    queryKey: ['todos'],
    queryFn: fetchTodoList,
  })

  if (status === 'loading') {
    return <span>Loading...</span>
  }

  if (status === 'error') {
    return <span>Error: {error.message}</span>
  }

  // 여기서는 else 논리를 사용했지만 `status === 'success'`도 가능합니다.
  return (
    <ul>
      {data.map((todo) => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  )
}
```

`data`에 접근하기 전에 `loading`과 `error`를 확인했다면 타입스크립트는 `data`의 타입을 올바르게 좁힐 것입니다.

### 가져오기 상태

`result` 객체에서 `status` 필드 외에, 다음의 옵션과 함께 추가 `fetchStatus` 프로퍼티도 얻을 수 있습니다.

- `fetchStatus === 'fetching'` - 질의가 현재 가져오는 중
- `fetchStatus === 'paused'` - 질의가 가져오려고 했지만 일시 중지됨 (자세한 내용은 [네트워크 모드](https://tanstack.com/query/latest/docs/react/guides/network-mode) 안내서를 참고)
- `fetchStatus === 'idle'` - 질의가 현재 아무 작업도 수행하지 않음

### 왜 상태가 두 개인가요?

뒤에서 다시 가져오기 및 `stale-while-revalidate` 논리는 `status` 및 `fetchStatus`에 대한 모든 조합을 가능하게 합니다.

예시:

- `success` 상태의 질의는 일반적으로 `idle` `fetchStatus`이지만, 뒤에서 다시 가져오기가 발생하면 `fetching`일 수도 있습니다.
- 마운트되고 데이터가 없는 질의는 일반적으로 `loading` 상태와 `fetching` `fetchStatus`이지만, 네트워크 연결이 없다면 `paused`일 수도 있습니다.

따라서 실제로 데이터를 가져오지 않고도 질의가 `loading` 상태일 수 있음을 주의하세요.

일반적으로 다음과 같습니다.

- `status`는 `data`에 대한 정보(데이터의 유무)를 제공
- `fetchStatus`는 `queryFn`에 대한 정보(실행 여부)를 제공

## 추가 자료

상태 확인을 수행하는 다른 방법은 [커뮤니티 자원](../community/tkdodos-blog#4-status-checks-in-react-query)을 참고하세요.