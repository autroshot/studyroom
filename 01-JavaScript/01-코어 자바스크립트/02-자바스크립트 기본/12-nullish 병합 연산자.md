# nullish 병합 연산자 ‘??’
---
:warning: **최근에 추가됨**

스펙에 추가된 지 얼마 안 된 문법이다. 구식 브라우저는 폴리필이 필요하다.

---

<strong>nullish 병합 연산자(nullish coalescing operator)</strong> `??`는 여러 피연산자 중 **값이 할당된 변수**를 찾는다. 즉, `null`이나 `undefined`가 아닌 변수를 찾는다. 변수에 **기본값을 할당하는 용도**로 사용할 수 있다.
```js
let firstName = null;
let lastName = null;
let nickName = '바이올렛';
    
alert(firstName ?? lastName ?? nickName ?? '익명의 사용자'); // 바이올렛
```
- `??`와 `||`의 차이: 기능은 비슷하나 변수에 `0`이 할당될 수 있으면 `??`가 더 적합하다.
- `??`의 우선순위는 꽤 낮다. 따라서 `??`를 사용할 때는 **괄호를 사용**하는 것이 좋다.
- `??`를 괄호 없이 `||`나 `&&`와 함께 사용하면 에러가 발생한다.
```js
let x = 1 && 2 ?? 3; // SyntaxError: Unexpected token '??'
```
