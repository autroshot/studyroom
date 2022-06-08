# 'try..catch'와 에러 핸들링
에러가 발생하면 스크립트는 즉시 중단되고 콘솔에 에러가 출력된다.

`try..catch` 문법을 사용하면 스크립트가 죽는 걸 방지하고, 에러를 잡아서 합리적인 조치를 할 수 있다.

## ‘try…catch’ 문법
```js
try {
  // 코드...
} catch (err) {
  // 에러 핸들링
}
```
try…catch 동작 알고리즘은 다음과 같다.

1.  `try {...}`  안의 코드가 실행된다.
2.  에러가 없다면,  `try`  안의 마지막 줄까지 실행되고,  `catch`  블록은 건너뛴다.
3.  에러가 있다면,  `try`  안 코드의 실행이 중단되고 `catch(err)`  블록으로 제어 흐름이 넘어간다. 변수  `err`(아무 이름이나 사용 가능)는 무슨 일이 일어났는지에 대한 설명이 담긴 에러 객체를 포함한다.

`try {...}` 블록 안에서 에러가 발생해도 `catch`에서 에러를 처리하기 때문에 스크립트는 죽지 않는다.

---
:warning: **`try..catch`는 오직 런타임 에러에만 동작한다.**

`try..catch`는 <strong>실행 가능한(runnable)</strong> 코드에서만 동작한다. 실행 가능한 코드는 유효한 자바스크립트 코드를 의미합니다.

문법적으로 잘못된 경우에는 `try..catch`가 동작하지 않는다.
```js
try {
  {{{{{{{{{{{{
} catch(e) {
  alert('유효하지 않은 코드이기 때문에, 자바스크립트 엔진은 이 코드를 이해할 수 없습니다.');
}
```
자바스크립트 엔진은 코드를 읽고 난 후 코드를 실행한다. 코드를 읽는 중에 발생하는 에러는 'parse-time 에러’라고 부르는데, 엔진은 이 코드를 이해할 수 없기 때문에 parse-time 에러는 코드 안에서 복구가 불가능하다.

`try..catch`는 유효한 코드에서 발생하는 에러만 처리할 수 있다. 이런 에러를 <strong>런타임 에러(runtime error)</strong> 혹은 <strong>예외(exception)</strong>라고 부른다.

---
:warning: **`try..catch`는 동기적으로 동작한다.**

setTimeout처럼 ‘스케줄 된(scheduled)’ 코드에서 발생한 예외는 `try..catch`에서 잡아낼 수 없다.
```js
try {
  setTimeout(function() {
    noSuchVariable; // 스크립트는 여기서 죽는다.
  }, 1000);
} catch (e) {
  alert('작동 멈춤');
}
```
`setTimeout`에 넘겨진 익명 함수는 엔진이 `try..catch`를 떠난 다음에서야 실행되기 때문이다.

스케줄 된 함수 내부의 예외를 잡으려면, `try..catch`를 반드시 함수 내부에 구현해야 한다.
```js
setTimeout(function() {
  try {    
    noSuchVariable; // 이제 try..catch에서 에러를 핸들링 할 수 있다.
  } catch {
    alert('에러를 잡았습니다!');
  }
}, 1000);
```
---

## 에러 객체
에러가 발생하면 자바스크립트는 에러 상세내용이 담긴 객체를 생성한다. 그 후 `catch` 블록에 이 객체를 인수로 전달한다.
```js
try {
  // ...
} catch(err) { // <-- '에러 객체', err 대신 다른 이름으로도 쓸 수 있다.
  // ...
}
```
모든 내장 에러에 대해 에러 객체는 두 가지 주요 프로퍼티를 가진다.
### name
에러의 이름이다. 정의되지 않은 변수 때문에 발생한 에러라면  `"ReferenceError"`가 이름이 된다.

### message
에러의 상세 내용을 담고 있는 메시지이다.

표준은 아니지만,  `name`과  `message`  이외에 대부분의 호스트 환경에서 지원하는 프로퍼티도 있다.  `stack`은 가장 널리 사용되는 비표준 프로퍼티 중 하나이다.

### stack
현재 호출 스택이다. 에러를 유발한 중첩 호출들의 순서 정보를 가진 문자열로 디버깅 목적으로 사용된다.

```js
try {
  lalala; // 에러, 변수가 정의되지 않았다.
} catch(err) {
  alert(err.name); // ReferenceError
  alert(err.message); // lalala is not defined
  alert(err.stack); // ReferenceError: lalala is not defined at ... (호출 스택)

  // 에러 전체를 보여줄 수도 있다.
  // 이때, 에러 객체는 "name: message" 형태의 문자열로 변환된다.
  alert(err); // ReferenceError: lalala is not defined
}
```

## 선택적 ‘catch’ 바인딩
---
:warning: **최근에 추가됨**

스펙에 추가된 지 얼마 안 된 문법이다. 구식 브라우저는 폴리필이 필요하다.

---
에러에 대한 자세한 정보가 필요하지 않으면, `catch`에서 이를 생략할 수 있다.
```js
try {
  // ...
} catch { // <-- (err) 없이 쓸 수 있다.
  // ...
}
```

## ‘try…catch’ 사용하기
`try..catch`가 실무에서 어떻게 사용되는지 알아보겠다.

앞서 JSON으로 인코딩된 값을 읽을 수 있도록 해주는  [JSON.parse(str)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)  메서드에 대해 배웠다.

이 메서드는 주로 서버 등에서 네트워크를 통해 전달받은 데이터를 디코딩하는 데 사용한다. 전달받은 데이터에 `JSON.parse`를 호출하는 식으로 사용된다.

JSON에 관한 자세한 정보는 [JSON과 메서드](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95.md#12-json%EA%B3%BC-%EB%A9%94%EC%84%9C%EB%93%9C) 챕터에서 확인할 수 있다.

잘못된 형식의  `json`이 들어온 경우,  `JSON.parse`는 에러를 만들기 때문에 스크립트가 죽는다.

`try..catch`를 사용해 이를 처리해 보겠다.
```js run
let json = '{ bad json }';

try {
  let user = JSON.parse(json); // <-- 여기서 에러가 발생하므로

  alert(user.name); // 이 코드는 동작하지 않는다.
} catch (e) {
  // 에러가 발생하면 제어 흐름이 catch 문으로 넘어온다.
  alert('데이터에 에러가 있어 재요청을 시도합니다.');
  alert(e.name);
  alert(e.message);
}
```
`catch` 블록 안에서 새로운 네트워크 요청 보내기, 사용자에게 대안 제안하기, 로깅 장치에 에러 정보 보내기 등과 같은 작업을 추가할 수 있다.

## 직접 에러를 만들어서 던지기
`throw` 연산자는 에러를 생성한다.

문법:
```js
throw <error object>
```
이론적으로는 숫자, 문자열 같은 원시형 자료를 비롯한 어떤 것이든 <strong>에러 객체(error object)</strong>로 사용할 수 있다. 하지만 내장 에러와의 호환을 위해 되도록 에러 객체에 `name`과 `message` 프로퍼티를 넣어주는 것을 권장한다.

자바스크립트는 `Error`, `SyntaxError`, `ReferenceError`, `TypeError`등의 표준 에러 객체 관련 생성자를 지원한다. 이 생성자들을 이용해 에러 객체를 만들 수도 있다.
```js
let error = new Error(message);
// or
let error = new SyntaxError(message);
let error = new ReferenceError(message);
// ...
```
내장 생성자를 사용해 만든 내장 에러 객체의 `name` 프로퍼티는 생성자 이름과 동일한 값을 갖는다. 프로퍼티 `message`의 값은 인수에서 가져온다.
```js
let error = new Error('이상한 일이 발생했습니다. o_O');

alert(error.name); // Error
alert(error.message); // 이상한 일이 발생했습니다. o_O
```
`user`에 `name`이 없으면 에러가 발생한 것으로 간주하고 `thorw` 연산자를 사용해 예외 처리를 해보겠다.
```js run
let json = '{ 'age': 30 }'; // 불완전한 데이터이다.

try {
  let user = JSON.parse(json); // <-- 에러 없음

  if (!user.name) {
    throw new SyntaxError('불완전한 데이터: 이름 없음');
  }

  alert(user.name);
} catch(e) {
  alert('JSON Error: ' + e.message); // JSON Error: 불완전한 데이터: 이름 없음
}
```

## 에러 다시 던지기
또 다른 예기치 않은 에러가 `try {...}` 블록 안에서 발생할 수 있다.

**catch는 알고 있는 에러만 처리하고 나머지는 ‘다시 던져야’ 합니다.**

‘다시 던지기’ 기술은 다음과 같다.
1.  catch가 모든 에러를 받는다.
2.  `catch(err) {...}`  블록 안에서 에러 객체  `err`를 분석한다.
3.  에러 처리 방법을 알지 못하면  `throw err`를 한다.

보통 에러 타입은 `instanceof` 명령어로 확인한다.
```js
try {
  user = { /*...*/ };
} catch(err) {
  if (err instanceof ReferenceError) {
    alert('ReferenceError'); //  정의되지 않은 변수에 접근하여 'ReferenceError'가 발생했다.
  }
}
```
`err.name` 프로퍼티로도 에러 클래스 이름을 확인할 수 있다. 기본형 에러는 모두 `err.name` 프로퍼티를 가진다. 또는 `err.constructor.name`를 사용할 수도 있다.

에러를 다시 던져서 `catch` 블록에서는 `SyntaxError`만 처리되도록 하겠다.
```js run
let json = '{ "age": 30 }'; // 불완전한 데이터이다.
try {
  let user = JSON.parse(json);

  if (!user.name) {
    throw new SyntaxError('불완전한 데이터: 이름 없음');
  }

  blabla(); // 예상치 못한 에러

  alert(user.name);
} catch(e) {
  if (e instanceof SyntaxError) {
    alert('JSON Error: ' + e.message);
  } else {
    throw e; // 에러 다시 던지기
  }
}
```
`catch` 블록 안에서 다시 던져진 에러는 `try..catch` 밖으로 던져진다. 이때 바깥에 `try..catch`가 있다면 여기서 에러를 잡고, 없다면 스크립트는 죽는다.

이렇게 하면 `catch` 블록에서는 처리 가능한 에러만 처리하고, 알 수 없는 에러는 건너뛸 수 있다.

이제 `try..catch`를 하나 더 만들어. 다시 던져진 예상치 못한 에러를 처리해 보겠다.
```js
function readData() {
  let json = '{ "age": 30 }';

  try {
    // ...
    blabla(); // 에러
  } catch (e) {
    // ...
    if (!(e instanceof SyntaxError)) {
      throw e; // 알 수 없는 에러를 다시 던지기
    }
  }
}

try {
  readData();
} catch (e) {
  alert('External catch got: ' + e); // 에러를 잡는다.
}
```
`readData`는 `SyntaxError`만 처리할 수 있고, 함수 바깥의 `try..catch`에서는 예상치 못한 에러도 처리할 수 있게 되었다.

## try…catch…finally
`try..catch`는  `finally`라는 코드 절을 하나 더 가질 수 있다.

`finally`안의 코드는 다음과 같은 상황에서 실행된다.
-   에러가 없는 경우:  `try`  실행이 끝난 후
-   에러가 있는 경우:  `catch`  실행이 끝난 후

`finally`를 사용하면 `try..catch`를 다음과 같이 확장할 수 있다.
```js
try {
   ... 코드를 실행 ...
} catch(e) {
   ... 에러 핸들링 ...
} finally {
   ... 항상 실행 ...
}
```
다음 예시에서는 `fib` 함수에서 에러가 발생했는지와 상관없이, `finally`를 사용하면 연산 시간을 제대로 측정할 수 있다.
```js
let num = +prompt('양의 정수를 입력해주세요.', 35)

let diff, result;

function fib(n) {
  if (n < 0 || Math.trunc(n) != n) {
    throw new Error('음수나 정수가 아닌 값은 처리할 수 없습니다.');
  }
  return n <= 1 ? n : fib(n - 1) + fib(n - 2);
}

let start = Date.now();

try {
  result = fib(num);
} catch (e) {
  result = 0;
} finally {
  diff = Date.now() - start;
}

alert(result || '에러 발생');

alert(`연산 시간: ${diff}ms`);
```

---
:information_source: **`try..catch..finally` 안의 변수는 지역 변수이다.**

위 예시에서 변수 `diff`와 `result`는 `try..catch`  전에 선언된 것을 주목해야 한다.

---
:information_source: **`finally` 와 `return`**

`finally` 절은 `try..catch` 절을 빠져나가는 어떤 경우에도 실행된다. `return`을 사용해 명시적으로 빠져나가려는 경우도 마찬가지이다.

다음 예시의 `try` 블록 안에는 `return`이 있다. 이 경우에는 값이 바깥 코드로 반환되기 전에 `finally`가 실행된다.
```js run
function func() {
  try {
    return 1;
  } catch (e) {
    /* ... */
  } finally {
    alert('finally');
  }
}

alert(func()); // 'finally'가 출력된 후, 1이 출력된다.
```
---
:information_source: **`try..finally`**

`catch` 절이 없는 `try..finally` 구문도 상황에 따라 유용하게 쓸 수 있다. `try..finally` 안에서 에러를 처리하고 싶지 않지만, 시작한 프로세스가 마무리되었는지 확실히 하고 싶은 경우에 사용한다.
```js
function func() {
  // 무언가를 측정하는 경우와 같이 끝맺음이 있어야 하는 프로세스
  try {
    // ...
  } finally {
    // 스크립트가 죽더라도 완료된다.
  }
}
```
위 코드에는 `catch`가 없기 때문에 `try` 안에서 발생한 에러는 항상 밖으로 떨어져 나온다. `finally`는 실행 흐름이 함수를 떠나기 전에 실행된다.

---

## 전역 catch
---
:warning: **호스트 환경을 확인해야 한다.**

이 절은 코어 자바스크립트가 아니다.

---
`try..catch` 바깥에서 발생한 에러를 잡는 것은 중요하기 때문에, 자바스크립트 호스트 환경 대다수는 자체적으로 에러 처리 기능을 제공한다.

Node.js의 [`process.on("uncaughtException")`](https://nodejs.org/api/process.html#process_event_uncaughtexception)이 그 예이다. 브라우저 환경에서는 [window.onerror](https://developer.mozilla.org/ko/docs/Web/API/GlobalEventHandlers/onerror)를 이용해 에러를 처리할 수 있다. window.onerror 프로퍼티에 함수를 할당하면, 예상치 못한 에러가 발생했을 때 이 함수가 실행된다.

문법:
```js
window.onerror = function(message, url, line, col, error) {
  // ...
};
```
- `message`: 에러 메시지
- `url`: 에러가 발생한 스크립트의 URL
- `line`, `col`: 에러가 발생한 곳의 줄과 열 번호
- `error`: 에러 객체

전역 핸들러 `window.onerror`는 죽어버린 스크립트를 복구하려는 목적으로는 잘 사용하지 않는다. 프로그래밍 에러가 발생한 경우 `window.onerror`만으로 스크립트를 복구하는 건 사실상 불가능하다. `window.onerror`는 개발자에게 에러 메시지를 보내는 용도로 사용한다.

`window.onerror`말고  [https://errorception.com](https://errorception.com/)  나  [http://www.muscula.com](http://www.muscula.com/) 같은 에러 로깅 관련 상용 서비스가 여러 가지 있다.

이런 서비스들은 다음과 같은 프로세스로 동작한다.
1.  서비스를 가입하면 자바스크립트 파일(혹은 스크립트 url)을 받는데, 개발자는 이 파일을 페이지에 삽입한다.
2.  받은 파일은 커스텀  `window.onerror`  함수를 설정한다.
3.  에러가 발생하면, 이 커스텀 함수가 에러에 관한 내용을 담아 서비스에 네트워크 요청을 보낸다.
4.  개발자는 서비스 사이트에 로그인해 기록된 에러를 확인한다.

## 요약
```js
try {
  // 이곳의 코드를 실행한다.
} catch(err) {
  // 에러가 발생하면, 여기부터 실행된다.
  // err는 에러 객체이다.
} finally {
  // 에러 발생 여부와 상관없이 try/catch 이후에 실행된다.
}
```
`throw` 연산자를 사용하면 에러를 직접 만들 수 있다. 보통 `throw` 인수에는 내장 `Error` 클래스를 상속받은 에러 객체를 인수에 넘긴다.

**다시 던지기**는 에러 처리 시 사용되는 중요한 패턴입니다. `catch` 블록에서는 처리 가능한 에러만 다루고, 예상치 못한 에러는 다시 던지기한다.

`try..catch`가 없어도 대부분의 호스트 환경이 **전역** 에러 핸들러를 지원하기 때문에 떨어져 나온 에러를 잡을 수 있다. `window.onerror`는 브라우저 환경의 전역 에러 핸들러이다.
