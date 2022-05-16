# 테스트 자동화와 Mocha
## 테스트는 왜 해야 하는가?
코드를 수동으로 재실행하면서 테스트를 하면 무언가를 놓치기 쉬우므로 테스트를 자동화할 필요가 있다. 테스팅 자동화는 테스트 코드가 실제 동작에 관여하는 코드와 별개로 작성되었을 때 가능하다. 테스트 코드를 이용하면 함수를 다양한 조건에서 실행해 볼 수 있는데, 이때 실행 결과와 기대 결과를 비교할 수 있다.

### Behavior Driven Development(BDD)
BDD는 테스트(test), 문서(documentation), 예시(example)를 한데 모아놓은 개념이다.

## 명세서
본격적으로 코드를 작성하기 전에 코드가 무슨 일을 하는지 상상한 후 이를 자연어로 표현해야 한다. 이때 만들어지는 산출물을 BDD에서는 명세서(specification) 또는 짧게 줄여서 **스펙**(spec)이라고 부른다.
```js
describe('pow', function() {
  it('주어진 숫자의 n 제곱', function() {
    assert.equal(pow(2, 3), 8);
  });
});
```
스펙은 다음과 같이 세 가지로 구성된다.
1. `describe('title', function() { ... })`  
: 구현하고자 하는 기능에 대한 설명이 들어가며 `it` 블록을 한데 모아주는 역할도 한다.

2. `it('유스 케이스 설명', function() { ... })`  
: `it`의 첫 번째 인수에는 특정 유스 케이스에 대한 설명이 들어갑니다. 이 설명은 누구나 읽을 수 있고 이해할 수 있는 자연어로 적어준다. 두 번째 인수에는 유스 케이스 테스트 함수가 들어간다.

3. `assert.equal(value1, value2)`  
: 기능을 제대로 구현했다면 it 블록 내의 코드가 에러 없이 실행된다. assert.equal은 인수끼리 동등 비교했을 때 다르다고 판단되면 에러를 반환한다.

## 개발 순서
1. 명세서 초안을 작성한다. 초안에는 기본적인 테스트도 들어간다.
2. 명세서 초안을 보고 코드를 작성한다.
3. 코드가 작동하는지 확인하기 위해 Mocha라 불리는 테스트 프레임워크를 사용해 명세서를 실행한다. 이때, 코드가 잘못 작성되었다면 에러가 출력된다. 개발자는 테스트를 모두 통과해 에러가 더는 출력되지 않을 때까지 코드를 수정한다.
4. 이제 모든 테스트를 통과하는 코드 초안이 완성되었다.
5. 명세서에 지금까지는 고려하지 않았던 유스케이스 몇 가지를 추가한다. 테스트가 실패하기 시작할 것이다.
6. 세 번째 단계로 돌아가 테스트를 모두 통과할 때까지 코드를 수정한다.
7. 기능이 완성될 때까지 3~6단계를 반복한다.

## 스펙 실행하기
테스트에 사용되는 라이브러리는 아래 세 가지다.
- [Mocha](http://mochajs.org/): 핵심 테스트 프레임워크로 `describe`, `it`과 같은 테스팅 함수와 테스트 실행 관련 주요 함수를 제공한다.
- [Chai](http://chaijs.com): 다양한 assertion을 제공해 주는 라이브러리다.
- [Sinon](http://sinonjs.org/): 함수의 정보를 얻을 때 사용되는 라이브러리로 내장 함수 등을 모방한다. 본 챕터에서는 사용하지 않는다.

```js
<!DOCTYPE html>
<html>
<head>
  <!-- 결과 출력에 사용되는 mocha css를 불러온다. -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/mocha/3.2.0/mocha.css">
  <!-- Mocha 프레임워크 코드를 불러온다. -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/mocha/3.2.0/mocha.js"></script>
  <script>
    mocha.setup('bdd'); // 기본 셋업
  </script>
  <!-- chai를 불러온다. -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/chai/3.5.0/chai.js"></script>
  <script>
    // chai의 다양한 기능 중, assert를 전역에 선언한다.
    let assert = chai.assert;
  </script>
</head>
<body>
  <script>
    function pow(x, n) {
      /* 작동 코드를 여기에 작성한다. */
    }
  </script>

  <!-- 테스트(describe, it...)가 있는 스크립트를 불러온다. -->
  <script src="test.js"></script>

  <!-- 테스트 결과를 id가 "mocha"인 요소에 출력하도록 한다.-->
  <div id="mocha"></div>

  <!-- 테스트를 실행한다. -->
  <script>
    mocha.run();
  </script>
</body>
</html>
```

## 스펙 개선하기
스펙에 테스트를 추가하는 방법은 두 가지가 있다.
1. 기존 `it` 블록에 `assert`를 하나 더 추가한다.
```js
describe('pow', function() {
  it('주어진 숫자의 n 제곱', function() {
    assert.equal(pow(2, 3), 8);
    assert.equal(pow(3, 4), 81);
  });
});
```
2. 테스트를 하나 더 추가한다. 즉, `it` 블록을 하나 더 추가한다.
```js
describe('pow', function() {
  it('2를 세 번 곱하면 8이다.', function() {
    assert.equal(pow(2, 3), 8);
  });

  it('3을 네 번 곱하면 81이다.', function() {
    assert.equal(pow(3, 4), 81);
  });
});
```
`assert`에서 에러가 발생하면 `it` 블록은 즉시 종료된다. 기존 `it` 블록에 `assert`를 하나 더 추가하면 첫 번째 `assert`가 실패했을 때 두 번째 `assert`의 결과를 알 수 없다. 따라서 더 많은 정보를 얻을 수 있는 두 번째 방법을 추천한다. 또한 하나의 테스트에서는 한 가지만 확인하는 것을 권장한다.

## 코드 개선하기
수동으로 여러 개의 `it` 블록을 만드는 대신 `for`문을 사용해 자동으로 `it` 블록을 만들 수 있다.
```js
describe('pow', function() {
  function makeTest(x) {
    let expected = x * x * x;
    it(`${x}을/를 세 번 곱하면 ${expected}이다.`, function() {
      assert.equal(pow(x, 3), expected);
    });
  }

  for (let x = 1; x <= 5; x++) {
    makeTest(x);
  }
});
```

## 중첩 describe
중첩 `describe`는 새로운 테스트 '하위 그룹(subgroup)'을 정의할 때 사용된다. 이렇게 새로 정의된 테스트 하위 그룹은 테스트 결과 보고서에 들여쓰기 된 상태로 출력된다.
```js
describe('pow', function() {
  describe('x를 세 번 곱한다.', function() {
    function makeTest(x) {
      let expected = x * x * x;
      it(`${x}을/를 세 번 곱하면 ${expected}이다.`, function() {
        assert.equal(pow(x, 3), expected);
      });
    }

    for (let x = 1; x <= 5; x++) {
      makeTest(x);
    }
  });
  // describe와 it을 사용해 이 아래에 더 많은 테스트를 추가할 수 있다.
});
```

## before/after와 beforeEach/afterEach
함수 `before`는 (전체) 테스트가 실행되기 전에 실행되고, 함수 `after`는 (전체) 테스트가 실행된 후에 실행된다. 함수 `beforeEach`는 매 `it`이 실행되기 전에 실행되고, 함수 `afterEach`는 매 `it`이 실행된 후에 실행된다. 이 기능들은 대개 초기화 용도로 사용된다.
```js
describe("test", function() {
  before(() => alert("테스트를 시작한다 - 테스트가 시작되기 전"));
  after(() => alert("테스트를 종료한다 - 테스트가 종료된 후"));

  beforeEach(() => alert("단일 테스트를 시작한다 - 각 테스트 시작 전"));
  afterEach(() => alert("단일 테스트를 종료한다 - 각 테스트 종료 후"));

  it('test 1', () => alert(1));
  it('test 2', () => alert(2));
});
```
실행 순서는 다음과 같다.
```js
테스트를 시작한다 - 테스트가 시작되기 전          (before)
단일 테스트를 시작한다 - 각 테스트 시작 전         (beforeEach)
1
단일 테스트를 종료한다 - 각 테스트 종료 후         (afterEach)
단일 테스트를 시작한다 - 각 테스트 시작 전         (beforeEach)
2
단일 테스트를 종료한다 - 각 테스트 종료 후         (afterEach)
테스트를 종료한다 - 테스트가 종료된 후            (after)
```

## 스펙 확장하기
`n`이 조건에 맞지 않을 때 함수가 `NaN`을 반환하는지 아닌지를 검사해주는 테스트를 추가한다.
```js
describe('pow', function() {
  // ...

  it('n이 음수일 때 결과는 NaN이다.', function() {
    assert.isNaN(pow(2, -1));
  });

  it('n이 정수가 아닐 때 결과는 NaN이다.', function() {
    assert.isNaN(pow(2, 1.5));
  });
});
```
BDD의 핵심은 **실패할 수밖에 없는 테스트를 추가하고, 테스트를 통과할 수 있게(에러가 발생하지 않게) 코드를 개선하는 것**이다.

`pow` 함수를 다음과 같이 개선하면 새로운 테스트를 통과할 것이다.
```js
function pow(x, n) {
  if (n < 0) return NaN;
  if (Math.round(n) != n) return NaN;

  let result = 1;

  for (let i = 0; i < n; i++) {
    result *= x;
  }

  return result;
}
```

## 다양한 assertion
- `assert.equal(value1, value2)`: `value1 == value2`
- `assert.strictEqual(value1, value2)`: `value1 === value2`
- `assert.notEqual`, `assert.notStrictEqual`: `value1 != value2`, `value1 !== value2`
- `assert.isTrue(value)`: `value === true`
- `assert.isFalse(value)`: `value === false`

이 외의 다양한 assertion은 [docs](http://chaijs.com/api/assert/)에서 확인할 수 있다.

## 요약
스펙의 용도는 다음 세 가지이다.
1. **테스트**: 함수가 의도하는 동작을 제대로 수행하고 있는지 보장한다.
2. **문서**: 함수가 어떤 동작을 수행하고 있는지 설명해준다. `describe`와 `it`에 설명이 들어간다.
3. **예시**: 실제 동작하는 예시를 이용해 함수를 어떻게 사용할 수 있는지 알려준다.

테스트를 하지 않고 코드를 작성해왔다면 개발자들은 둘 중 하나의 결과를 맞이하게 된다. 테스팅 자동화는 이런 문제들을 피할 수 있게 만들어준다.
1. **아무 대책 없이 코드를 변경**한다. 부작용을 생각하지 않고 함수를 수정했기 때문에 어디선가 버그가 발생하고 만다.
2. 버그의 대가가 가혹하기 때문에 **수정이나 개선을 기피**하게 되고, 결국 코드는 구식이 되어버린다.

테스팅 자동화의 또다른 장점은 **아키텍처의 품질을 보장**한다는 것이다. 테스트를 작성하려면 함수가 어떤 동작을 하는지, 입력값은 무엇이고 출력값은 무엇인지 정의하고 난 후에 구현을 시작한다. 구현을 시작하는 순간부터 이미 좋은 아키텍처가 보장되는 것이다.
