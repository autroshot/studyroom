# 문제 해결

# Next.js로 프리즈마 클라이언트를 인스턴스화하기 위한 모범 사례

### 문제

많은 사용자가 Next.js로 개발하는 동안 이 경고를 접했다.

```
warn(prisma-client) There are already 10 instances of Prisma Client actively running.
```

개발에서 `next dev` 명령은 실행 중에 Node.js 캐시를 정리한다. 그러면 데이터베이스에 대한 연결을 생성하는 핫 리로딩으로 인해 매번 새 `PrismaClient` 인스턴스가 초기화된다. 이렇게 하면 각 `PrismaClient` 인스턴스가 자체 연결 풀을 보유하므로 데이터베이스 연결이 빠르게 소진될 수 있다.

## 해결책

해결책은 단일 인스턴스 `PrismaClient`를 인스턴스화하고 `global` 객체에 저장하는 것이다. 그런 다음 [`global`](https://nodejs.org/api/globals.html#globals_global) 객체에 `PrismaClient`가 없는 경우에만 `PrismaClient`를 인스턴스화한다. 이미 있는 경우에는 동일한 인스턴스를 다시 사용하여 추가 `PrismaClient` 인스턴스를 만드는 것을 방지한다.

```js
// ./db

import { PrismaClient } from '@prisma/client'

declare global {
  // 전역 var 선언을 허용한다.
  // eslint-disable-next-line no-var
  var prisma: PrismaClient | undefined
}

export const prisma =
  global.prisma ||
  new PrismaClient({
    log: ['query'],
  })

if (process.env.NODE_ENV !== 'production') global.prisma = prisma
```

이 파일을 만든 후 다음과 같이 Next.js `pages` 아무 곳에서나 `PrismaClient` 인스턴스를 가져올 수 있다.

```js
// pages/index.tsx

import { prisma } from './db'

export const getServerSideProps = async ({ req }) => {
  const token = req.headers.AUTHORIZATION
  const userId = await getUserId(token)
  const posts = await prisma.post.findMany({
    where: {
      author: { id: userId },
    },
  })
  return { props: { posts } }
}
```

