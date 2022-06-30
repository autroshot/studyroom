# MPA, SPA, SSR, CSR

## MPA

MPA(Multi Page Application)는 여러 페이지로 구성된 웹 애플리케이션이다.

요청이 있을 때마다 서버로부터 새로운 HTML을 받아 페이지 전체를 새로 렌더링한다.

전통적인 웹 페이지 구성 방식이다.

## SPA

SPA(Single Page Application)는 하나의 페이지로 구성된 웹 애플리케이션이다.

최초에 한 번 페이지 전체를 로드한 후에는 요청이 있을 때마다 일부만 AJAX를 통해 교체한다.

React, Vue, 앵귤러 등으로 SPA를 구현할 수 있다.

## SSR

SSR(Server Side Rendering)는 서버로부터 완전하게 만들어진 HTML을 받아와 페이지 전체를 렌더링하는 방식이다.

장점:

1. 빠른 초기 로딩
2. SEO(검색 엔진 최적화)에 유리

단점:

1. TTV(Time To View)와 TTI(Time To Interact)의 시간 간격
2. 화면 깜빡임
3. 서버 부하가 큼

## CSR

CSR(Client Side Rendering)는 사용자의 요청에 따라 필요한 부분만 응답 받아 렌더링하는 방식이다.

장점:

1. 초기 로딩 이후에는 속도가 빠름
2. 서버 부하가 적음
3. 사용자 친화적

단점:

1. 느린 초기 로딩
2. SEO에 불리