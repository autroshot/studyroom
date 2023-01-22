# 개요

리액트 쿼리는 종종 리액트를 위한 누락된 데이터 가져오기 라이브러리로 설명됩니다. 보다 기술적인 용어로 설명하면, 리액트 애플리케이션에서 **서버 상태 가져오기, 캐싱, 동기화, 갱신**을 쉽게 만듭니다.

## 동기

기본적으로 리액트 앱에는 컴포넌트에서 데이터를 가져오거나 갱신하는 독단적인 방법이 **없으므로**, 개발자는 결국 데이터를 가져오는 고유한 방법을 구축하게 됩니다. 이는 일반적으로 리액트 훅을 사용하여 컴포넌트 기반 상태와 부작용을 결합하거나, 보다 범용적인 상태 관리 라이브러리를 사용하여 앱 전체에서 비동기 데이터를 저장하고 제공하는 것을 의미합니다.

기존의 상태 관리 라이브러리 대부분은 클라이언트 상태를 다루는 데는 적합하지만, **비동기 또는 서버 상태를 다루는 데는 그다지 적합하지 않습니다**. **서버 상태는 완전히 다르기** 때문입니다. 초심자를 위해 간단히 설명하면, 서버 상태는 다음과 같습니다.

- 내가 제어하거나 소유하지 않는 위치에 원격으로 유지됨
- 가져오기 및 갱신에 비동기 API가 필요함
- 공유 소유권을 암시하며 내가 모르게 다른 사람이 변경할 수 있음
- 주의하지 않으면 잠재적으로 앱에서 '구식'이 될 수 있음

앱에서 서버 상태의 특성을 파악하면 **더 많은 문제가 발생할 것입니다**. 예를 들면 다음과 같습니다.

- 캐싱... (프로그래밍에서 아마도 가장 어려운 일)
- 동일한 데이터에 대한 복수의 요청을 단일 요청으로 중복 제거
- 백그라운드에서 '오래된' 데이터 업데이트
- 데이터가 '오래된' 시점 알기
- 데이터 갱신을 최대한 신속하게 반영
- 페이지 매김 및 느린 로딩 데이터와 같은 성능 최적화
- 서버 상태의 메모리와 쓰레기 수집 관리
- 구조적 공유로 질의 결과 메모이징

이 목록에 압도당하지 않는다면, 그것은 당신이 이미 모든 서버 상태 문제를 해결했으며 상을 받을 자격이 있다는 것을 의미합니다. 하지만 만약 여러분이 대다수의 사람들과 같다면, 여러분은 아직 이러한 도전들의 전부 또는 대부분을 해결하지 못했거나 단지 표면을 긁고 있을 뿐입니다!

리액트 쿼리는 서버 상태를 관리하기 위한 최고의 라이브러리 중 하나입니다. **즉시 사용 가능하고, 설정이 전혀 필요 없으며**, 앱이 성장함에 따라 원하는 대로 **커스텀이 가능**합니다.

리액트 쿼리를 사용하면 서버 상태의 까다로운 문제와 장애물을 극복할 수 있습니다. 그리고 앱 데이터가 당신을 제어하기 전에 당신이 앱 데이터를 제어할 수 있습니다.

보다 기술적인 측면에서 리액트 쿼리는 다음과 같습니다.

- 앱에서 복잡하고 잘못 이해된 코드의 **많은** 줄을 제거하고, 몇 줄의 리액트 쿼리 논리로 대체할 수 있습니다.
- 앱의 유지 관리가 쉬워집니다. 그리고 새로운 서버 상태 데이터 소스를 연결할 필요 없이 새 기능을 쉽게 구축할 수 있습니다.
- 앱이 그 어느 때보다 빨리지고 응답성이 향상되어 최종 사용자에게 직접적인 영향을 미칩니다.
- 잠재적으로는 대역폭을 절약하고 메모리 성능을 높이는 데 도움이 됩니다.

## 설명은 됐고 코드를 보여주세요!

아래 예시는 가장 기본적이고 간단한 형태의 리액트 쿼리입니다. 리액트 쿼리 깃허브 프로젝트 자체에 대한 깃허브 통계를 가져옵니다.

[코드샌드박스에서 열기](https://codesandbox.io/s/github/tannerlinsley/react-query/tree/main/examples/react/simple)

```tsx
import {
  QueryClient,
  QueryClientProvider,
  useQuery,
} from '@tanstack/react-query'

const queryClient = new QueryClient()

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Example />
    </QueryClientProvider>
  )
}

function Example() {
  const { isLoading, error, data } = useQuery({
    queryKey: ['repoData'],
    queryFn: () =>
      fetch('https://api.github.com/repos/tannerlinsley/react-query').then(
        (res) => res.json(),
      ),
  })

  if (isLoading) return 'Loading...'

  if (error) return 'An error has occurred: ' + error.message

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.description}</p>
      <strong>👀 {data.subscribers_count}</strong>{' '}
      <strong>✨ {data.stargazers_count}</strong>{' '}
      <strong>🍴 {data.forks_count}</strong>
    </div>
  )
}
```
