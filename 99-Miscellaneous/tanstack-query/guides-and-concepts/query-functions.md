# 질의 함수

질의 함수는 문자 그대로 **프라미스를 반환**하는 모든 함수가 될 수 있습니다. 반환되는 프라미스는 **데이터를 이행**하거나 **오류를 발생**시켜야 합니다.

다음은 모두 유효한 질의 함수 구성입니다.

```tsx
useQuery({ queryKey: ['todos'], queryFn: fetchAllTodos })
useQuery({ queryKey: ['todos', todoId], queryFn: () => fetchTodoById(todoId) })
useQuery({
  queryKey: ['todos', todoId],
  queryFn: async () => {
    const data = await fetchTodoById(todoId)
    return data
  },
})
useQuery({
  queryKey: ['todos', todoId],
  queryFn: ({ queryKey }) => fetchTodoById(queryKey[1]),
})
```

## 오류 처리 및 발생

탠스택 쿼리가 질의에 오류가 있는지 확인하려면, 질의 함수가 반드시 **오류를 발생**시키거나 **거부된 프라미스**를 반환해야 합니다. 질의 함수에서 발생하는 모든 오류는 질의의 `error` 상태에서 지속됩니다.

```tsx
const { error } = useQuery({
  queryKey: ['todos', todoId],
  queryFn: async () => {
    if (somethingGoesWrong) {
      throw new Error('Oh no!')
    }
    if (somethingElseGoesWrong) {
      return Promise.reject(new Error('Oh no!'))
    }

    return data
  },
})
```

## 기본적으로 오류를 발생시키지 않는 기타 클라이언트와 `fetch`의 사용법

`axios` 또는 `graphql-request`와 같은 대부분의 유틸리티는 실패한 HTTP 호출에 대해 자동으로 오류를 발생시키지만, `fetch`와 같은 일부 유틸리티는 기본적으로 오류를 발생시키지 않습니다. 이 경우에는 오류를 직접 발생시켜야 합니다. 인기 있는 `fetch` API를 사용하여 이를 수행하는 간단한 방법은 다음과 같습니다.

```tsx
useQuery({
  queryKey: ['todos', todoId],
  queryFn: async () => {
    const response = await fetch('/todos/' + todoId)
    if (!response.ok) {
      throw new Error('Network response was not ok')
    }
    return response.json()
  },
})
```

## 질의 함수 변수

질의 키는 가져오는 데이터를 고유하게 식별할 뿐만 아니라, `QueryFunctionContext`의 일부로 질의 함수에 편리하게 전달됩니다. 필요하다면 질의 함수를 추출할 수도 있습니다.

```tsx
function Todos({ status, page }) {
  const result = useQuery({
    queryKey: ['todos', { status, page }],
    queryFn: fetchTodoList,
  })
}

// 질의 함수에서 키, 상태, 페이지 변수에 접근합니다!
function fetchTodoList({ queryKey }) {
  const [_key, { status, page }] = queryKey
  return new Promise()
}
```

### `QueryFunctionContext`

`QueryFunctionContext`는 각 질의 함수에 전달되는 객체로 다음으로 구성됩니다.

- `queryKey: QueryKey`: [질의 키](https://tanstack.com/query/latest/docs/react/guides/query-keys)
- `pageParam?: unknown`
   - [무한 질의](https://tanstack.com/query/latest/docs/react/guides/infinite-queries) 전용
   - 현재 페이지를 가져오는 데 사용되는 페이지 매개변수
- `signal?: AbortSignal`
   - 탠스택 쿼리에서 제공하는 [AbortSignal](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal) 인스턴스
   - [질의 취소](https://tanstack.com/query/latest/docs/react/guides/query-cancellation)에 사용 가능
- `meta: Record<string, unknown> | undefined`
   - 질의에 대한 추가 정보로 채울 수 있는 선택적 필드