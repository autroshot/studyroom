# API

## yup

### ref(path: string, options: { contextPrefix: string }): Ref

다른 형제나 형제 하위 필드에 대한 참조를 만듭니다. 참조는 유효성 검사나 캐스트 시간에 이행되고 지정된 경우 지원됩니다. `ref`를 사용하는 필드보다 `ref` 값이 먼저 이행되도록 적절한 순서로 평가됩니다. 순환 종속성에 주의해야 합니다.

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

## Schema

### Schema.when(keys: string | string[], builder: object | (values: any[], schema) => Schema): Schema

형제 또는 형제 자식 필드를 기반으로 스키마를 조정합니다. `is`에 값 또는 일치자 함수를, `then`에 `true`일 때의 스키마를, `otherwise`에 `false`일 때의 스키마를 가진 객체 리터럴을 인수로 받습니다.

`is` 조건은 일치 연산자(`===`)를 사용합니다. 동등 연산자(`==`)를 사용하려면 다음과 같은 함수를 건네줍니다.

```typescript
is: (value) => value == true
```

또한 프로퍼티 앞에 `$`를 붙여 입력 값 대신 `validate()` 또는 `cast`에서 전달된 `context`에 종속되는 프로퍼티를 지정할 수도 있습니다.

다음 예시에서는 복수의 `when` 조건을 추가합니다.

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

둘 이상의 종속 키를 지정할 수도 있습니다. 이 경우 각 값은 인수로 확산됩니다.

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

또는 현재 스키마에 제공된 각 키에 대한 값 배열로 호출되는 스키마를 반환하는 함수를 제공할 수 있습니다.

```typescript
let schema = yup.object({
  isBig: yup.boolean(),
  count: yup.number().when('isBig', ([isBig], schema) => {
    return isBig ? schema.min(5) : schema.min(0);
  }),
});

await schema.validate({ isBig: false, count: 4 });
```

### Schema.test(name: string, message: string | function | any, test: function): Schema

유효성 검사 체인에 테스트 함수를 추가합니다. 테스트는 객체가 캐스트된 후에 실행됩니다. 많은 타입에 내장된 테스트가 있지만, 원한다면 커스텀 테스트를 쉽게 만들 수 있습니다. 비동기 커스텀 유효성 검사를 허용하기 위해 모든 테스트가 비동기로 실행되거나 동기로 실행됩니다. 따라서 테스트 실행 순서는 보장되지 않습니다.

모든 테스트는 `name`, 오류 `message`를 제공해야 합니다. 그리고 현재 `value`가 유효하고 `false`이면 `true`를 반환하는 유효성 검사 함수나  `ValidationError`를 제공해야 합니다. 테스트를 비동기화하려면 `true` 또는 `false` 또는 `ValidationError`를 이행하는 프라미스를 반환합니다.

`message` 인수의 경우 `${param}` 문법을 사용하여 지정된 경우 특정 값을 보간하는 문자열을 제공할 수 있습니다. 기본적으로 모든 테스트 메시지에는 중첩된 스키마에서 중요한 `path` 값이 전달됩니다.

`test` 함수는 현재 `value`와 함께 호출됩니다. 고급 유효성 검사를 원한다면 대체 시그니처를 사용하여 더 많은 옵션을 제공할 수 있습니다.

```typescript
let jimmySchema = string().test(
  'is-jimmy',
  '${path} is not Jimmy',
  (value, context) => value === 'jimmy',
);

// 프라미스를 반환하여 비동기로 만듭니다.
let asyncJimmySchema = string()
  .label('First name')
  .test(
    'is-jimmy',
    ({ label }) => `${label} is not Jimmy`, // 메시지는 함수 형태도 가능합니다.
    async (value, testContext) =>
      (await fetch('/is-jimmy/' + value)).responseText === 'true',
  );

await schema.isValid('jimmy'); // => true
await schema.isValid('john'); // => false
```

테스트 함수는 유용한 메타데이터와 함수를 노출하는 두 번째 인수로 특수한 컨텍스트 값으로 호출됩니다. 화살표가 아닌 함수의 경우 테스트 컨텍스트가 함수 `this`로도 설정됩니다. 화살표 함수에서는 `this`로 접근할 수 없다는 점을 주의합니다.

- `testContext.path` - 현재 유효성 검사의 문자열 경로
- `testContext.schema` - 테스트가 실행 중인 이행된 스키마 객체
- `testContext.options` - `validate()` 또는 `isValid()`가 호출된 `options` 객체
- `testContext.parent` - 중첩된 스키마의 경우 부모 객체의 값
- `testContext.originalValue` - 테스트 중인 원래 값
- `testContext.createError(Object: { path: String, message: String, params: Object })` - 유효성 검사 오류를 생성하고 반환합니다. `path`, `params` 또는 오류 `message`를 동적으로 설정하는 데 유용합니다. 생략하면 현재 경로나 기본 메시지가 사용됩니다.