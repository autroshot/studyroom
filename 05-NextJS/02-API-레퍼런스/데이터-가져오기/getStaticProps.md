# getStaticProps

`getStaticProps` 함수를 내보내면 함수에서 반환된 `props`를 사용하여 빌드 타임에 페이지를 미리 렌더링한다.

```jsx
export async function getStaticProps(context) {
  return {
    props: {}, // 페이지 컴포넌트에 props로 전달된다.
  }
}
```

`getStaticProps`에서 사용하기 위해 최상위 범위의 모듈을 가져올 수 있다. 사용된 가져오기는 클라이언트 측에서 번들로 제공되지 않는다 . 즉, 데이터베이스에서 데이터를 가져오는 것을 포함하여 `getStaticProps`에서 직접 서버 측 코드를 작성할 수 있다.

## 컨텍스트 매개변수

`context` 매개변수는 다음의 키가 포함된 객체이다.

- `params`에는 [동적 경로](../../01-문서/03-라우팅/02-동적-경로.md)를 사용하는 페이지의 경로 매개변수가 포함된다. 예를 들어 페이지 이름이 `[id].js`인 경우 `params`는 `{ id: ... }`이다. 이것은 `getStaticPaths`와 함께 사용해야 한다.
- 페이지가 [미리보기 모드](https://nextjs.org/docs/advanced-features/preview-mode)이면 `preview`가 `true`이며 이외에는 `undefined`이다.
- `previewData`에는 `setPreviewData`에서 설정한 미리보기 데이터가 포함된다.
- `locale`에는 활성 로케일이 포함된다. (활성화된 경우)
- `locales`에는 지원되는 모든 로케일이 포함된다. (활성화된 경우)
- `defaultLocale`에는 설정된 기본 로케일이 포함된다. (활성화된 경우)