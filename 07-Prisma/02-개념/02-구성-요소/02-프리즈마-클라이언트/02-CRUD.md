# CRUD

이 페이지에서는 생성된 프리즈마 클라이언트 API로 CRUD 작업을 수행하는 방법을 설명합니다. CRUD는 다음을 나타내는 약어입니다.

- Create
- Read
- Update
- Delete

## 예시 스키마

모든 예시는 다음 스키마를 기반으로 합니다.

```tsx
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model ExtendedProfile {
  id        Int    @id @default(autoincrement())
  biography String
  user      User   @relation(fields: [userId], references: [id])
  userId    Int    @unique
}

model User {
  id           Int              @id @default(autoincrement())
  name         String?
  email        String           @unique
  profileViews Int              @default(0)
  role         Role             @default(USER)
  coinflips    Boolean[]
  posts        Post[]
  profile      ExtendedProfile?
}

model Post {
  id         Int        @id @default(autoincrement())
  title      String
  published  Boolean    @default(true)
  author     User       @relation(fields: [authorId], references: [id])
  authorId   Int
  comments   Json?
  views      Int        @default(0)
  likes      Int        @default(0)
  categories Category[]
}

model Category {
  id    Int    @id @default(autoincrement())
  name  String @unique
  posts Post[]
}

enum Role {
  USER
  ADMIN
}
```

관계형 데이터베이스의 경우에는 `db push` 명령을 사용하여 예시 스키마를 자체 데이터베이스에 푸시합니다.

```shell
npx prisma db push
```

## Create

### 단일 레코드 생성하기

다음 쿼리는 두 개의 필드가 있는 단일 사용자를 생성합니다.

```js
const user = await prisma.user.create({
  data: {
    email: 'elsa@prisma.io',
    name: 'Elsa Prisma',
  },
})
```

결과:

```js
{
  id: 22,
  name: 'Elsa Prisma',
  email: 'elsa@prisma.io',
  profileViews: 0,
  role: 'USER',
  coinflips: []
}
```

사용자 `id`는 자동 생성되며 스키마에 따라 필수 필드가 결정됩니다.

#### 생성된 타입을 사용하여 단일 레코드 생성하기

다음 예시에서는 동일한 결과를 생성하지만 `create` 쿼리의 컨텍스트 외부에 `user`라는 이름의 `UserCreateInput` 변수를 생성합니다. 간단한 검사(`create` 쿼리에 게시물을 포함해야 하는지 여부)를 완료한 후 `user` 변수가 쿼리에 전달됩니다.

```tsx
import { PrismaClient, Prisma } from '@prisma/client'

const prisma = new PrismaClient()

async function main() {
  let includePosts: boolean = false
  let user: Prisma.UserCreateInput

  // 쿼리에 게시물이 포함되야 하는지를 확인합니다.
  if (includePosts) {
    user = {
      email: 'elsa@prisma.io',
      name: 'Elsa Prisma',
      posts: {
        create: {
          title: 'Include this post!',
        },
      },
    }
  } else {
    user = {
      email: 'elsa@prisma.io',
      name: 'Elsa Prisma',
    }
  }

  // 쿼리에 user 객체를 전달합니다.
  const createUser = await prisma.user.create({ data: user })
}

main()
  .then(async () => {
    await prisma.$disconnect()
  })
  .catch(async (e) => {
    console.error(e)
    await prisma.$disconnect()
    process.exit(1)
  })
```

자세한 내용은 [생성된 타입](https://www.prisma.io/docs/concepts/components/prisma-client/advanced-type-safety)을 참고합니다.

### 여러 레코드 생성하기

프리즈마 클라이언트는 2.20.0 이상에서 GA 기능으로 대량 삽입을 지원합니다.

다음 `createMany` 쿼리는 여러 사용자를 생성하고 중복은 건너뜁니다. (`email`이 고유해야 함)

```js
const createMany = await prisma.user.createMany({
  data: [
    { name: 'Bob', email: 'bob@prisma.io' },
    { name: 'Bobo', email: 'bob@prisma.io' }, // Duplicate unique key!
    { name: 'Yewande', email: 'yewande@prisma.io' },
    { name: 'Angelique', email: 'angelique@prisma.io' },
  ],
  skipDuplicates: true, // Skip 'Bobo'
})
```

결과:

```js
{
  count: 3
}
```

`createMany`는 복수의 값이 있는 단일 `INSERT INTO`문을 사용합니다. 이는 일반적으로 행별로 `INSERT`하는 것보다 더 효율적입니다.

```sql
BEGIN
INSERT INTO "public"."User" ("id","name","email","profileViews","role","coinflips","testing","city","country") VALUES (DEFAULT,$1,$2,$3,$4,DEFAULT,DEFAULT,DEFAULT,$5), (DEFAULT,$6,$7,$8,$9,DEFAULT,DEFAULT,DEFAULT,$10), (DEFAULT,$11,$12,$13,$14,DEFAULT,DEFAULT,DEFAULT,$15), (DEFAULT,$16,$17,$18,$19,DEFAULT,DEFAULT,DEFAULT,$20) ON CONFLICT DO NOTHING
COMMIT
SELECT "public"."User"."country", "public"."User"."city", "public"."User"."email", SUM("public"."User"."profileViews"), COUNT(*) FROM "public"."User" WHERE 1=1 GROUP BY "public"."User"."country", "public"."User"."city", "public"."User"."email" HAVING AVG("public"."User"."profileViews") >= $1 ORDER BY "public"."User"."country" ASC OFFSET $2
```

> **참고**
>
> `$transaction` 내부에 복수의 `create`문이 있으면 복수의 `INSERT`문이 생성됩니다.

### 레코드를 생성하고 관련 레코드를 연결하거나 생성하기

레코드와 하나 이상의 관련 레코드를 동시에 생성하는 방법은 [관계 쿼리 > 중첩 쓰기](./04-관계-쿼리.md/#중첩-쓰기)를 참고하세요.

## Read

### ID 또는 고유 식별자로 레코드 가져오기

다음 `findUnique` 쿼리는 고유 식별자 또는 ID로 단일 레코드를 반환합니다.

```js
// 고유 식별자 사용
const user = await prisma.user.findUnique({
  where: {
    email: 'elsa@prisma.io',
  },
})

// ID 사용
const user = await prisma.user.findUnique({
  where: {
    id: 99,
  },
})
```

### 복합 ID 또는 복합 고유 식별자로 레코드 가져오기

다음 예시는 `@@id` 또는 `@@unique`로 정의된 복합 ID 또는 고유 식별자로 레코드를 검색하는 방법을 보여줍니다.

다음 Prisma 모델은 복합 ID를 정의합니다.

```js
model TimePeriod {
  year    Int
  quarter Int
  total   Decimal

  @@id([year, quarter])
}
```

이 복합 ID로 기간을 검색하려면 다음 `fieldName1_fieldName2` 패턴을 따르는 생성된 `year_quarter` 필드를 사용합니다.

```js
const timePeriod = await prisma.timePeriod.findUnique({
  where: {
    year_quarter: {
      quarter: 4,
      year: 2020,
    },
  },
})
```

다음 Prisma 모델은 커스텀 이름(`timePeriodId`)으로 복합 고유 식별자를 정의합니다.

```js
model TimePeriod {
  year    Int
  quarter Int
  total   Decimal

  @@unique(fields: [year, quarter], name: "timePeriodId")
}
```

이 고유 식별자로 기간을 검색하려면 커스텀 `timePeriodId` 필드를 사용합니다.

```js
const timePeriod = await prisma.timePeriod.findUnique({
  where: {
    timePeriodId: {
      quarter: 4,
      year: 2020,
    },
  },
})
```

### 모든 레코드 가져오기

다음 `findMany` 쿼리는 모든 `User` 레코드를 반환합니다.

```js
const users = await prisma.user.findMany()
```

결과에 [페이지를 매길](./06-페이지네이션.md) 수도 있습니다.

### 특정 기준과 일치하는 첫 번째 레코드 가져오기

다음 `findFirst` 쿼리는 좋아요가 100개 이상인 게시물이 하나 이상 있는 가장 최근에 생성된 사용자를 반환합니다.

1. 내림차순 ID로 사용자 정렬 (가장 큰 것부터) - 가장 큰 ID가 가장 최근 것
2. 좋아요가 100개 이상인 게시물이 하나 이상 있는 첫 번째 사용자를 내림차순으로 반환

```js
const findUser = await prisma.user.findFirst({
  where: {
    posts: {
      some: {
        likes: {
          gt: 100
        }
      }
    }
  },
  orderBy: {
    id: "desc"
  }
})
```

### 필터링된 레코드 목록 가져오기

프리즈마 클라이언트는 레코드 필드 및 관련 레코드 필드에 대한 [필터링](https://www.prisma.io/docs/concepts/components/prisma-client/filtering-and-sorting)을 지원합니다.

#### 단일 필드 값으로 필터링하기

다음 쿼리는 `"prisma.io"`으로 끝나는 이메일이 있는 모든 `User` 레코드를 반환합니다.

```js
const users = await prisma.user.findMany({
  where: {
    email: {
      endsWith: "prisma.io"
    }
  },
}
```

#### 여러 필드 값으로 필터링하기

다음 쿼리는 [연산자](https://www.prisma.io/docs/reference/api-reference/prisma-client-reference#filter-conditions-and-operators) 조합을 사용합니다. 이름이 `E`로 시작하는 사용자 또는 프로필 뷰가 1개 이상 있는 관리자를 반환합니다.

```js
const users = await prisma.user.findMany({
  where: {
    OR: [
      {
        name: {
          startsWith: 'E',
        },
      },
      {
        AND: {
          profileViews: {
            gt: 0,
          },
          role: {
            equals: 'ADMIN',
          },
        },
      },
    ],
  },
})
```

#### 관련 레코드 필드 값으로 필터링하기

다음 쿼리는 `prisma.io`로 끝나는 이메일이 있고 게시되지 않은 게시물이 하나 이상(`some`) 있는 사용자를 반환합니다.

```js
const users = await prisma.user.findMany({
  where: {
    email: {
      endsWith: "prisma.io"
    },
    posts: {
      some: {
        published: false
      }
    }
  },
}
```

관련 필드 값 필터링에 대한 자세한 내용은 [관계 쿼리](https://www.prisma.io/docs/concepts/components/prisma-client/relation-queries)를 참고하세요.

### 필드의 하위 집합 선택하기

다음 `findUnique` 쿼리는 `select`를 사용하여 특정 `User` 레코드의 `email`과 `name` 필드를 반환합니다.

```js
const user = await prisma.user.findUnique({
  where: {
    email: 'emma@prisma.io',
  },
  select: {
    email: true,
    name: true,
  },
})
```

결과:

```js
{ email: 'emma@prisma.io', name: "Emma" }
```

관계를 포함하는 자세한 내용은 다음을 참고합니다.

- [필드 선택하기](https://www.prisma.io/docs/concepts/components/prisma-client/select-fields)
- [관계 쿼리](https://www.prisma.io/docs/concepts/components/prisma-client/relation-queries)

#### 관련 레코드 필드의 하위 집합 선택하기

다음 쿼리는 중첩 `select`를 사용하여 다음을 반환합니다.

- 사용자의 `email`
- 각 게시물의 `likes` 필드

```js
const user = await prisma.user.findUnique({
  where: {
    email: 'emma@prisma.io',
  },
  select: {
    email: true,
    posts: {
      select: {
        likes: true,
      },
    },
  },
})
```

결과:

```js
{ email: 'emma@prisma.io', posts: [ { likes: 0 }, { likes: 0 } ] }
```

### 관련 레코드 포함하기

다음 쿼리는 모든 `ADMIN` 사용자를 반환하고 결과에 각 사용자의 게시물을 포함합니다.

```js
const users = await prisma.user.findMany({
  where: {
    role: 'ADMIN',
  },
  include: {
    posts: true,
  },
})
```

결과:

```js
{
    "id": 38,
    "name": "Maria",
    "email": "maria@prisma.io",
    "profileViews": 20,
    "role": "ADMIN",
    "coinflips": [
        true,
        false,
        false
    ],
    "posts": []
},
{
    "id": 39,
    "name": "Oni",
    "email": "oni2@prisma.io",
    "profileViews": 20,
    "role": "ADMIN",
    "coinflips": [
        true,
        false,
        false
    ],
    "posts": [
        {
        "id": 25,
        "authorId": 39,
        "title": "My awesome post",
        "published": true,
        "comments": null,
        "views": 0,
        "likes": 0
        }
    ]
}
```

## Update

### 단일 레코드 업데이트하기

다음 쿼리는 `update`를 사용해 `email`로 단일 `User` 레코드를 찾고 업데이트합니다.

```js
const updateUser = await prisma.user.update({
  where: {
    email: 'viola@prisma.io',
  },
  data: {
    name: 'Viola the Magnificent',
  },
})
```

결과:

```js
{
   "id": 43,
   "name": "Viola the Magnificent",
   "email": "viola@prisma.io",
   "profileViews": 0,
   "role": "USER",
   "coinflips": [],
}
```

### 여러 레코드 업데이트하기

다음 쿼리는 `updateMany`를 사용해 `prisma.io`를 포함하는 모든 `User` 레코드를 업데이트합니다.

```js
const updateUsers = await prisma.user.updateMany({
  where: {
    email: {
      contains: 'prisma.io',
    },
  },
  data: {
    role: 'ADMIN',
  },
})
```

결과:

```js
{
   "count": 19
}
```

### 레코드 업데이트하거나 생성하기

다음 쿼리는 `upsert`를 사용해 특정 이메일 주소로 `User` 레코드를 업데이트하거나 해당 `User` 레코드가 존재하지 않는 경우에는 생성합니다.

```js
const upsertUser = await prisma.user.upsert({
  where: {
    email: 'viola@prisma.io',
  },
  update: {
    name: 'Viola the Magnificent',
  },
  create: {
    email: 'viola@prisma.io',
    name: 'Viola the Magnificent',
  },
})
```

결과:

```js
{
   "id": 43,
   "name": "Viola the Magnificent",
   "email": "viola@prisma.io",
   "profileViews": 0,
   "role": "ADMIN",
   "coinflips": [],
}
```

### 숫자 필드 업데이트하기

[원자 번호 연산](https://www.prisma.io/docs/reference/api-reference/prisma-client-reference#atomic-number-operations)을 사용해 현재 값을 기반으로 숫자 필드를 업데이트합니다. (예: 증가 또는 곱하기)

다음 쿼리는 `views`와 `likes` 필드를 `1` 증가시킵니다.

```js
const updatePosts = await prisma.post.updateMany({
  data: {
    views: {
      increment: 1,
    },
    likes: {
      increment: 1,
    },
  },
})
```

## Delete

### 단일 레코드 삭제하기

다음 쿼리는`delete`를 사용해 단일 `User` 레코드를 삭제합니다.

```js
const deleteUser = await prisma.user.delete({
  where: {
    email: 'bert@prisma.io',
  },
})
```

모든 `Post`에는 작성자가 필요하므로 하나 이상의 게시물이 있는 사용자를 삭제하려고 하면 오류가 발생합니다. ([캐스케이딩 삭제](https://www.prisma.io/docs/concepts/components/prisma-client/crud#cascading-deletes-deleting-related-records) 참고)

### 여러 레코드 삭제하기

다음 쿼리는 `deleteMany`를 사용해 `email`에 `prisma.io`가 포함된 모든 `User` 레코드를 삭제합니다.

```js
const deleteUsers = await prisma.user.deleteMany({
  where: {
    email: {
      contains: 'prisma.io',
    },
  },
})
```

모든 `Post`에는 작성자가 필요하므로 하나 이상의 게시물이 있는 사용자를 삭제하려고 하면 오류가 발생합니다. ([캐스케이딩 삭제](https://www.prisma.io/docs/concepts/components/prisma-client/crud#cascading-deletes-deleting-related-records) 참고)

### 모든 레코드 삭제하기

다음 쿼리는 `deleteMany`를 사용해 모든 `User` 레코드를 삭제합니다.

```js
const deleteUsers = await prisma.user.deleteMany({})
```

사용자에게 관련 레코드(예: 게시물)가 있으면 이 쿼리가 실패합니다. 이 경우 [먼저 관련 레코드를 삭제](https://www.prisma.io/docs/concepts/components/prisma-client/crud#cascading-deletes-deleting-related-records)해야 합니다.

### 캐스케이딩 삭제하기(관련 레코드 삭제하기)

> **참고**
>
> 2.26.0 이상 에서는 **미리보기 기능** [참조 작업](https://www.prisma.io/docs/concepts/components/prisma-schema/relations/referential-actions)을 사용하여 캐스케이딩 삭제를 수행할 수 있습니다.

다음 쿼리는 `delete`를 사용해 단일 `User` 레코드를 삭제합니다.

```js
const deleteUser = await prisma.user.delete({
  where: {
    email: 'bert@prisma.io',
  },
})
```

그러나 예시 스키마에는 `Post`와 `User` 사이에 필수 관계가 있습니다. 즉, 게시물이 있는 사용자는 삭제할 수 없습니다.

```
The change you are trying to make would violate the required relation 'PostToUser' between the `Post` and `User` models.
```

이 오류를 해결하려면 다음을 수행할 수 있습니다.

- 관계를 선택으로 만듭니다.

    ```js
    model Post {
      id       Int   @id @default(autoincrement())
      author   User? @relation(fields: [authorId], references: [id])
      authorId Int?
    }
    ```

- 사용자를 삭제하기 전에 게시물 작성자를 다른 사용자로 변경합니다.
- 트랜잭션에서 두 개의 개별 쿼리로 사용자와 모든 게시물을 삭제합니다. (모든 쿼리는 성공해야 함)

```js
const deletePosts = prisma.post.deleteMany({
  where: {
    authorId: 7,
  },
})

const deleteUser = prisma.user.delete({
  where: {
    id: 7,
  },
})

const transaction = await prisma.$transaction([deletePosts, deleteUser])
```

### 모든 테이블에서 모든 레코드 삭제하기

때로는 모든 테이블에서 모든 데이터를 제거하지만 실제 테이블은 유지하고 싶을 때가 있습니다. 이것은 개발 환경과 테스트 중에 특히 유용할 수 있습니다.

다음은 프리즈마 클라이언트와 프리즈마 마이그레이트를 사용하여 모든 테이블에서 모든 레코드를 삭제하는 방법을 보여줍니다.

#### `deleteMany`로 모든 데이터 삭제하기

테이블이 삭제되어야 하는 순서를 알면 `deleteMany` 함수를 사용할 수 있습니다. 이것은 `$transaction`에서 동기적으로 실행되며 모든 유형의 데이터베이스와 함께 사용할 수 있습니다.

```js
const deletePosts = prisma.post.deleteMany()
const deleteProfile = prisma.profile.deleteMany()
const deleteUsers = prisma.user.deleteMany()

// 이 트랜잭션은 동기적으로 실행되므로 deleteUsers가 마지막에 실행되야 합니다.
await prisma.$transaction([deleteProfile, deletePosts, deleteUsers])
```

✅ **장점**

- 스키마의 구조를 미리 알고 있을 때 잘 작동합니다.
- 각 테이블 데이터를 동기적으로 삭제합니다.

❌ **단점**

- 관계형 데이터베이스로 작업할 때 이 함수는 관계형 제약 조건에 관계없이 테이블을 조회하고 `TRUNCATE`하는 보다 일반적인 솔루션만큼 확장되지 않습니다. 이 확장 문제는 몽고DB 커넥터를 사용할 때 적용되지 않습니다.

> **참고**
>
> `$transaction`는 각 모델 테이블에 대해 캐스케이딩 삭제를 수행하므로 순서대로 호출해야 합니다.

#### 원시 SQL로 모든 데이터 삭제하기 / TRUNCATE

원시 SQL를 사용하는 것이 익숙하다면 `$executeRawUnsafe`를 이용해 테이블에 `TRUNCATE`을 수행할 수 있습니다.

다음 예시에서 MySQL 데이터베이스를 사용합니다. 이 경우 `TRUNCATE`를 실행하기 전에 제약 조건을 제거해야 합니다. 전체 프로세스는 `$transaction`로 실행됩니다.

```js
const transactions: PrismaPromise<any>[] = []
transactions.push(prisma.$executeRaw`SET FOREIGN_KEY_CHECKS = 0;`)

const tablenames = await prisma.$queryRaw<
  Array<{ TABLE_NAME: string }>
>`SELECT TABLE_NAME from information_schema.TABLES WHERE TABLE_SCHEMA = 'tests';`

for (const { TABLE_NAME } of tablenames) {
  if (TABLE_NAME !== '_prisma_migrations') {
    try {
      transactions.push(prisma.$executeRawUnsafe(`TRUNCATE ${TABLE_NAME};`))
    } catch (error) {
      console.log({ error })
    }
  }
}

transactions.push(prisma.$executeRaw`SET FOREIGN_KEY_CHECKS = 1;`)

try {
  await prisma.$transaction(transactions)
} catch (error) {
  console.log({ error })
}
```

✅ **장점**

- 확장 가능
- 매우 빠름

❌ **단점**

- 작업을 취소할 수 없음
- 예약된 SQL 키워드를 테이블 이름으로 사용하면 원시 쿼리를 실행하려고 할 때 문제가 발생할 수 있음

#### 프리즈마 마이그레이트로 모든 기록 삭제하기

프리즈마 마이그레이트를 사용하는 경우 `migrate reset`을 사용할 수 있습니다.

1. 데이터베이스 삭제
2. 새 데이터베이스 만들기
3. 마이그레이션 적용
4. 데이터로 데이터베이스 시드

## 고급 쿼리 예제

### 깊이 중첩된 레코드 트리 만들기

- 단일 `User`
- 두 개의 새로운 관련 `Post` 레코드
- 게시물별로 `Category` 연결하거나 생성하기

```js
const u = await prisma.user.create({
  include: {
    posts: {
      include: {
        categories: true,
      },
    },
  },
  data: {
    email: 'emma@prisma.io',
    posts: {
      create: [
        {
          title: 'My first post',
          categories: {
            connectOrCreate: [
              {
                create: { name: 'Introductions' },
                where: {
                  name: 'Introductions',
                },
              },
              {
                create: { name: 'Social' },
                where: {
                  name: 'Social',
                },
              },
            ],
          },
        },
        {
          title: 'How to make cookies',
          categories: {
            connectOrCreate: [
              {
                create: { name: 'Social' },
                where: {
                  name: 'Social',
                },
              },
              {
                create: { name: 'Cooking' },
                where: {
                  name: 'Cooking',
                },
              },
            ],
          },
        },
      ],
    },
  },
})
```

