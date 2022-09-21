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

## `process.cwd()`를 사용해 파일 읽기

파일은 `getStaticProps`의 파일 시스템에서 직접 읽을 수 있습니다.

파일을 읽으려면 파일의 전체 경로를 가져와야 합니다.

넥스트는 코드를 별도의 디렉터리로 컴파일하므로 `__dirname`을 사용할 수 없습니다. 여기에서 반환되는 경로는 실제 페이지 디렉터리와 다릅니다.

넥스트가 실행되는 디렉터리를 제공하는 `process.cwd()`를 대신 사용하면 됩니다.

```jsx
import { promises as fs } from 'fs'
import path from 'path'

// posts는 빌드 타임에 getStaticProps()에 의해 채워집니다.
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>
          <h3>{post.filename}</h3>
          <p>{post.content}</p>
        </li>
      ))}
    </ul>
  )
}

// 이 함수는 서버 측에서 빌드 타임에 호출됩니다.
// 클라이언트 측에서는 호출되지 않으므로
// 직접 데이터베이스 쿼리를 수행할 수도 있습니다.
export async function getStaticProps() {
  const postsDirectory = path.join(process.cwd(), 'posts')
  const filenames = await fs.readdir(postsDirectory)

  const posts = filenames.map(async (filename) => {
    const filePath = path.join(postsDirectory, filename)
    const fileContents = await fs.readFile(filePath, 'utf8')

    // 보통은 여기서 콘텐츠를 파싱하거나 변환합니다.
    // 예를 들어 마크다운을 HTML로 변환할 수 있습니다.

    return {
      filename,
      content: fileContents,
    }
  })
  // { props: { posts } }를 반환합니다.
  // Blog 컴포넌트는 빌드 타임에 프롭으로 posts를 받습니다.
  return {
    props: {
      posts: await Promise.all(posts),
    },
  }
}

export default Blog
```

