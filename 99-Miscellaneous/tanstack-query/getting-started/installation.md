# 설치

[NPM](https://npmjs.com)을 사용하거나, [unpkg.com](https://unpkg.com)을 통해 오래된 `<script>`를 사용하여 리액트 쿼리를 설치할 수 있습니다.

### NPM

```bash
$ npm i @tanstack/react-query
# 또는
$ pnpm add @tanstack/react-query
# 또는
$ yarn add @tanstack/react-query
```

리액트 쿼리는 리액트 v16.8+와 호환되며 리액트DOM 및 리액트 네이티브와 함께 작동합니다.

> 다운로드하기 전에 더 알아보고 싶나요? [simple](https://tanstack.com/query/v4/docs/react/examples/react/simple)이나 [basic](https://tanstack.com/query/v4/docs/react/examples/react/basic) 예시를 살펴보세요!

### CDN

모듈 번들러나 패키지 관리자를 사용하지 않는 경우, [unpkg.com](https://unpkg.com) CDN에서 호스팅되는 글로벌(UMD) 빌드도 있습니다. HTML 파일 하단에 다음의 `<script>` 태그를 추가하기만 하면 됩니다.

```html
<script src="https://unpkg.com/@tanstack/react-query@4/build/umd/index.production.js"></script>
```

이제 `window.ReactQuery` 객체 및 해당 내보내기에 접근할 수 있습니다.

> 이 설치와 사용법은 페이지에 [리액트 CDN 스크립트 번들](https://reactjs.org/docs/cdn-links.html)을 필요로 합니다.

### 요구 사항

리액트 쿼리는 최신 브라우저에 최적화되어 있습니다. 다음 브라우저와 호환됩니다.

```
Chrome >= 73
Firefox >= 78
Edge >= 79
Safari >= 12.0
iOS >= 12.0
opera >= 53
```

> 환경에 따라 폴리필을 추가해야 할 수도 있습니다. 오래된 브라우저를 지원하려면 `node_modules`에서 직접 라이브러리를 트랜스파일해야 합니다.