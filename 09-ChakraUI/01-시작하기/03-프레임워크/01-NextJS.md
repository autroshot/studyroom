# 넥스트로 시작하기

### 설치

넥스트 프로젝트에서 다음을 실행하여 차크라 UI를 설치합니다.

```bash
npm i @chakra-ui/react @emotion/react@^11 @emotion/styled@^11 framer-motion@^6
```

```bash
yarn add @chakra-ui/react @emotion/react@^11 @emotion/styled@^11 framer-motion@^6
```

### 제공자 설정

차크라 UI를 설치한 후, 앱의 루트에 `ChakraProvider`를 설정해야 합니다.

`pages/_app.js` 또는 `pages/_app.tsx`(존재하지 않으면 생성함)으로 가서 `Component`를 `ChakraProvider`로 감쌉니다.

```jsx live=false
// pages/_app.js
import { ChakraProvider } from '@chakra-ui/react'

function MyApp({ Component, pageProps }) {
  return (
    <ChakraProvider>
      <Component {...pageProps} />
    </ChakraProvider>
  )
}

export default MyApp
```

### 테마 커스터마이즈하기

디자인 요구 사항에 맞게 기본 테마 객체를 커스터마이즈하려면 `theme`를 확장해야 합니다.

차크라 UI는 기본 테마를 커스텀과 깊게 병합할 수 있는 `extendTheme` 함수를 제공합니다.

```jsx live=false
// pages/_app.js
import { ChakraProvider } from '@chakra-ui/react'

// 1. extendTheme 함수를 가져옵니다.
import { extendTheme } from '@chakra-ui/react'

// 2. 커스텀 색상, 글꼴 등을 포함하도록 테마를 확장합니다.
const colors = {
  brand: {
    900: '#1a365d',
    800: '#153e75',
    700: '#2a69ac',
  },
}

const theme = extendTheme({ colors })

// 3. theme 프랍을 ChakraProvider에 건네줍니다.
function MyApp({ Component, pageProps }) {
  return (
    <ChakraProvider theme={theme}>
      <Component {...pageProps} />
    </ChakraProvider>
  )
}

export default MyApp;
```

> 컴포넌트를 커스터마이즈하거나 고유한 디자인 시스템을 구축하고 싶다면
> `theme-tools` 패키지에 유용한 유틸리티가 포함되어 있습니다.

### 색상 모드 스크립트

로컬 저장소 동기화가 올바르게 작동하도록, 색상 모드 스크립트는 `body` 태그 내부의
콘텐츠 앞에 추가해야 합니다.

> 색상 모드를 처음 설정할 때 `ColorModeScript`를 테마에 설정으로 추가하고 이를 참조하는 것을 권장합니다.

> 사이트에서 `ColorModeScript`를 엄격한 `Content-Security-Policy`로 사용하려면, `<script>` 태그에 전달될 `nonce` 프랍을 사용할 수 있습니다.

```jsx live=false ln={14}
// pages/_document.js

import { ColorModeScript } from '@chakra-ui/react'
import NextDocument, { Html, Head, Main, NextScript } from 'next/document'
import theme from './theme'

export default class Document extends NextDocument {
  render() {
    return (
      <Html lang='en'>
        <Head />
        <body>
          {/* 👇 이곳에 스크립트가 들어갑니다. */}
          <ColorModeScript initialColorMode={theme.config.initialColorMode} />
          <Main />
          <NextScript />
        </body>
      </Html>
    )
  }
}
```

#### 타입스크립트에 대한 참고 사항 🚨

타입스크립트 프로젝트에 차크라 UI를 추가할 때 타입스크립트 버전은 최소 4.1.0이 필요합니다.

### ChakraProvider의 프랍

| Name | Type | Default | Description |
| ---------------- | ---------------- | --------------------- | --------------------------------------------------- |
| resetCSS | `boolean` | `true` | `<CSSReset />`을 자동으로 포함 |
| theme | `Theme` | `@chakra-ui/theme` | 선택적인 커스텀 테마 |
| colorModeManager | `StorageManager` | `localStorageManager` | 사용자 색상 모드의 기본 설정을 유지하는 관리자 |
| portalZIndex | `number` | `undefined` | `Portal`에 사용되는 일반적인 z-index |