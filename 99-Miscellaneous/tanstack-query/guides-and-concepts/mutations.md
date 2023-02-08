# 변형

질의와 달리 변형은 일반적으로 데이터를 생성·갱신·삭제하거나 서버 부작용을 수행하는 데 사용됩니다. 이를 위해 탠스택 쿼리는 `useMutation` 훅을 내보냅니다.

다음은 서버에 새 할 일을 추가하는 변형의 예입니다.

```tsx
function App() {
  const mutation = useMutation({
    mutationFn: (newTodo) => {
      return axios.post('/todos', newTodo)
    },
  })

  return (
    <div>
      {mutation.isLoading ? (
        'Adding todo...'
      ) : (
        <>
          {mutation.isError ? (
            <div>An error occurred: {mutation.error.message}</div>
          ) : null}

          {mutation.isSuccess ? <div>Todo added!</div> : null}

          <button
            onClick={() => {
              mutation.mutate({ id: new Date(), title: 'Do Laundry' })
            }}
          >
            Create Todo
          </button>
        </>
      )}
    </div>
  )
}
```

변형은 항상 다음 상태 중 하나만 가능합니다.

- `isIdle` 또는 `status === 'idle'` - 변형이 현재 유휴 상태이거나 신선하거나 재설정 상태
- `isLoading` 또는 `status === 'loading'` - 변형이 현재 실행 중
- `isError` 또는 `status === 'error'` - 변형에서 오류가 발생
- `isSuccess` 또는 `status === 'success'` - 변형이 성공했으며 변형 데이터를 사용할 수 있음

이러한 주요 상태 외에도 변형 상태에 따라 더 많은 정보를 사용할 수 있습니다.

- `error` - 변형이 `error` 상태이면, `error` 프로퍼티로 오류를 사용할 수 있습니다.
- `data` - 변형이 `success` 상태이면, `data` 프로퍼티로 데이터를 사용할 수 있습니다.

위의 예시에서 **하나의 변수 또는 객체**로 `mutate` 함수를 호출하여 변수를 변형 함수에 전달할 수 있음을 확인했습니다.

변수만 사용하면 변형이 그리 특별하지 않지만, `onSuccess` 옵션, [질의 클라이언트의 `invalidateQueries` 메서드](https://tanstack.com/query/latest/docs/react/reference/QueryClient#queryclientinvalidatequeries), [질의 클라이언트의 `setQueryData` 메서드](https://tanstack.com/query/latest/docs/react/reference/QueryClient#queryclientsetquerydata)와 함께 사용하면 변형이 매우 강력한 도구가 됩니다.

> 중요: `mutate` 함수는 비동기 함수이므로 **React 16 이하**의 이벤트 콜백에서 직접 사용할 수 없습니다. `onSubmit`의 이벤트에 접근해야 한다면 다른 함수에서 `mutate`를 래핑해야 합니다. 이는 [리액트 이벤트 풀링](https://reactjs.org/docs/legacy-event-pooling.html) 때문입니다.

```tsx
// React 16 이하 버전에서는 작동하지 않습니다.
const CreateTodo = () => {
  const mutation = useMutation({
    mutationFn: (event) => {
      event.preventDefault()
      return fetch('/api', new FormData(event.target))
    },
  })

  return <form onSubmit={mutation.mutate}>...</form>
}

// 잘 작동합니다.
const CreateTodo = () => {
  const mutation = useMutation({
    mutationFn: (formData) => {
      return fetch('/api', formData)
    },
  })
  const onSubmit = (event) => {
    event.preventDefault()
    mutation.mutate(new FormData(event.target))
  }

  return <form onSubmit={onSubmit}>...</form>
}
```

## 변형 상태 재설정

변형 요청의 `error` 또는 `data`를 지워야 하는 경우가 있습니다. `reset` 함수를 사용하여 이를 처리할 수 있습니다.

```tsx
const CreateTodo = () => {
  const [title, setTitle] = useState('')
  const mutation = useMutation({ mutationFn: createTodo })

  const onCreateTodo = (e) => {
    e.preventDefault()
    mutation.mutate({ title })
  }

  return (
    <form onSubmit={onCreateTodo}>
      {mutation.error && (
        <h5 onClick={() => mutation.reset()}>{mutation.error}</h5>
      )}
      <input
        type="text"
        value={title}
        onChange={(e) => setTitle(e.target.value)}
      />
      <br />
      <button type="submit">Create Todo</button>
    </form>
  )
}
```

## 변형 부작용

`useMutation`은 변형의 수명 주기 동안 모든 단계에서 빠르고 쉬운 부작용을 허용하는 몇 가지 도우미 옵션과 함께 제공됩니다. 이는 [변형 후 질의 무효화 및 다시 가져오기](https://tanstack.com/query/latest/docs/react/guides/invalidations-from-mutations)와 [낙관적 갱신](https://tanstack.com/query/latest/docs/react/guides/optimistic-updates) 모두에 유용합니다.

```tsx
useMutation({
  mutationFn: addTodo,
  onMutate: (variables) => {
    // 변형이 곧 일어납니다!

    // 예를 들어 롤백할 때 사용할 데이터가 포함된 컨텍스트를 선택적으로 반환합니다.
    return { id: 1 }
  },
  onError: (error, variables, context) => {
    // 오류가 발생했습니다!
    console.log(`rolling back optimistic update with id ${context.id}`)
  },
  onSuccess: (data, variables, context) => {
    // 붐 베이비!
  },
  onSettled: (data, error, variables, context) => {
    // 오류 또는 성공... 상관없습니다!
  },
})
```

콜백 함수에서 프라미스를 반환하면 다음 콜백이 호출되기 전에 먼저 대기합니다.

```tsx
useMutation({
  mutationFn: addTodo,
  onSuccess: async () => {
    console.log("I'm first!")
  },
  onSettled: async () => {
    console.log("I'm second!")
  },
})
```

`mutate`를 호출할 때 `useMutation`에 정의된 콜백 외에 **추가 콜백을 트리거**하고 싶을 수 있습니다. 이는 컴포넌트별 부작용을 유발하는 데 사용할 수 있습니다.

컴포넌트별 부작용을 위해 `mutate` 함수의 변형 변수 다음에 동일한 콜백 옵션을 제공할 수 있습니다. 지원되는 옵션은 `onSuccess`, `onError`, `onSettled`입니다. 변형이 완료되기 **전에** 컴포넌트가 언마운트되면 이러한 추가 콜백이 실행되지 않는다는 점에 유의하세요.

```tsx
useMutation({
  mutationFn: addTodo,
  onSuccess: (data, variables, context) => {
    // 첫 번째로 실행됨
  },
  onError: (error, variables, context) => {
    // 첫 번째로 실행됨
  },
  onSettled: (data, error, variables, context) => {
    // 첫 번째로 실행됨
  },
})

mutate(todo, {
  onSuccess: (data, variables, context) => {
    // 두 번째로 실행됨!
  },
  onError: (error, variables, context) => {
    // 두 번째로 실행됨!
  },
  onSettled: (data, error, variables, context) => {
    // 두 번째로 실행됨!
  },
})
```

### 연속 변형

연속 변형에서 `onSuccess`, `onError`, `onSettled` 콜백을 처리하는 데 약간의 차이가 있습니다. `mutate` 함수에 전달되면 컴포넌트가 여전히 마운트되어 있는 경우에만 **한 번** 실행됩니다. 이는 `mutate` 함수가 호출될 때마다 변형 관찰자가 제거되고 다시 구독되기 때문입니다. 반대로 `useMutation` 핸들러는 `mutate` 호출마다 실행됩니다.

> 대부분의 경우 `useMutation`에 전달된 `mutationFn`은 비동기적입니다. 이 경우 변형이 이행되는 순서는 `mutate` 함수 호출의 순서와 다를 수 있습니다.

```tsx
useMutation({
  mutationFn: addTodo,
  onSuccess: (data, error, variables, context) => {
    // 세 번 호출됨
  },
})

[('Todo 1', 'Todo 2', 'Todo 3')].forEach((todo) => {
  mutate(todo, {
    onSuccess: (data, error, variables, context) => {
      // 어떤 변형이 먼저 이행되는지에 관계없이,
      // 마지막 변형 (Todo 3)에 대해 한 번만 실행됨
    },
  })
})
```

## 프라미스

`mutate` 대신 `mutateAsync`를 사용하여 성공 시 이행되거나 오류가 발생하는 프라미스를 가져옵니다. 예를 들어 부작용을 구성하는 데 사용할 수 있습니다.

```tsx
const mutation = useMutation({ mutationFn: addTodo })

try {
  const todo = await mutation.mutateAsync(todo)
  console.log(todo)
} catch (error) {
  console.error(error)
} finally {
  console.log('done')
}
```

## 재시도

기본적으로 탠스택 쿼리는 오류 발생 시 변형을 재시도하지 않지만 `재시도` 옵션을 사용하면 가능합니다.

```tsx
const mutation = useMutation({
  mutationFn: addTodo,
  retry: 3,
})
```

장치가 오프라인 상태여서 변형이 실패하면, 장치가 다시 연결될 때 동일한 순서로 다시 시도됩니다.

## 변형 지속하기

필요하다면 변형을 스토리지에 유지하고 나중에 재개할 수 있습니다. 수화 함수로 이를 수행할 수 있습니다.

```tsx
const queryClient = new QueryClient()

// addTodo 변형 정의하기
queryClient.setMutationDefaults(['addTodo'], {
  mutationFn: addTodo,
  onMutate: async (variables) => {
    // 할 일 목록에 대한 현재 질의를 취소하기
    await queryClient.cancelQueries({ queryKey: ['todos'] })

    // 낙관적인 할 일 생성하기
    const optimisticTodo = { id: uuid(), title: variables.title }

    // 할 일 목록에 낙관적 할 일 추가하기
    queryClient.setQueryData(['todos'], (old) => [...old, optimisticTodo])

    // 낙관적 할 일과 함께 컨텍스트를 반환하기
    return { optimisticTodo }
  },
  onSuccess: (result, variables, context) => {
    // 할 일 목록의 낙관적 할 일을 결과로 바꾸기
    queryClient.setQueryData(['todos'], (old) =>
      old.map((todo) =>
        todo.id === context.optimisticTodo.id ? result : todo,
      ),
    )
  },
  onError: (error, variables, context) => {
    // 할 일 목록에서 낙관적 할 일 제거하기
    queryClient.setQueryData(['todos'], (old) =>
      old.filter((todo) => todo.id !== context.optimisticTodo.id),
    )
  },
  retry: 3,
})

// 일부 컴포넌트에서 변형 시작하기
const mutation = useMutation({ mutationKey: ['addTodo'] })
mutation.mutate({ title: 'title' })

// 예를 들어 장치가 오프라인 상태이기 때문에 변형이 일시 중지된 경우,
// 앱이 종료될 때 일시 중지된 변형이 탈수될 수 있습니다.
const state = dehydrate(queryClient)

// 그런 다음 앱이 시작될 때 변형을 다시 수화할 수 있습니다.
hydrate(queryClient, state)

// 일시 중지된 변형을 재개하기
queryClient.resumePausedMutations()
```

### 오프라인 변형 지속하기

[`persistQueryClient` 플러그인](https://tanstack.com/query/latest/docs/react/plugins/persistQueryClient)으로 오프라인 변형을 유지하는 경우, 기본 변형 함수를 제공하지 않으면 페이지를 다시 로드할 때 변형을 재개할 수 없습니다.

이는 기술적 한계입니다. 외부 저장소에 유지되면 함수를 직렬화할 수 없으므로 변형 상태만 지속됩니다. 수화 후에 변형를 유발하는 컴포넌트가 마운트되지 않을 수 있으므로 `resumePausedMutations`를 호출하면 `No mutationFn found` 오류가 발생할 수 있습니다.

```tsx
const persister = createSyncStoragePersister({
  storage: window.localStorage,
})
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      cacheTime: 1000 * 60 * 60 * 24, // 24 hours
    },
  },
})

// 페이지를 다시 로드한 후 일시 중지된 변형을 다시 시작할 수 있도록 기본 변형 함수가 필요합니다.
queryClient.setMutationDefaults(['todos'], {
  mutationFn: ({ id, data }) => {
    return api.updateTodo(id, data)
  },
})

export default function App() {
  return (
    <PersistQueryClientProvider
      client={queryClient}
      persistOptions={{ persister }}
      onSuccess={() => {
        // localStorage에서 초기 복원이 성공한 후 변형 재개하기
        queryClient.resumePausedMutations()
      }}
    >
      <RestOfTheApp />
    </PersistQueryClientProvider>
  )
}
```

또한 질의와 변형을 모두 다루는 광범위한 [오프라인 예시](https://tanstack.com/query/latest/docs/react/examples/react/offline)도 있습니다.

## 더 읽을거리

변형에 대한 자세한 내용은 커뮤니티 자원에서 [#12: 리액트 쿼리의 변형 정복하기](https://tanstack.com/query/latest/docs/react/community/tkdodos-blog#12-mastering-mutations-in-react-query)를 참고하세요.