# BigInt
---
:warning: **최근에 추가됨**

스펙에 추가된 지 얼마 안 된 문법이다.

---
`BigInt`는 길이의 제약 없이 정수를 다룰 수 있게 해주는 숫자형이다.

정수 리터럴 끝에 `n`을 붙이거나 함수 `BigInt`를 호출하면 문자열이나 숫자를 가지고 `BigInt` 타입의 값을 만들 수 있다.
```js
const bigint = 1234567890123456789012345678901234567890n;

const sameBigint = BigInt("1234567890123456789012345678901234567890");

const bigintFromNumber = BigInt(10); // 10n과 동일하다.
```

## 수학 연산자
`BigInt`는 대부분 일반 숫자와 큰 차이 없이 사용할 수 있다.
```js
alert(1n + 2n); // 3

alert(5n / 2n); // 2
```
위 예시에서 나눗셈 연산 `5/2`의 결과엔 소수 부분이 없다는 점을 유의해야 한다. **bigInt를 대상으로 한 연산은 bigInt를 반환한다.**

bigInt와 일반 숫자를 섞어서 사용할 수는 없다.
```js
alert(1n + 2); // Error: Cannot mix BigInt and other types
```
일반 숫자와 섞어서 써야 하는 상황이라면 `BigInt()`나 `Number()`를 사용해 명시적으로 형 변환을 해주면 된다.
```js
let bigint = 1n;
let number = 2;

// 숫자를 bigint로 바꾼다.
alert(bigint + BigInt(number)); // 3

// bigint를 숫자로 바꾼다.
alert(Number(bigint) + number); // 3
```
형 변환과 관련된 연산은 항상 조용히 동작한다. 절대 에러가 발생하지 않는다. 그런데 bigint가 너무 커서 숫자형에서 허용하는 자릿수를 넘으면 나머지 비트는 자동으로 잘린다. 이 점에 유의하며 형 변환을 해야 한다.

---
:information_source: **단항 덧셈 연산자는 bigint에 사용할 수 없다.**

알다시피 단항 덧셈 연산자 `+value`를 사용하면 `value`를 쉽게 숫자형으로 바꿀 수 있다.

혼란을 막기 위해 bigint를 대상으로는 단항 덧셈 연산자를 지원하지 않는다.
```js
let bigint = 1n;

alert( +bigint ); // 에러
```
bigint를 숫자형으로 바꿀 때는 `Number()`를 사용해야 한다.

---

## 비교 연산자
비교 연산자 `<`, `>`는 bigint와 숫자형 둘 다에 사용할 수 있다.
```js
alert(2n > 1n); // true

alert(2n > 1); // true
```
숫자형와 bigint는 다른 형이므로, 동등 연산자 `==`에서는 결과가 `true`일 수 있어도 일치 연산자 `===`에서는 결과가 반드시 `false`이다.
```js
alert(1 == 1n); // true

alert(1 === 1n); // false
```

## 논리 연산
bigint는 `if` 안이나 다른 논리 연산자와 함께 사용할 때 숫자형과 동일하게 동작한다.

`if`안에서 `0n`은 falsy이고 다른 값들은 truthy로 평가된다.
```js
if (0n) {
  // 절대 실행되지 않는다.
}
```
`||`, `&&` 같은 논리 연산자를 bigint에 사용할 때도 숫자형과 유사하게 동작한다.
```js
alert(1n || 2); // 1 (1n은 truthy로 판단된다)

alert(0n || 2); // 2 (0n은 falsy로 판단된다)
```

## 폴리필
bigint 폴리필을 만드는 것은 까다롭다. `+`, `-`를 비롯한 다양한 연산자들이 bigint와 일반 숫자에서 다른 결과를 보이기 때문이다.

bigint끼리 나누면 항상 bigint를 반환하는 것이 그 예이다.

동일한 결과가 나오게 하려면 폴리필에서 기존 코드를 분석하고 내장 연산자 모두를 관련 함수로 대체해 줄 수 있어야 한다. 그런데 이런 방식은 복잡하고 성능 이슈가 생길 수 있다.

따라서 잘 알려진 좋은 bigint 폴리필은 없다.

그러나 [JSBI](https://github.com/GoogleChromeLabs/jsbi) 라이브러리의 개발자들이 제안한 대안이 있다.

이 라이브러리는 자체 메소드를 사용해 큰 숫자를 구현한다. 네이티브 bigint 대신 이 메소드를 사용할 수 있다.
| 연산 | 네이티브 `BigInt` | JSBI |
|-----------|-----------------|------|
| 일반 숫자를 사용해 bigint 만들기 | `a = BigInt(789)` | `a = JSBI.BigInt(789)` |
| 덧셈 | `c = a + b` | `c = JSBI.add(a, b)` |
| 뺄셈	| `c = a - b` | `c = JSBI.subtract(a, b)` |
| ... | ... | ... |

이렇게 JSBI를 사용해 숫자를 만든 다음 바벨 플러그인에 있는 폴리필을 사용해 JSBI 호출을 네이티브 bigint로 변환하면 원하는 브라우저에서 연산을 수행할 수 있다.

다시 말해 이 접근 방식은 네이티브 bigint 대신 JSBI로 코드를 작성할 것을 제안한다. 그러나 JSBI는 내부적으로 bigint와 마찬가지로 숫자형으로 작동하고 명세서를 모방하므로 코드는 bigint-ready가 될 것이다.

이러한 JSBI 코드는 bigint를 지원하지 않는 엔진과 지원하는 엔진에서 '있는 그대로' 사용할 수 있다. 폴리필은 호출을 네이티브 bigint로 변환한다.
