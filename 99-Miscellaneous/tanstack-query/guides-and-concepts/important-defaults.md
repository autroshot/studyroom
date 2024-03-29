# 중요 기본값

기본적으로 리액트 쿼리는 **공격적이지만 온건한** 기본값으로 설정됩니다. **경우에 따라 이 기본값은 새로운 사용자를 방심하게 만들거나 사용자가 모르다면 학습이나 디버깅을 어렵게 만들 수 있습니다.** 계속해서 리액트 쿼리를 배우고 사용하려면 다음 사항을 기억할 필요가 있습니다.

- `useQuery` 또는 `useInfiniteQuery`를 이용한 질의 인스턴스는 기본적으로 **캐시된 데이터를 오래된 것으로 간주합니다**.

> 이 동작을 변경하려면 `staleTime` 옵션을 사용하여 전역 및 질의별로 질의를 설정할 수 있습니다. 더 긴 `staleTime`을 지정하면 질의가 데이터를 다시 가져오는 빈도가 줄어듭니다.

- 오래된 질의는 다음의 경우에 백그라운드에서 자동으로 다시 가져옵니다.
   - 질의 마운트의 새 인스턴스
   - 창이 다시 초점을 얻음
   - 네트워크가 다시 연결됨
   - 질의가 선택적으로 다시 가져오기 간격으로 설정됨

예상하지 못한 다시 가져오기를 본다면, 방금 창이 초점을 얻고 리액트 쿼리가 `refetchOnWindowFocus`를 수행하기 때문일 수 있습니다. 개발 중에는 특히 브라우저 개발자 도구와 앱 사이에서 초점을 바꾸면 가져오기가 더 자주 발생할 수 있습니다.

> 이 기능을 변경하려면 `refetchOnMount`, `refetchOnWindowFocus`, `refetchOnReconnect`, `refetchInterval`과 같은 옵션을 사용할 수 있습니다.

- `useQuery`, `useInfiniteQuery` 또는 질의 관찰자의 활성 인스턴스가 더 이상 없는 질의 결과는 '비활성' 레이블이 지정되고, 나중에 다시 사용될 경우를 대비하여 캐시에 남습니다.
- 기본적으로 '비활성' 쿼리는 **5분** 후에 쓰레기 수집됩니다.

> 이를 변경하려면 질의의 기본 `cacheTime`을 `1000 * 60 * 5`밀리초 이외의 값으로 바꿀 수 있습니다.

- 실패한 질의는 오류를 잡아 UI에 표시하기 전에 **지수 백오프 지연으로 조용히 3번 재시도**됩니다.

> 이를 변경하려면 질의에 대한 `retry`의 기본값인 `3`과 `retryDelay`의 기본값인 기본 지수 백오프 함수를 다른 옵션으로 변경할 수 있습니다.

- 기본적으로 질의 결과는 **데이터가 실제로 변경되었는지 감지하기 위해 구조적으로 공유**되고, 그렇지 않은 경우 **데이터 참조가 변경되지 않은 상태로 유지**되어 `useMemo` 및 `useCallback`과 관련된 값 안정화에 도움이 됩니다. 이 개념이 낯설더라도 걱정하지 마세요! 99.9%의 경우 이 기능을 비활성화할 필요가 없으며, 비용 없이 앱의 성능을 높일 수 있습니다.

> 구조적 공유는 JSON 호환 값에서만 작동하며 다른 모든 값 타입은 항상 변경된 것으로 간주됩니다. 예를 들어 응답이 커서 성능 문제가 발생한다면 `config.structuralSharing` 플래그를 사용하여 이 기능을 비활성화할 수 있습니다. 질의 응답에서 JSON이 호환되지 않는 값을 처리하고 데이터가 변경되었는지 감지하려면, `config.isDataEqual`을 사용하여 데이터 비교 함수를 정의할 수 있습니다.

## 추가 자료

기본값에 대한 자세한 설명은 커뮤니티 리소스의 다음 문서를 참고하세요.

- [실용적인 리액트 쿼리](https://react-query-v3.tanstack.com/community/tkdodos-blog#1-practical-react-query)
- [상태 관리자로서의 리액트 쿼리](https://react-query-v3.tanstack.com/community/tkdodos-blog#10-react-query-as-a-state-manager)