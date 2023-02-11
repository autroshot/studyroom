# 변형 응답에 의한 갱신

서버에서 객체를 **갱신**하는 변형을 처리할 때, 변형의 응답으로 새 객체가 자동으로 반환되는 것이 일반적입니다. 해당 항목에 대한 질의를 다시 가져오고 이미 가지고 있는 데이터에 대한 네트워크 호출을 낭비하는 대신, 변형 함수가 반환한 객체를 활용할 수 있습니다. [Query Client의 `setQueryData`](https://tanstack.com/query/latest/docs/react/reference/QueryClient#queryclientsetquerydata) 메서드를 사용하여 즉시 새 데이터로 기존 질의를 갱신할 수 있습니다.

```tsx
const queryClient = useQueryClient()

const mutation = useMutation({
  mutationFn: editTodo,
  onSuccess: data => {
    queryClient.setQueryData(['todo', { id: 5 }], data)
  }
})

mutation.mutate({
  id: 5,
  name: 'Do the laundry',
})

// 다음 질의는 성공한 변형의 응답으로 갱신됩니다.
const { status, data, error } = useQuery({
  queryKey: ['todo', { id: 5 }],
  queryFn: fetchTodoById,
})
```

`onSuccess` 논리를 재사용 가능한 변형에 연결할 수 있습니다. 이를 위해 다음과 같은 커스텀 훅을 만들 수 있습니다.

```tsx
const useMutateTodo = () => {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: editTodo,
    // 두 번째 인수는 `mutate` 함수가 받는 변수 객체입니다.
    onSuccess: (data, variables) => {
      queryClient.setQueryData(['todo', { id: variables.id }], data)
    },
  })
}
```

## 불변성

`setQueryData`를 통한 갱신은 **불변** 방식으로 수행되어야 합니다. (캐시에서 검색한) 데이터를 변형하여 캐시에 직접 **쓰지 마세요**. 처음에는 작동할 수 있지만 그 과정에서 미묘한 버그가 발생할 수 있습니다.

```tsx
queryClient.setQueryData(
  ['posts', { id }],
  (oldData) => {
    if (oldData) {
      // ❌ 이렇게 하지 마세요.
      oldData.title = 'my new post title'
    }
    return oldData
  })

queryClient.setQueryData(
  ['posts', { id }],
  // ✅ 이게 옳은 방식입니다.
  (oldData) => oldData ? {
    ...oldData,
    title: 'my new post title'
  } : oldData
)
```
