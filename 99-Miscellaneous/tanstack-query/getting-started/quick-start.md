# 빠른 시작

이 코드 조각은 리액트 쿼리의 세 가지 핵심 개념을 간략하게 보여줍니다.

- [질의](https://tanstack.com/query/latest/docs/react/guides/queries)
- [변형](https://tanstack.com/query/latest/docs/react/guides/mutations)
- [질의 무효화](https://tanstack.com/query/latest/docs/react/guides/query-invalidation)

완전히 작동하는 예시를 원한다면 [simple 코드샌드박스 예시](https://tanstack.com/query/latest/docs/react/examples/react/simple)를 참고하세요.

```tsx
import {
  useQuery,
  useMutation,
  useQueryClient,
  QueryClient,
  QueryClientProvider,
} from '@tanstack/react-query'
import { getTodos, postTodo } from '../my-api'

// 클라이언트 생성하기
const queryClient = new QueryClient()

function App() {
  return (
    // 앱에 클라이언트 제공하기
    <QueryClientProvider client={queryClient}>
      <Todos />
    </QueryClientProvider>
  )
}

function Todos() {
  // 클라이언트에 접근하기
  const queryClient = useQueryClient()

  // 질의
  const query = useQuery({ queryKey: ['todos'], queryFn: getTodos })

  // 변형
  const mutation = useMutation({
    mutationFn: postTodo,
    onSuccess: () => {
      // 무효화 및 다시 가져오기
      queryClient.invalidateQueries({ queryKey: ['todos'] })
    },
  })

  return (
    <div>
      <ul>
        {query.data?.map((todo) => (
          <li key={todo.id}>{todo.title}</li>
        ))}
      </ul>

      <button
        onClick={() => {
          mutation.mutate({
            id: Date.now(),
            title: 'Do Laundry',
          })
        }}
      >
        Add Todo
      </button>
    </div>
  )
}

render(<App />, document.getElementById('root'))
```

이 세 가지 개념은 리액트 쿼리의 핵심 기능 대부분을 구성합니다. 문서의 다음 섹션에서는 각 핵심 개념에 대해 자세히 설명합니다.
