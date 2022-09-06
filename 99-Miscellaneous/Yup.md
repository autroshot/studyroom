# 엽

엽은 런타임 값 파싱과 유효성 검사를 위한 스키마 빌더이다. 스키마를 정의하거나, 일치하도록 값을 변환하거나, 기존 값의 타입을 단언하거나, 둘 다 할 수 있다. 엽 스키마는 표현력이 매우 뛰어나서, 복잡하고 상호 의존적인 유효성 검사나 값 변환을 모델링할 수 있다.

핵심 기능:

- 단순하면서도 복잡한 데이터 모델을 모델링할 수 있는 간결하면서도 표현력이 풍부한 스키마 인터페이스
- 강력한 타입스크립트 지원 - 스키마에서 정적 타입을 유추하거나 스키마가 타입을 올바르게 구현하는지 확인
- 내장된 비동기 유효성 검사. 서버 측, 클라이언트 측 유효성 검사를 동등하게 모델링
- 확장 가능성 - 고유한 타입 안전 메서드 및 스키마 추가 가능
- 풍부한 오류 세부 정보와 간편한 디버깅

## 시작하기

스키마는 파싱 작업(변환)과 입력 값에 대한 단언(테스트)으로 구성된다. 입력 값의 유효성을 검사하여 파싱하고 설정된 단언문 집합을 실행한다. 메서드를 체이닝하여 스키마를 구축한다.

```typescript
import { object, string, number, date, InferType } from 'yup';

let userSchema = object({
  name: string().required(),
  age: number().required().positive().integer(),
  email: string().email(),
  website: string().url().nullable(),
  createdOn: date().default(() => new Date()),
});

// 파싱하고 단언문에 대한 유효성 검사를 실시한다.
const user = await userSchema.validate(await fetchUser());

type User = InferType<typeof userSchema>;
/* {
  name: string;
  age: number;
  email?: string | undefined
  website?: string | null | undefined
  createdOn: Date
}*/
```

스키마를 사용하여 입력 값을 올바른 타입으로 강제 변환하거나 **캐스팅(cast)**하고, 선택적으로 추가 단언문 없이 해당 값을 보다 구체적인 값으로 변환한다.

```typescript
// 값을 올바른 타입으로 강제로 변환하는 것을 시도한다.
const parsedUser = userSchema.cast({
  name: 'jimmy',
  age: '24',
  createdOn: '2014-09-23T19:25:25Z',
});
// ✅ { name: 'jimmy', age: 24, createdOn: Date }
```

입력 값이 이미 파싱된 상태라면 입력 값의 유효성을 **엄격하게** 확인하고 파싱 로직 실행 비용을 아낄 수 있다.

```typescript
// ❌ 유효성 검사 오류 "age is not a number"
const parsedUser = await userSchema.validate(
  {
    name: 'jimmy',
    age: '24',
  },
  { strict: true },
);
```

## 스키마 기본

스키마 정의는 입력을 원하는 모양과 타입으로 조작하는 **변환** 파싱, 파싱된 데이터에 대한 단언문을 만드는 **테스트**로 구성된다. 스키마는 또한 오류 메시지를 개선하거나, 스키마를 동적으로 사용하는 도구를 빌드하거나, 스키마를 다른 형식으로 직렬화하는 데 사용할 수 있는, 스키마 자체에 대한 세부 정보인 **메타데이터**를 저장한다.

유연성을 위해 특정 요구 사항에 맞게 파싱과 단언문을 별도로 실행하는 것도 가능하다.

### 파싱: 변환

각 내장 타입은 JSON과 같은 직렬화된 데이터를 파싱할 때 편리한 기본 타입 파싱을 구현한다. 또한 타입은 가능한 타입별 변환을 구현한다.

```typescript
const num = number().cast('1'); // 1

const obj = object({
  firstName: string().lowercase().trim(),
})
  .camelCase()
  .cast('{"first_name": "jAnE "}'); // { firstName: 'jane' }
```

커스텀 변환을 추가하는 것도 가능하다.

```typescript
const reversedString = string()
  .transform((currentValue) => currentValue.split('').reverse().join(''))
  .cast('dlrow olleh'); // "hello world"
```

변환은 이전 변환의 값이 다음 변환으로 파이프되는 **파이프라인**을 형성한다. 최종 값이 `undefined`이면 엽은 설정된 스키마 기본값을 적용한다.

> **주의**
>
> 변환 함수의 반환 값이 유효한 타입이라는 보장은 없다. 이전 변환이 실패했을 수 있다. 예를 들어 숫자 변환은 입력 값으로 `NaN`이나 숫자를 받을 수 있다.

### 유효성 검사: 테스트

엽은 입력 값에 대한 단언문, 즉 테스트를 강력하게 지원한다. 테스트는 입력이 어떤 기준을 준수한다고 단언한다. 테스트는 입력(또는 해당 타입)을 변경하지 않는다는 점에서 변환과 구별되며, 일반적으로 불가능하지는 않더라도 정적 타입으로 표현하기 어려운 검사를 위해 예약된다.

```typescript
string()
  .min(3, 'must be at least 3 characters long')
  .email('must be a valid email')
  .validate('no'); // ValidationError
```

변환과 마찬가지로 테스트를 즉석에서 커스터마이징할 수 있다.

```typescript
const jamesSchema = string().test(
  'is-james',
  (d) => `${d.path} is not James`,
  (value) => value == null || value === 'James',
);

jamesSchema.validateSync('James'); // "James"

jamesSchema.validateSync('Jane'); // ValidationError "this is not James"
```

> **주의**
>
> 변환과 달리 커스텀 테스트의 `value`는 올바른 타입(이 경우 선택적 문자열)이 보장된다. 이 값은 스키마에 따라 `undefined`나 `null`이 될 수 있으며, 변환이 존재 관련 단언을 하지 않는 한 존재하지 않는 값에 대해 `true`를 반환할 수 있다.

#### 오류 커스터마이징하기

가장 간단한 경우, 테스트 함수는 검사가 통과했는지 여부에 따라 `true`나 `false`를 반환한다. 테스트가 실패하면 엽은 해당 테스트에 대한 (기본) 메시지와 함께 [`ValidationError`](https://github.com/jquense/yup#validationerrorerrors-string--arraystring-value-any-path-string)를 던진다. `ValidationErrors`에는 테스트 이름, 호출된 인수 (존재하는 경우), 중첩 유효성 검사의 경우에는 실패한 필드의 경로 등 테스트에 대한 많은 메타데이터가 포함되어 있다.

오류 메시지는 스키마 실패 방식을 커스터마이징하기 위해 즉석에서 만들 수도 있다.

```typescript
const order = object({
  no: number().required().
  sku: string().test({
    name: 'is-sku',
    skipAbsent: true,
    test(value, ctx) {
      if (!value.startsWith('s-')) {
        return ctx.createError({ message: 'SKU missing correct prefix' })
      }
      if (!value.endsWith('-42a')) {
        return ctx.createError({ message: 'SKU missing correct suffix' })
      }
      if (value.length < 10) {
        return ctx.createError({ message: 'SKU is not the right length' })
      }
      return true
    }
  })
})

order.validate({ no: 1234, sku: 's-1a45-14a' })
```

### 합성 및 재사용

스키마는 변경할 수 없으며 각 메서드 호출은 새 스키마 개체를 반환한다. 다른 인스턴스 변경에 대한 걱정 없이 재사용하고 전달할 수 있다.

```typescript
const optionalString = string().optional();

const definedString = optionalString.defined();

const value = undefined;
optionalString.isValid(value); // true
definedString.isValid(value); // false
```

## 타입스크립트 통합

엽 스키마는 정적 타입스크립트 인터페이스를 생성한다. `InferType`을 사용해 해당 인터페이스를 추출할 수 있다.

```typescript
import * as yup from 'yup';

const personSchema = yup.object({
  firstName: yup.string().defined(),
  nickName: yup.string().default('').nullable(),
  sex: yup
    .mixed()
    .oneOf(['male', 'female', 'other'] as const)
    .defined(),
  email: yup.string().nullable().email(),
  birthDate: yup.date().nullable().min(new Date(1900, 0, 1)),
});

interface Person extends yup.InferType<typeof personSchema> {
  // 타입 대신 인터페이스를 사용하는 것이 일반적으로 더 나은 편집기 피드백을 제공한다.
}
```

### 스키마 기본값

캐스팅이 `undefined` 출력 값을 생성할 때 스키마의 기본값이 사용된다. 따라서 기본값을 설정하면 스키마의 출력 타입에 영향을 미치며 기본적으로 `defined()`로 표시한다.

```typescript
import { string } from 'yup';

const value: string = string().default('hi').validate(undefined);

// vs

const value: string | undefined = string().validate(undefined);
```

### 스키마가 기존 타입과 일치하는지 확인하기

어떤 경우에는 타입스크립트 타입이 이미 존재하며 스키마가 호환 가능한 타입을 생성하는지 확인하고 싶을 수 있다.

```typescript
import { object, number, string, ObjectSchema } from 'yup';

interface Person {
  name: string;
  age?: number;
  sex: 'male' | 'female' | 'other' | null;
}

// 스키마가 유효한 사용자를 생성하지 않으면 컴파일 타임에 타입 오류가 발생한다.
const schema: ObjectSchema<Person> = object({
  name: string().defined(),
  age: number().optional(),
  sex: string<'male' | 'female' | 'other'>().nullable().defined();
});

// ❌ 오류:
// "Type 'number | undefined' is not assignable to type 'string'."
const badSchema: ObjectSchema<Person> = object({
  name: number(),
});
```

### 새로운 메소드로 내장 스키마 확장하기

원한다면 타입스크립트의 인터페이스 병합 동작을 사용하여 스키마 타입을 확장할 수 있다. 타입 확장은 `globals.d.ts`와 같은 주변 타입 정의 파일에 있어야 한다. 앱 코드에서 엽 타입을 실제로 확장하는 것을 잊지 말아야 한다.

> **주의**
>
> 병합은 제네릭을 포함하여 타입 정의가 정확히 동일한 경우에만 작동한다. 각 타입에 대한 엽 소스 코드를 참조하여 올바르게 정의하고 있는지 확인해야 한다.

```typescript
// globals.d.ts
declare module 'yup' {
  interface StringSchema<TType, TContext, TDefault, TFlags> {
    append(appendStr: string): this;
  }
}

// app.ts
import { addMethod, string } from 'yup';

addMethod(string, 'append', function append(appendStr: string) {
  return this.transform((value) => `${value}${appendStr}`);
});

string().append('~~~~').cast('hi'); // 'hi~~~~'
```

### 타입스크립트 설정

형식 추론이 작동하려면 `strictNullChecks` 컴파일러 옵션이 활성화되어 있어야 한다.

또한 기능적으로 더 나은 타입을 위해 `strictFunctionTypes`을 `false`로 설정하는 것이 좋다. 이렇게 하면 전반적인 건전성이 감소하지만 타입스크립트는 이미 메서드와 생성자에 대한 이 검사를 비활성화한다. (TS 문서 참고)

> **참고**
>
> 이 기능을 개발하는 동안 DOM의 일부를 포함하여 본질적으로 안전하지 않은 클래스 계층 구조를 많이 발견했다. 이 때문에 이 설정은 메서드 문법이 아닌 함수 문법으로 작성된 함수에만 적용된다.

이득은 다양할 수 있지만, 이 검사가 실제 버그를 많이 방지하지는 못하면서 앱에서 번거로운 명시적 타입 캐스팅의 양을 증가시키는 것으로 보인다.

## 오류 메시지 커스터마이징

기본 오류 메시지는 유효성 검사 테스트에 제공된 메시지가 없을 때 커스터마이징할 수 있다. 커스텀 사전에 메시지가 누락된 경우 오류 메시지는 기본적으로 엽의 것으로 표시된다.

```typescript
import { setLocale } from 'yup';

setLocale({
  mixed: {
    default: 'Não é válido',
  },
  number: {
    min: 'Deve ser maior que ${min}',
  },
});

// 이제 커스텀 사전을 정의한 후에 엽 스키마를 사용한다.
let schema = yup.object().shape({
  name: yup.string(),
  age: yup.number().min(18),
});

try {
  await schema.validate({ name: 'jimmy', age: 11 });
} catch (err) {
  err.name; // => 'ValidationError'
  err.errors; // => ['Deve ser maior que 18']
}
```

### 현지화 및 i18n

엽은 다국어를 지원한다. `setLocale` 함수는 번역 키와 값으로 오류 객체를 생성하는 데 사용할 수 있는 함수를 받는다. 이것들은 원하는 i18n 라이브러리에 넣을 수 있다.

```typescript
import { setLocale } from 'yup';

setLocale({
  // 값이 없는 메시지에 대한 일정한 번역 키를 사용한다.
  mixed: {
    default: 'field_invalid',
  },
  // 함수를 사용하여 스키마에서 값을 포함한 오류 객체를 생성한다.
  number: {
    min: ({ min }) => ({ key: 'field_too_short', values: { min } }),
    max: ({ max }) => ({ key: 'field_too_big', values: { max } }),
  },
});

// ...

let schema = yup.object().shape({
  name: yup.string(),
  age: yup.number().min(18),
});

try {
  await schema.validate({ name: 'jimmy', age: 11 });
} catch (err) {
  messages = err.errors.map((err) => i18next.t(err.key));
}
```

## API

### yup

#### ref(path: string, options: { contextPrefix: string }): Ref

다른 형제나 형제 하위 필드에 대한 참조를 만든다. 참조는 유효성 검사나 캐스트 시간에 이행되고 지정된 경우 지원된다. `ref`를 사용하는 필드보다 `ref` 값이 먼저 이행되도록 적절한 순서로 평가된다. 순환 종속성에 주의해야 한다.

```typescript
import { ref, object, string } from 'yup';

let schema = object({
  baz: ref('foo.bar'),
  foo: object({
    bar: string(),
  }),
  x: ref('$x'),
});

schema.cast({ foo: { bar: 'boom' } }, { context: { x: 5 } });
// => { baz: 'boom',  x: 5, foo: { bar: 'boom' } }
```

### Schema

#### Schema.when(keys: string | string[], builder: object | (values: any[], schema) => Schema): Schema

형제 또는 형제 자식 필드를 기반으로 스키마를 조정한다. `is`에 값 또는 일치자 함수를, `then`에 `true`일 때의 스키마를, `otherwise`에 `false`일 때의 스키마를 가진 객체 리터럴을 인수로 받는다.

`is` 조건은 일치 연산자(`===`)를 사용한다. 동등 연산자(`==`)를 사용하려면 다음과 같은 함수를 건네준다.

```typescript
is: (value) => value == true
```

또한 프로퍼티 앞에 `$`를 붙여 입력 값 대신 `validate()` 또는 `cast`에서 전달된 `context`에 종속되는 프로퍼티를 지정할 수도 있다.

다음 예시에서는 복수의 `when` 조건을 추가한다.

```typescript
let schema = object({
  isBig: boolean(),
  count: number()
    .when('isBig', {
      is: true, // '(val) => val == true'와 동일
      then: (schema) => schema.min(5),
      otherwise: (schema) => schema.min(0),
    })
    .when('$other', ([other], schema) =>
      other === 4 ? schema.max(6) : schema,
    ),
});

await schema.validate(value, { context: { other: 4 } });
```

둘 이상의 종속 키를 지정할 수도 있다. 이 경우 각 값은 인수로 확산된다.

```typescript
let schema = object({
  isSpecial: boolean(),
  isBig: boolean(),
  count: number().when(['isBig', 'isSpecial'], {
    is: true, // '(isBig, isSpecial) => isBig && isSpecial'와 동일
    then: (schema) => schema.min(5),
    otherwise: (schema) => schema.min(0),
  }),
});

await schema.validate({
  isBig: true,
  isSpecial: true,
  count: 10,
});
```

또는 현재 스키마에 제공된 각 키에 대한 값 배열로 호출되는 스키마를 반환하는 함수를 제공할 수 있다.

```typescript
let schema = yup.object({
  isBig: yup.boolean(),
  count: yup.number().when('isBig', ([isBig], schema) => {
    return isBig ? schema.min(5) : schema.min(0);
  }),
});

await schema.validate({ isBig: false, count: 4 });
```

#### Schema.test(name: string, message: string | function | any, test: function): Schema

유효성 검사 체인에 테스트 함수를 추가한다. 테스트는 객체가 캐스트된 후에 실행된다. 많은 타입에 내장된 테스트가 있지만, 원한다면 커스텀 테스트를 쉽게 만들 수 있다. 비동기 커스텀 유효성 검사를 허용하기 위해 모든 테스트가 비동기로 실행되거나 동기로 실행된다. 따라서 테스트 실행 순서는 보장되지 않는다.

모든 테스트는 `name`, 오류 `message`를 제공해야 한다. 그리고 현재 `value`가 유효하고 `false`이면 `true`를 반환하는 유효성 검사 함수나  `ValidationError`를 제공해야 한다. 테스트를 비동기화하려면 `true` 또는 `false` 또는 `ValidationError`를 이행하는 프라미스를 반환한다.

`message` 인수의 경우 `${param}` 문법을 사용하여 지정된 경우 특정 값을 보간하는 문자열을 제공할 수 있다. 기본적으로 모든 테스트 메시지에는 중첩된 스키마에서 중요한 `path` 값이 전달된다.

`test` 함수는 현재 `value`와 함께 호출된다. 고급 유효성 검사를 원한다면 대체 시그니처를 사용하여 더 많은 옵션을 제공할 수 있다.

```typescript
let jimmySchema = string().test(
  'is-jimmy',
  '${path} is not Jimmy',
  (value, context) => value === 'jimmy',
);

// 프라미스를 반환하여 비동기로 만든다.
let asyncJimmySchema = string()
  .label('First name')
  .test(
    'is-jimmy',
    ({ label }) => `${label} is not Jimmy`, // 메시지는 함수 형태도 가능하다.
    async (value, testContext) =>
      (await fetch('/is-jimmy/' + value)).responseText === 'true',
  );

await schema.isValid('jimmy'); // => true
await schema.isValid('john'); // => false
```

테스트 함수는 유용한 메타데이터와 함수를 노출하는 두 번째 인수로 특수한 컨텍스트 값으로 호출된다. 화살표가 아닌 함수의 경우 테스트 컨텍스트가 함수 `this`로도 설정된다. 화살표 함수에서는 `this`로 접근할 수 없다는 점을 주의한다.

- `testContext.path` - 현재 유효성 검사의 문자열 경로
- `testContext.schema` - 테스트가 실행 중인 이행된 스키마 객체
- `testContext.options` - `validate()` 또는 `isValid()`가 호출된 `options` 객체
- `testContext.parent` - 중첩된 스키마의 경우 부모 객체의 값
- `testContext.originalValue` - 테스트 중인 원래 값
- `testContext.createError(Object: { path: String, message: String, params: Object })` - 유효성 검사 오류를 생성하고 반환한다. `path`, `params` 또는 오류 `message`를 동적으로 설정하는 데 유용하다. 생략하면 현재 경로나 기본 메시지가 사용된다.
