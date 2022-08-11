# Next.js의 작동 방식

## 개발과 프로덕션 환경

환경은 코드가 실행되는 컨텍스트이다.

개발하는 동안에는 로컬 시스템에서 애플리케이션을 빌드하고 실행한다. 프로덕션(production)으로 가는 것은 애플리케이션을 배포하고 사용자가 사용할 수 있도록 준비하는 과정이다.

Next.js는 애플리케이션의 개발과 프로덕션 단계를 위한 기능을 제공한다.

- 개발 단계에서 Next.js는 개발자와 애플리케이션 구축 경험을 최적화한다. 타입스크립트 및 [ESLint ](https://nextjs.org/docs/basic-features/typescript)[통합](https://nextjs.org/docs/basic-features/eslint), [빠른 새로고침](https://nextjs.org/docs/basic-features/fast-refresh) 등과 같은 기능을 통해 개발자 경험을 향상시킨다.
- 프로덕션 단계에서 Next.js는 최종 사용자와 애플리케이션 사용 경험을 최적화한다. 코드를 변환하여 성능과 접근성을 높이는 것을 목표로 한다.

환경마다 고려 사항과 목표가 다르기 때문에 앱을 개발에서 프로덕션으로 옮기려면 해야 할 일이 많다. 예를 들어 애플리케이션 코드는 컴파일, 번들링, 축소, 코드 분할이 필요하다.

## 컴파일

개발자는 JSX, 타입스크립트, 최신 버전의 자바스크립트와 같이 개발자에게 더 친숙한 언어로 코드를 작성한다. 이러한 언어는 개발자의 효율성과 자신감을 향상시키지만 브라우저가 이를 이해하려면 자바스크립트로 컴파일해야 한다.

**컴파일(Compiling)은 한 언어로 된 코드를 다른 언어 또는 해당 언어의 다른 버전으로 출력하는 과정을 의미한다.**

<img width="680" alt="compiling" src="https://user-images.githubusercontent.com/95019875/176668809-a5e66756-fa97-4e2e-aea9-0d0dd7e1388b.png">

Next.js에서 컴파일은 코드를 편집할 때 개발 단계에서 발생한다. 그리고 프로덕션을 위해 앱을 준비하는 빌드 단계에서도 발생한다.

## 축소

개발자는 사람의 가독성에 최적화된 코드를 작성한다. 이 코드에는 주석, 공백, 들여쓰기, 여러 줄과 같이 코드를 실행하는 데 필요하지 않은 추가 정보가 포함된다.

**축소(Minifying)는 코드의 기능을 변경하지 않고 불필요한 코드 서식과 주석을 제거하는 과정이다.** 목표는 파일 크기를 줄여 응용 프로그램의 성능을 향상시키는 것이다.

<img width="680" alt="minifying" src="https://user-images.githubusercontent.com/95019875/176668832-8fe6c9c5-6a96-4344-85d0-2103afb14a68.png">

Next.js에서 자바스크립트와 CSS 파일은 프로덕션을 위해 자동으로 축소된다.

## 번들링

개발자는 앱을 더 큰 부분의 앱을 구축하는 데 사용할 수 있는 모듈, 컴포넌트, 기능으로 나눈다. 이러한 내부 모듈과 외부의 서드 파티 패키지를 내보내고 가져오면 파일 종속성을 가지는 복잡한 웹이 만들어진다.

**번들링(Bundling)은 웹 종속성을 해결하고 파일(또는 모듈)을 브라우저에 최적화된 번들로 병합(패키징)하는 과정이다.** 이를 통해 사용자가 웹 페이지를 방문할 때 요청하는 파일의 수를 줄일 수 있다.

<img width="680" alt="bundling" src="https://user-images.githubusercontent.com/95019875/176668851-ae743115-36a2-4faa-9f1b-9402b5312fb0.png">

## 코드 분할

개발자는 일반적으로 애플리케이션을 다른 URL에서 액세스할 수 있는 여러 페이지로 분할한다. 이러한 각 페이지는 앱에 대한 고유한 **진입점(entry point)**이 된다.

**코드 분할(Code Splitting)은 앱 번들을 각 진입점에 필요한 더 작은 청크(chunk)로 분할하는 과정이다.** 목표는 해당 페이지를 실행하는 데 필요한 코드만 로드하여 애플리케이션의 초기 로드 시간을 개선하는 것이다.

<img width="680" alt="code-splitting" src="https://user-images.githubusercontent.com/95019875/176668867-a7f70760-a4c1-4992-bed7-047d5546a485.png">

Next.js는 코드 분할을 기본적으로 지원한다. 디렉토리 내의 각 파일 `pages/`는 빌드 단계에서 자동으로 자체 자바스크립트 번들로 코드 분할된다.

그리고 다음과 같은 특징을 지닌다.

- 페이지 간에 공유되는 모든 코드는 추가 탐색 시 동일한 코드를 다시 다운로드하지 않도록 다른 번들로 분할된다.
- 초기 페이지 로드 후에 Next.js는 사용자가 탐색할 가능성이 있는 다른 페이지의 [코드를 미리 로드](https://nextjs.org/docs/api-reference/next/link)할 수 있다.
- [동적 가져오기](https://nextjs.org/docs/advanced-features/dynamic-import)는 처음에 로드되는 코드를 수동으로 분할하는 또 다른 방법이다.

## 빌드 타임과 런타임

**빌드 타임**(빌드 단계)은 **프로덕션용 애플리케이션 코드를 준비하는 일련의 단계**이다.

애플리케이션을 빌드할 때 Next.js는 코드를 프로덕션에 최적화된 파일로 변환하여 서버에 배포하고 사용자가 사용할 수 있게 된다. 이러한 파일에는 다음이 포함된다.

- 정적으로 생성된 페이지용 HTML 파일
- 서버에서 페이지를 렌더링하기 위한 자바스크립트 코드
- 클라이언트에서 페이지를 대화형으로 만들기 위한 자바스크립트 코드
- CSS 파일

**런타임**(요청 타임)은 애플리케이션이 빌드되고 배포된 후, **사용자의 요청에 대한 응답**으로 애플리케이션이 실행되는 때를 의미한다.

## 클라이언트와 서버

웹 앱의 맥락에서 **클라이언트**는 앱 코드에 대한 요청을 서버에 보내는 사용자 장치의 브라우저를 의미한다. 클라이언트는 서버에서 받은 응답을 사용자가 상호 작용할 수 있는 인터페이스로 바꾼다.

**서버**는 앱 코드를 저장하고, 클라이언트로부터 요청을 수신하고, 일부 계산을 수행하고, 적절한 응답을 다시 보내는 데이터 센터의 컴퓨터를 의미한다.

<img width="680" alt="client-server" src="https://user-images.githubusercontent.com/95019875/176668910-bcc9c83d-e212-466f-a96f-4276ed7c653a.png">

## 렌더링

리액트에서 작성한 코드를 UI의 HTML 표현으로 변환하는 데 반드시 필요한 과정이 있다. 바로 **렌더링(rendering)**이다.

렌더링은 서버나 클라이언트에서 수행될 수 있다. 빌드에서 미리 렌더링하거나, 런타임의 모든 요청에서 렌더링이 발생할 수 있다.

Next.js에서는 **서버 측 렌더링(Server-Side Rendering, SSR)**, **정적 사이트 생성(Static Site Generation, SSG)**, **클라이언트 측 렌더링(Client-Side Rendering, CSR)**의 세 가지 유형의 렌더링 방법을 사용할 수 있다.

### 사전 렌더링

SSR과 SSG은 결과가 클라이언트로 전송되기 전에 외부 데이터를 가져오고 React 컴포넌트를 HTML로 변환하기 때문에 **사전 렌더링(Pre-Rendering)**이라고도 부른다.

### CSR vs 사전 렌더링

표준 React 앱에서 브라우저는 서버로부터 UI를 만드는 자바스크립트의 지시와 빈 HTML 껍데기를 받는다. 초기 렌더링 작업이 사용자의 장치에서 발생하기 때문에 이것을 CSR이라고 한다.

<img width="680" alt="client-side-rendering" src="https://user-images.githubusercontent.com/95019875/176668932-1474f358-20f7-4a10-a6ef-b75002a47f98.png">

> **참고**
>
> React의 `useEffect()`나 [useSWR](https://swr.vercel.app/)과 같은 데이터 fetching Hook으로 데이터를 가져오도록 선택하여 Next.js 앱의 특정 컴포넌트에 대해 CSR을 사용할 수 있다.

이와 반대로 Next.js는 기본적으로 모든 페이지를 **미리 렌더링한다.** 사전 렌더링은 HTML이 사용자 장치에서 자바스크립트로 모두 수행되는 대신 서버에서 미리 생성된다는 것을 의미한다.

완전히 클라이언트 측에서 렌더링된 앱의 경우에는 렌더링 작업이 완료되는 동안 사용자에게 빈 페이지가 표시된다.

사용자가 만들어진 HTML을 볼 수 있는 사전 렌더링된 앱은 다음과 같다.

<img width="680" alt="pre-rendering" src="https://user-images.githubusercontent.com/95019875/176668940-ba6d5aac-474e-491f-a003-d2614c0e515a.png">

### SSR

SSR을 사용하면 각 요청에 대한 페이지의 HTML이 서버에서 생성된다. 페이지를 상호작용형으로 만들기 위해 생성된 HTML, JSON 데이터, 자바스크립트 지시가 클라이언트로 전송된다.

클라이언트에서 HTML은 빠른 비상호작용형 페이지를 표시하는 데 사용되는 반면 리액트는 JSON 데이터와 자바스크립트 지시를 사용하여 컴포넌트를 상호작용형으로 만든다(예: 버튼에 이벤트 핸들러 연결). 이 과정을 **수화(hydration)**라고 부른다.

Next.js에서는 [getServerSideProps](https://nextjs.org/docs/basic-features/data-fetching/get-server-side-props)를 사용하여 SSR로 페이지를 만들 수 있다.

### SSG

SSG를 사용하면 HTML이 서버에서 생성되지만 SSR과 달리 런타임에는 서버가 없다. 대신 앱이 배포될 때 빌드에서 콘텐츠가 한 번 생성되고 HTML이 [CDN](https://nextjs.org/learn/foundations/how-nextjs-works/cdns-and-edge)에 저장되서 각 요청에 재사용된다.

Next.js에서는 [getStaticProps](https://nextjs.org/docs/basic-features/data-fetching/get-static-props)를 사용하여 SSG로 페이지를 만들 수 있다.

> **참고**
>
> [점진적인 정적 재생성](https://nextjs.org/docs/basic-features/data-fetching/incremental-static-regeneration)을 사용하여 사이트를 구축한 후에 정적 페이지를 생성하거나 업데이트할 수 있다. 즉 데이터가 변경되더라도 전체 사이트를 다시 만들 필요가 없다.

Next.js의 장점은 SSG, SSR, SCR에 관계없이 페이지별로 유스 케이스에 가장 적합한 렌더링 방법을 선택할 수 있다는 것이다. 특정 유스 케이스에 적합한 렌더링 방법에 대한 자세한 내용은 [데이터 가져오기](https://nextjs.org/docs/basic-features/data-fetching/overview)에서 확인할 수 있다.

## 네트워크

앱이 네트워크에 배포된 후에 앱 코드가 어디에 저장되고 실행되는지 알 필요가 있다.

Next.js 앱의 경우에는 앱 코드를 **오리진 서버**, **CDN(Content Delivery Network)**, **에지(Edge)**에 배포할 수 있다 .

### 오리진 서버

서버는 앱 코드의 원본 버전을 저장하고 실행하는 메인 컴퓨터를 의미한다.

CDN 서버나 에지 서버와 같이 앱 코드가 배포될 수 있는 다른 장소와 구별하기 위해 **오리진(origin)**이라는 용어를 사용한다.

오리진 서버는 요청을 받으면 응답을 보내기 전에 몇 가지 계산을 수행한다. 이 계산 작업의 결과는 CDN으로 이동될 수 있다.

### CDN

CDN은 전 세계 여러 위치에 정적 콘텐츠(예: HTML 및 이미지 파일)를 저장하고 클라이언트와 원본 서버 사이에 배치된다. 새 요청이 들어오면 사용자와 가장 가까운 CDN 위치에서 캐시된 결과로 응답할 수 있다.

<img width="680" alt="cdn" src="https://user-images.githubusercontent.com/95019875/176668968-8273d6b8-893a-4a9c-a18e-9f6836cd2f6d.png">

이렇게 하면 각 요청에 대해 계산을 수행할 필요가 없기 때문에 오리진의 부하가 줄어든다. 또한 응답이 지리적으로 더 가까운 위치에서 제공되기 때문에 사용자가 더 빠르게 작업할 수 있다.

Next.js에서는 사전 렌더링을 미리 수행할 수 있다. 그리고 CDN은 작업의 정적 결과를 저장하는 데 적합하므로 콘텐츠 전달이 빨라진다.

### 에지

에지(edge)는 사용자에게 가장 가까운 네트워크의 프린지(fringe)에 대한 일반화된 개념이다. CDN은 네트워크의 가장자리(에지)에 정적 콘텐츠를 저장하기 때문에 에지의 일부로 볼 수 있다.

CDN과 유사하게 에지 서버는 전 세계 여러 위치에 배포된다. 그러나 정적 콘텐츠를 저장하는 CDN과 달리 일부 에지 서버는 코드를 실행할 수 있다.

즉 사용자에게 더 가까운 에지에서 캐싱과 코드 실행을 모두 수행할 수 있다.

에지에서 코드를 실행하면 전통적으로 클라이언트 측 또는 서버 측에서 수행되었던 작업 중 일부를 에지로 이동할 수 있다([예제 참조](https://vercel.com/features/edge-functions)). 이렇게 하면 클라이언트로 전송되는 코드의 양이 줄어들고 사용자 요청의 일부가 원본 서버로 완전히 돌아갈 필요가 없으므로 앱의 성능이 향상되어 대기 시간이 줄어든다.

Next.js에서는 [미들웨어](https://nextjs.org/docs/middleware)를 사용하여 에지에서 코드를 실행할 수 있다. 곧 [리액트 서버 컴포넌트](https://nextjs.org/docs/advanced-features/react-18/overview#react-server-components-alpha)로도 가능해질 예정이다.

