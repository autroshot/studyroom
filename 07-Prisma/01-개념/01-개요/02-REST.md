# REST

REST API를 구축할 때 프리즈마 클라이언트를 경로 컨트롤러 내에서 사용하여 데이터베이스 쿼리를 보낼 수 있다.

![REST API](https://user-images.githubusercontent.com/95019875/184155544-ef4cedf6-c6c8-4dbe-b97e-070c99505c97.png)

## 지원되는 라이브러리

프리즈마 클라이언트는 데이터베이스에 쿼리를 보내는 책임만 가지고 있으므로 원하는 모든 HTTP 서버 라이브러리나 웹 프레임워크와 결합할 수 있다.

다음은 프리즈마와 함께 사용할 수 있는 라이브러리 및 프레임워크 목록이다.

- [Express](https://expressjs.com/)
- [koa](https://koajs.com/)
- [hapi](https://hapi.dev/)
- [Fastify](https://www.fastify.io/)
- [Sails](https://sailsjs.com/)
- [AdonisJs](https://adonisjs.com/)
- [NestJS](https://nestjs.com/)
- [Next.js](https://nextjs.org/)
- [Foal TS](https://foalts.org/)
- [Polka](https://github.com/lukeed/polka)
- [Micro](https://github.com/zeit/micro)
- [Feathers](https://feathersjs.com/)
- [Remix](https://remix.run/)

## REST API 서버 예시

다음과 같은 프리즈마 스키마가 있다고 가정하겠다.

```tsx
datasource db {
  provider = "sqlite"
  url      = "file:./dev.db"
}

generator client {
  provider = "prisma-client-js"
}

model Post {
  id        Int     @id @default(autoincrement())
  title     String
  content   String?
  published Boolean @default(false)
  author    User?   @relation(fields: [authorId], references: [id])
  authorId  Int?
}

model User {
  id    Int     @id @default(autoincrement())
  email String  @unique
  name  String?
  posts Post[]
}
```

이제 생성된 [프리즈마 클라이언트 API](https://www.prisma.io/docs/concepts/components/prisma-client)를 사용하여 HTTP 요청이 들어올 때 데이터베이스 작업을 수행하는 경로 컨트롤러(예: Express 사용)를 구현할 수 있다.

이 페이지에는 몇 가지 샘플 코드 스니펫이 있다. 이 코드 스니펫을 실행하고 싶다면 [REST API 예제](https://github.com/prisma/prisma-examples/tree/latest/typescript/rest-express)를 사용할 수 있다 .

#### GET

```js
app.get('/feed', async (req, res) => {
  const posts = await prisma.post.findMany({
    where: { published: true },
    include: { author: true },
  })
  res.json(posts)
})
```

이 경우 `feed` 엔드포인트는 `author` 객체를 포함하는 `Post` 객체인 중첩된 JSON 응답을 반환한다.

다음은 샘플 응답이다.

```json
[
  {
    "id": "21",
    "title": "Hello World",
    "content": "null",
    "published": "true",
    "authorId": 42,
    "author": {
      "id": "42",
      "name": "Alice",
      "email": "alice@prisma.io"
    }
  }
]
```

#### POST

```js
app.post(`/post`, async (req, res) => {
  const { title, content, authorEmail } = req.body
  const result = await prisma.post.create({
    data: {
      title,
      content,
      published: false,
      author: { connect: { email: authorEmail } },
    },
  })
  res.json(result)
})
```

#### PUT

```js
app.put('/publish/:id', async (req, res) => {
  const { id } = req.params
  const post = await prisma.post.update({
    where: { id: Number(id) },
    data: { published: true },
  })
  res.json(post)
})
```

#### DELETE

```js
app.delete(`/post/:id`, async (req, res) => {
  const { id } = req.params
  const post = await prisma.post.delete({
    where: {
      id: Number(id),
    },
  })
  res.json(post)
})
```

