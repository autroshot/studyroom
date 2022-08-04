# 내장 CSS 지원

Next.js를 사용하면 자바스크립트 파일에서 CSS 파일을 가져올 수 있다. 이것은 Next.js가 자바스크립트를 넘어 [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)의 개념을 확장하기 때문에 가능하다.

## 전역 스타일시트 추가하기

앱에 스타일시트를 추가하려면 `pages/_app.js` 내부에서 CSS 파일을 가져온다.

예를 들어 다음과 같이 `styles.css`라는 스타일시트가 있다고 가정해 보겠다.

```css
body {
  font-family: 'SF Pro Text', 'SF Pro Icons', 'Helvetica Neue', 'Helvetica',
    'Arial', sans-serif;
  padding: 20px 20px 60px;
  max-width: 680px;
  margin: 0 auto;
}
```

[`pages/_app.js` 파일](https://nextjs.org/docs/advanced-features/custom-app)이 없으면 새로 만든다. 그리고 `styles.css`를 `import`한다.

```jsx
import '../styles.css'

// 이 default export는 새 `pages/_app.js` 파일에서 필요하다.
export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

이러한 스타일( `styles.css`)은 앱의 모든 페이지와 컴포넌트에 적용된다. 스타일시트의 전역적 특성, 그리고 충돌을 피하기 위해 `pages/_app.js` 내부에서만 가져오는 것이 가능하다.

개발 단계에서 스타일시트를 이런 식으로 표현하면 스타일을 편집할 때 즉시 다시 로드할 수 있다. 즉, 앱의 상태를 최신으로 유지할 수 있다.

생산에서 모든 CSS 파일은 자동으로 하나의 축소된 `.css` 파일로 연결된다.

### node_modules에서 스타일 가져오기

Next.js 9.5.4 버전부터 `node_modules`에서 CSS 파일을 가져오는 것이 앱의 모든 위치에서 허용된다.

`bootstrap`이나 `nprogress`와 같은 전역 스타일시트은 `pages/_app.js` 내부에서 파일을 가져와야 한다.

예시:

```jsx
// pages/_app.js
import 'bootstrap/dist/css/bootstrap.css'

export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

타사 컴포넌트에 필요한 CSS를 가져오는 경우에는 컴포넌트에서 가져오기를 수행할 수 있다.

예시:

```jsx
// components/ExampleDialog.js
import { useState } from 'react'
import { Dialog } from '@reach/dialog'
import VisuallyHidden from '@reach/visually-hidden'
import '@reach/dialog/styles.css'

function ExampleDialog(props) {
  const [showDialog, setShowDialog] = useState(false)
  const open = () => setShowDialog(true)
  const close = () => setShowDialog(false)

  return (
    <div>
      <button onClick={open}>Open Dialog</button>
      <Dialog isOpen={showDialog} onDismiss={close}>
        <button className="close-button" onClick={close}>
          <VisuallyHidden>Close</VisuallyHidden>
          <span aria-hidden>×</span>
        </button>
        <p>Hello there. I am a dialog</p>
      </Dialog>
    </div>
  )
}
```

## 컴포넌트 수준 CSS 추가하기

Next.js는 `[name].module.css` 파일명 컨벤션을 사용하는 [CSS 모듈](https://github.com/css-modules/css-modules)을 지원한다.

CSS 모듈은 고유한 클래스 이름을 자동으로 생성하여 로컬에서 CSS 범위를 지정한다. 이를 통해 충돌에 대한 걱정 없이 다른 파일에서 동일한 CSS 클래스 이름을 사용할 수 있다.

이 동작은 CSS 모듈을 컴포넌트 수준 CSS를 포함하는 이상적인 방법으로 만든다. CSS 모듈 파일은 앱의 어느 곳에서나 가져올 수 있다.

예를 들어 `components/` 폴더의 `Button`에 재사용 가능한 컴포넌트가 있다고 가정해 보겠다.

먼저 `components/Button.module.css`을 다음과 같이 작성한다.

```css
/*
.error {}가 다른 `.css`나 `.module.css` 파일과 충돌하는 것을 걱정할 필요가 없다.
*/
.error {
  color: white;
  background-color: red;
}
```

이제 `components/Button.js`을 만들고, 위의 CSS 파일을 가져와서 사용한다.

```jsx
import styles from './Button.module.css'

export function Button() {
  return (
    <button
      type="button"
      // 가져온 `styles` 객체의 프로퍼티로 "error" 클래스에 접근한다.
      className={styles.error}
    >
      Destroy
    </button>
  )
}
```

CSS 모듈은 선택적 기능으로 `.module.css` 확장자가 있는 파일에만 적용된다. 일반적인 `<link>` 스타일시트와 전역 CSS 파일은 계속 지원된다.

생산에서 모든 CSS 모듈 파일은 자동으로 축소 및 코드 분할된 `.css` 파일로 연결된다. 이러한 `.css` 파일은 앱의 핫 실행 경로를 나타내며 앱이 그려질 수 있는 최소한의 CSS가 로드되게 한다.

## Sass 지원

Next.js를 사용하면 `.scss`과 `.sass` 확장자로 Sass를 가져올 수 있다. CSS 모듈과 `.module.scss` 또는 `.module.sass` 확장자를 통해 컴포넌트 수준 Sass를 사용할 수 있다.

Next.js의 내장 Sass 지원을 사용하기 전에 [`sass`](https://github.com/sass/sass)를 설치해야 한다.

```bash
npm install --save-dev sass
```

Sass 지원에는 앞에서 설명한 내장 CSS 지원과 동일한 이점과 제한이 적용된다.

> **참고**
>
> Sass는 각각 고유한 확장자를 가진 [두 가지 다른 문법](https://sass-lang.com/documentation/syntax)을 지원한다. `.scss` 확장자를 사용하려면 [SCSS 문법](https://sass-lang.com/documentation/syntax#scss)을 사용해야 하는 반면 `.sass` 확장자를 사용하려면 [들여쓰기 문법("Sass")](https://sass-lang.com/documentation/syntax#the-indented-syntax)을 사용해야 한다.
>
> 어떤 것을 선택해야 할지 잘 모르겠다면 들여쓰기 문법("Sass")이 필요 없고 CSS의 상위 집합인 `.scss` 확장으로 시작하면 된다.

### Sass 옵션 사용자 정의하기

Sass 컴파일러를 설정하려면 `next.config.js`의 `sassOptions`을 사용하면 된다.

예를 들어 `includePaths`를 추가하려면 다음과 같이 작성한다.

```js
const path = require('path')

module.exports = {
  sassOptions: {
    includePaths: [path.join(__dirname, 'styles')],
  },
}
```

### Sass 변수

Next.js는 CSS 모듈 파일에서 내보낸 Sass 변수를 지원한다.

다음은 내보낸 `primaryColor` Sass 변수를 사용하는 예시이다.

```scss
/* variables.module.scss */
$primary-color: #64ff00;

:export {
  primaryColor: $primary-color;
}
```

```tsx
// pages/_app.js
import variables from '../styles/variables.module.scss'

export default function MyApp({ Component, pageProps }) {
  return (
    <Layout color={variables.primaryColor}>
      <Component {...pageProps} />
    </Layout>
  )
}
```

## 자주 묻는 질문

### 자바스크립트를 비활성화해도 CSS가 작동할까

그렇다. 자바스크립트를 비활성화해도 CSS가 생산 빌드( `next start`)에서 여전히 로드된다. 하지만 개발 중에 [빠른 새로고침](https://nextjs.org/blog/next-9-4#fast-refresh) 기능을 사용하려면 자바스크립트 활성화가 필요하다.

