# 질의 무효화

질의를 다시 가져오기 전에 오래된 질의가 될 때까지 기다리는 것이 항상 효과적인 것은 아닙니다. 특히 사용자가 수행한 작업으로 인해 질의의 데이터가 오래되었다는 사실을 아는 경우에는 더욱 그렇습니다. 이를 위해 `QueryClient`에는 질의를 오래된 것으로 지능적으로 표시하고 잠재적으로 다시 가져올 수 있는 `invalidateQueries` 메서드가 있습니다!

```tsx
// 캐시의 모든 질의를 무효화하기
queryClient.invalidateQueries()
// todos로 시작하는 키가 있는 모든 쿼리를 무효화하기
queryClient.invalidateQueries({ queryKey: ['todos'] })
```

> 참고: 정규화된 캐시를 사용하는 다른 라이브러리가 명령적으로 또는 스키마 추론을 통해 새 데이터로 로컬 질의를 갱신하려고 시도하는 경우, 탠스택 쿼리는 대신 정규화된 캐시를 유지하는 데 수반되는 수작업을 피할 수 있는 도구를 제공합니다. 이 도구는 **대상 무효화, 뒤에서 다시 가져오기, 궁극적으로는 원자적 갱신**을 규정합니다.

질의가 `invalidateQueries`로 무효화되면 다음 두 가지 일이 발생합니다.

- 오래된 것으로 표시됩니다. 이 오래된 상태는 `useQuery` 또는 관련 훅에서 사용되는 모든 `staleTime` 설정을 재정의합니다.
- 질의가 현재 `useQuery` 또는 관련 훅을 통해 렌더링되는 경우, 뒤에서도 다시 가져옵니다.

## `invalidateQueries`를 사용한 질의 일치

`invalidateQueries`, `removeQueries`(그리고 부분적인 질의 일치를 지원하는 다른 API)와 같은 API를 사용하면 접두사를 기준으로 여러 질의를 일치시키거나, 매우 구체적인 질의와 일치시킬 수 있습니다. 사용 가능한 필터 유형에 대한 자세한 내용은 [질의 필터](https://tanstack.com/query/latest/docs/react/guides/filters#query-filters)를 참고하세요.

이 예시에서는 `todos` 접두사를 사용하여 질의 키에서 `todos`로 시작하는 모든 질의를 무효화할 수 있습니다.

```tsx
import { useQuery, useQueryClient } from '@tanstack/react-query'

// 컨텍스트에서 QueryClient 가져오기
const queryClient = useQueryClient()

queryClient.invalidateQueries({ queryKey: ['todos'] })

// 이 두 쿼리는 모두 무효화됨
const todoListQuery = useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodoList,
})
const todoListQuery = useQuery({
  queryKey: ['todos', { page: 1 }],
  queryFn: fetchTodoList,
})
```

`invalidateQueries` 메소드에 보다 구체적인 질의 키를 전달하여 특정 변수가 있는 질의를 무효화할 수도 있습니다.

```tsx
queryClient.invalidateQueries({
  queryKey: ['todos', { type: 'done' }],
})

// 이 쿼리는 무효화됨
const todoListQuery = useQuery({
  queryKey: ['todos', { type: 'done' }],
  queryFn: fetchTodoList,
})

// 그러나 이 쿼리는 무효화되지 않음
const todoListQuery = useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodoList,
})
```

`invalidateQueries` API는 매우 유연합니다. 변수나 하위 키가 없는 `todos` 질의**만** 무효화하고 싶다면, `invalidateQueries` 메서드에 `exact: true` 옵션을 전달할 수 있습니다.

```tsx
queryClient.invalidateQueries({
  queryKey: ['todos'],
  exact: true,
})

// 이 쿼리는 무효화됨
const todoListQuery = useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodoList,
})

// 그러나 이 쿼리는 무효화되지 않음
const todoListQuery = useQuery({
  queryKey: ['todos', { type: 'done' }],
  queryFn: fetchTodoList,
})
```

**더 작은** 낟알을 원하면 `invalidateQueries` 메서드에 술어(`predicate`) 함수를 전달할 수 있습니다. 이 함수는 질의 캐시에서 각 `Query` 인스턴스를 수신하고 해당 질의의 무효화 여부에 대해 `true` 또는 `false`를 반환할 수 있습니다.

```tsx
queryClient.invalidateQueries({
  predicate: (query) =>
    query.queryKey[0] === 'todos' && query.queryKey[1]?.version >= 10,
})

// 이 쿼리는 무효화됨
const todoListQuery = useQuery({
  queryKey: ['todos', { version: 20 }],
  queryFn: fetchTodoList,
})

// 이 쿼리는 무효화됨
const todoListQuery = useQuery({
  queryKey: ['todos', { version: 10 }],
  queryFn: fetchTodoList,
})

// 그러나 이 쿼리는 무효화되지 않음
const todoListQuery = useQuery({
  queryKey: ['todos', { version: 5 }],
  queryFn: fetchTodoList,
})
```
