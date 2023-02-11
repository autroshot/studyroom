# 변형에 의한 무효화

질의 무효화는 전투의 절반에 불과합니다. 나머지 절반은 무효화할 **시기**를 아는 것입니다. 일반적으로 앱에서 변형이 성공했을 때, 변형으로 인한 새로운 변경 사항을 처리하는 무효화 및 다시 가져오기 관련 질의가 앱에 있을 가능성이 매우 높습니다.

예를 들어 새로운 할 일을 게시하는 변형이 있다고 가정해 보겠습니다.

```tsx
const mutation = useMutation({ mutationFn: postTodo })
```

`postTodo` 변형이 성공하면, 모든 `todos` 질의를 무효화하고 새로운 할 일 항목을 표시하기 위해 다시 가져오는 것을 원할 수 있습니다. 이때, `useMutation`의 `onSuccess` 옵션과 `client`의 `invalidateQueries` 함수를 사용할 수 있습니다.

```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query'

const queryClient = useQueryClient()

// 이 변형이 성공하면 `todos` 또는 `reminders` 질의 키가 있는 모든 질의를 무효화합니다.
const mutation = useMutation({
  mutationFn: addTodo,
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ['todos'] })
    queryClient.invalidateQueries({ queryKey: ['reminders'] })
  },
})
```

[`useMutation` 훅](https://tanstack.com/query/latest/docs/react/guides/mutations)의 콜백에서 무효화하는 것도 가능합니다.

