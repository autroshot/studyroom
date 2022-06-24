# while과 for 반복문
반복문 본문이 한 번 실행되는 것을 <strong>반복(iteration, 이터레이션)</strong>이라고 부른다.

## ‘while’ 반복문
`while` 반복문의 문법은 다음과 같다.
```js
while (condition) {
  // 반복문 본문
}
```
`condition`이 truthy이면 반복문 본문(body)의 코드가 실행된다.

본문이 한 줄이면 대괄호를 생략할 수 있다.
```js
let i = 3;
while (i) alert(i--);
```

## ‘do...while’ 반복문
**본문을 최소 한 번은 실행**하고 싶을 때 사용한다. 대부분의 상황에서는 `while`문을 사용한다.
```js
do {
  // 반복문 본문
} while (condition);
```

## ‘for’ 반복문
```js
for (begin; condition; step) {
  // 반복문 본문
}
```
작동 방식은 다음과 같다.
```js
begin을 실행함
→ (condition이 truthy이면 → body를 실행한 후, step을 실행함)
→ (condition이 truthy이면 → body를 실행한 후, step을 실행함)
→ ...
```
- `for`문의 구성 요소는 필요에 따라 생략이 가능하다.
- `break`를 사용하면 원하는 때에 반복문을 빠져나갈 수 있다.
- `continue`을 사용하면 현재 반복을 멈추고 다음 반복으로 넘어간다. 중첩을 줄이는 데 도움을 준다.
```js
for (let i = 0; i < 10; i++) {
  // 조건이 참이라면 남아있는 본문은 실행되지 않습니다.
  if (i % 2 == 0) continue;

  alert(i); // 1, 3, 5, 7, 9가 차례대로 출력됨
}
```
- `break`나 `continue` 같이 표현식이 아닌 문법 구조는 삼항 연산자 `?`에 사용할 수 없다.
```js
(i > 5) ? alert(i) : continue; // continue 때문에 에러가 발생한다.
```

## break/continue와 레이블
반복문에 식별자를 붙일 수 있다.
```js
labelName: for (...) {...}
```
`break <labelName>`을 사용하면 해당하는 반복문을 빠져나올 수 있다. 레이블은 **중첩 반복문을 빠져나오는 유일한 방법**이다. `continue`의 사용법도 동일하다.
```js
outer: for (let i = 0; i < 3; i++) {
  for (let j = 0; j < 3; j++) {
    let input = prompt(`(${i},${j})의 값`, '');
    
    // 사용자가 아무것도 입력하지 않거나 Cancel 버튼을 누르면 두 반복문 모두를 빠져나온다.
    if (!input) break outer;

    // 입력받은 값을 가지고 무언가를 한다.
  }
}
alert('완료!');
```
레이블은 사용되는 `break/continue` 이전에 있어야 한다.
