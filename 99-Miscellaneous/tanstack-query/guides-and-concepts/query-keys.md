# 질의 키

본질적으로 탠스택 쿼리는 질의 키를 기반으로 질의 캐싱을 관리합니다. 질의 키는 최상위 수준의 배열이어야 합니다. 질의 키는 단일 문자열이 포함된 배열처럼 단순하거나 많은 문자열 및 중첩된 객체의 배열처럼 복잡할 수 있습니다. 직렬화 가능하고 **질의 데이터에 고유한** 것이라면 질의 키로 사용할 수 있습니다!

## 간단한 질의 키

키의 가장 간단한 형태는 상수 값이 있는 배열입니다. 이 형식은 다음에 유용합니다.

- 일반적인 목록 또는 색인 자원
- 비계층적 자원

```tsx
// 할 일 목록
useQuery({ queryKey: ['todos'], ... })

// 그 외의 모든 것!
useQuery({ queryKey: ['something', 'special'], ... })
```

## 변수가 있는 배열 키

질의가 데이터를 고유하게 묘사하기 위해 추가 정보가 필요하다면, 문자열과 복수의 직렬화 가능한 복수의 객체를 가지는 배열로 이를 묘사할 수 있습니다. 이 형식은 다음에 유용합니다.

- 계층적 또는 중첩된 자원
   - 항목을 고유하게 식별하기 위해 ID, 색인 또는 기타 원시값을 전달하는 것이 일반적입니다.
- 추가 매개변수가 있는 쿼리
   - 추가 옵션의 객체를 전달하는 것이 일반적입니다.

```tsx
// 개별 할 일
useQuery({ queryKey: ['todo', 5], ... })

// '미리보기' 형식의 개별 할 일
useQuery({ queryKey: ['todo', 5, { preview: true }], ...})

// '완료'된 할 일 목록
useQuery({ queryKey: ['todos', { type: 'done' }], ... })
```

## 질의 키는 결정론적으로 해시됩니다!

즉, 객체의 키 순서에 관계없이 다음 질의는 모두 동일한 것으로 간주됩니다.

```tsx
useQuery({ queryKey: ['todos', { status, page }], ... })
useQuery({ queryKey: ['todos', { page, status }], ...})
useQuery({ queryKey: ['todos', { page, status, other: undefined }], ... })
```

그러나 다음 질의 키는 동일하지 않습니다. 배열 항목의 순서가 중요합니다!

```tsx
useQuery({ queryKey: ['todos', status, page], ... })
useQuery({ queryKey: ['todos', page, status], ...})
useQuery({ queryKey: ['todos', undefined, page, status], ...})
```

## 질의 함수가 변수에 의존한다면 질의 키에 변수를 포함하세요.

질의 키는 가져오는 데이터를 고유하게 묘사하므로 질의 함수에서 사용하는 변수를 포함해야 합니다.

예시:

```tsx
function Todos({ todoId }) {
  const result = useQuery({
    queryKey: ['todos', todoId],
    queryFn: () => fetchTodoById(todoId),
  })
}
```

## 더 읽을거리

더 큰 애플리케이션에서 질의 키를 구성하는 방법에 대한 팁은 [효과적인 리액트 질의 키](https://tanstack.com/query/latest/docs/react/community/tkdodos-blog#8-effective-react-query-keys)를 살펴보고 커뮤니티 자원에서 [질의 키 팩토리 패키지](https://tanstack.com/query/latest/docs/react/community/lukemorales-query-key-factory)를 확인하세요.