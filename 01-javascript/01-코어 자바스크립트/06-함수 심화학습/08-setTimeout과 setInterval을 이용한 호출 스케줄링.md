# setTimeout과 setInterval을 이용한 호출 스케줄링
일정 시간이 지난 후에 원하는 함수를 예약 실행(호출)할 수 있게 하는 것을 <strong>호출 스케줄링(scheduling a call)</strong>이라고 한다.

호출 스케줄링을 구현하는 방법은 두 가지가 있다.
-   `setTimeout`을 이용해 일정 시간이 지난 후에 함수를 실행하는 방법
-   `setInterval`을 이용해 일정 시간 간격을 두고 함수를 실행하는 방법

자바스크립트 명세서에는 `setTimeout`과 `setInterval`가 명시되어 있지 않다. 하지만 모든 브라우저, Node.js를 포함한 자바스크립트 호스트 환경 대부분이 이와 유사한 메서드와 내부 스케줄러를 지원한다.

## setTimeout
```js
let timerId = setTimeout(func|code, [delay], [arg1], [arg2], ...)
```
### func|code
실행하고자 하는 코드로 함수 또는 문자열이 들어간다. 문자열은 하위 호환성을 위한 것이고 보통은 함수를 넣는다.

### delay
실행 전 대기 시간이다. 단위는 밀리초(millisecond)이며 기본값은 0이다.

### arg1, arg2…
함수에 전달할 인수들이다. IE9 이하에서는 지원하지 않는다.

사용법은 다음 예시들을 참고한다.
```js run
function sayHi() {
  alert('안녕하세요.');
}

setTimeout(sayHi, 1000);
```
```js
function sayHi(who, phrase) {
  alert(who + ' 님, ' + phrase);
}

setTimeout(sayHi, 1000, '홍길동', '안녕하세요.'); // 홍길동 님, 안녕하세요.
```
첫 번째 인수에 문자열 대신 다음과 같이 익명 화살표 함수를 사용하는 것을 추천한다.
```js
setTimeout(() => alert('안녕하세요.'), 1000);
```
---
:information_source: **함수를 실행하지 말고 넘겨야 한다.**

`setTimeout`에 함수를 넘길 때, 함수 뒤에 `()` 을 붙이는 실수를 자주 한다.
```js
// 잘못된 코드
setTimeout(sayHi(), 1000);
```
`setTimeout`은 함수의 참조 값을 받도록 정의되어 있는데 `sayHi()`를 인수로 전달하면 '함수 실행 결과'가 전달되어 버린다.

---

### clearTimeout으로 스케줄링 취소하기
`setTimeout`을 호출하면 <strong>타이머 식별자(timer identifier)</strong>가 반환된다. 스케줄링을 취소하고 싶을 때는 이 식별자를 사용하면 된다.
```js
let timerId = setTimeout(...);
clearTimeout(timerId);
```
```js run no-beautify
let timerId = setTimeout(() => alert('아무 일도 일어나지 않습니다.'), 1000);
alert(timerId); // 타이머 식별자

clearTimeout(timerId);
alert(timerId); // 위 타이머 식별자와 동일함(취소 후에도 식별자의 값은 null이 되지 않는다.)
```
브라우저 환경에서는 식별자가 숫자이다. 다른 호스트 환경에서는 타이머 식별자가 숫자형 이외의 자료형일 수 있다. 참고로 Node.js에서 `setTimeout`을 실행하면 타이머 객체가 반환된다.

## setInterval
```js
let timerId = setInterval(func|code, [delay], [arg1], [arg2], ...)
```
사용법은 `setTimeout`과 동일하다.

`setTimeout`이 함수를 단 한 번만 실행하는 것과 달리 `setInterval`은 함수를 주기적으로 실행한다.

함수 호출을 중단하려면 `clearInterval(timerId)`을 사용하면 된다.
```js
// 2초 간격으로 메시지를 보여준다.
let timerId = setInterval(() => alert('째깍'), 2000);

// 5초 후에 정지한다.
setTimeout(() => { clearInterval(timerId); alert('정지'); }, 5000);
```
위 예시를 실행하면 메시지가 2초 간격으로 보이다가 5초 이후에는 더 이상 메시지가 보이지 않는다.

---
:information_source: **`alert` 창이 떠 있더라도 타이머는 멈추지 않는다.**

대부분의 브라우저는 `alert/confirm/prompt` 창이 떠 있는 동안에도 내부 타이머가 멈추지 않는다.

---

## 중첩 setTimeout
무언가를 일정 간격을 두고 실행할 때 `setInterval` 대신 중첩 `setTimeout`을 이용할 수 있다.
```js
/** setInterval을 이용하지 않고 아래와 같이 중첩 setTimeout을 사용한다.
let timerId = setInterval(() => alert('째깍'), 2000);
*/

let timerId = setTimeout(function tick() {
  alert('째깍');
  timerId = setTimeout(tick, 2000);
}, 2000);
```
중첩 `setTimeout`을 이용하는 방법은 `setInterval`을 사용하는 방법보다 유연하다. 호출 결과에 따라 다음 호출을 원하는 방식으로 조정해 스케줄링 할 수 있기 때문이다.

5초 간격으로 서버에 요청을 보내 데이터를 얻는다고 가정해 본다. 서버가 과부하 상태라면 요청 간격을 10초, 20초, 40초 등으로 증가시켜주는 게 좋을 것이다.

아래는 이를 구현한 의사 코드이다.
```js
let delay = 5000;

let timerId = setTimeout(function request() {
  ...요청 보내기...

  if (서버 과부하로 인한 요청 실패) {
    // 요청 간격을 늘립니다.
    delay *= 2;
  }

  timerId = setTimeout(request, delay);

}, delay);
```
`setInterval`과 중첩 `setTimeout`은 다음과 같은 차이가 존재한다. 

**중첩  `setTimeout`을 이용하는 방법은 지연 간격을 보장하지만  `setInterval`은 이를 보장하지 않는다.**

중첩  `setTimeout`은 함수 실행이 끝나고 타이머가 흐르기 때문에 지연 간격이 보장된다.

![settimeout-interval](https://user-images.githubusercontent.com/95019875/166169631-fab25e58-f5c9-4925-a3a1-c870101f7656.svg)

반면 `setInterval`의 타이머는 함수 실행과 동시에 흐르기 때문에 지연 간격이 보장되지 않는다. 함수 실행 시간이 `delay`보다 길어지는 경우도 존재한다.

![setinterval-interval](https://user-images.githubusercontent.com/95019875/166169671-50f9a30f-c06d-446f-9a59-7a4b91237270.svg)

다음과 같은 상황에서 브라우저 내 타이머가 느려지면 지연 간격이 보장되지 않는다.
-   CPU가 과부하 상태인 경우
-   브라우저 탭이 백그라운드 모드인 경우
-   노트북이 배터리에 의존해서 구동 중인 경우

이런 상황에서 타이머의 최소 지연 시간은 300밀리초에서 심하면 1,000밀리초까지 늘어난다.

---
:information_source: **가비지 컬렉션과 setInterval·setTimeout**

`setInterval`과 `setTimeout`에 넘긴 함수는 가비지 컬렉션의 대상이 되지 않는다.

이런 동작 방식에는 부작용이 하나 존재한다. 외부 렉시컬 환경을 참조하는 함수가 있다면,  이 함수가 메모리에 남아있는 동안에는 외부 변수 역시 메모리에 남아있게 되어서 메모리가 낭비된다. 이런 부작용을 막기 위해 스케줄링할 필요가 없어진 함수는 아무리 작더라도 취소할 필요가 있다.

---

## 대기 시간이 0인 setTimeout
`setTimeout(func, 0)`이나 `setTimeout(func)`을 사용하면 `setTimeout`의 대기 시간을 0으로 설정할 수 있다.

이렇게 대기 시간을 0으로 설정하면 `func`을 ‘가능한 한’ 빨리 실행할 수 있다. 다만, 이때 스케줄러는 현재 실행 중인 스크립트의 처리가 종료된 이후에 스케줄링한 함수를 실행한다.

예시를 실행하면 얼럿창에 'Hello’와 'World’가 순서대로 출력되는 것을 확인할 수 있다.
```js
setTimeout(() => alert('World'));

alert('Hello');
```
예시에서 첫 번째 줄은 '0밀리초 후에 함수 호출하기’라는 할 일을 '계획표에 기록’해주는 역할을 한다. 스케줄러는 현재 스크립트의 실행이 종료되고 나서야 '계획표에 어떤 할 일이 적혀있는지 확인’하므로, `Hello`가 먼저 출력되고 `World`가 그 이후에 출력된다.

대기 시간이 0인 `setTimeout`을 활용한 브라우저 환경에서의 유스 케이스는 [이벤트 루프: 마이크로태스크와 매크로태스크](https://github.com/autroshot/studyroom/tree/main/01-javascript/02-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80:%20%EB%AC%B8%EC%84%9C%2C%20%EC%9D%B4%EB%B2%A4%ED%8A%B8%2C%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4#3-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%ED%83%9C%EC%8A%A4%ED%81%AC%EC%99%80-%EB%A7%A4%ED%81%AC%EB%A1%9C%ED%83%9C%EC%8A%A4%ED%81%AC) 챕터에서 자세히 다룰 것이다.

---
:information_source: **브라우저 환경에서 실제 대기 시간은 0이 아니다.**

HTML5 표준에는 "다섯 번째 중첩 타이머 이후에는 대기 시간을 최소 4밀리초 이상으로 강제해야 한다."라는 제약이 명시되어있다.

이런 제약은 `setTimeout`뿐만 아니라 `setInterval`에도 적용된다. `setInterval(f)`도 처음 몇 번은 함수 `f`를 지연 없이 실행하지만, 나중엔 지연 간격을 4밀리초 이상으로 늘려버린다.

이는 오래전부터 있던 제약인데, 구식 스크립트 중 일부는 아직 이 제약에 의존하는 경우가 있어서 명세서를 변경하지 못하고 있는 상황이다.

한편, 서버 측에는 이런 제약이 없다. Node.js의 [process.nextTick](https://nodejs.org/api/process.html)과 [setImmediate](https://nodejs.org/api/timers.html)를 이용하면 비동기 작업을 지연 없이 실행할 수 있다. 위에서 언급된 제약은 브라우저에 한정된다.

---
