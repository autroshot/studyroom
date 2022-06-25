# Date 객체와 날짜
날짜를 저장할 수 있고, 날짜와 관련된 메서드를 제공하는 내장 객체 [Date](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date)에 대해 알아볼 것이다.

## 객체 생성하기
`new Date()`를 호출하면 새로운 `Date` 객체가 만들어지는데, `new Date()`는 다음과 같이 다양한 형태로 호출할 수 있다.

### new Date()
인수 없이 호출하면 현재 날짜와 시간이 저장된 `Date` 객체가 반환된다.
```js
let now = new Date();
alert(now); // 현재 날짜 및 시간이 출력된다.
```

### new Date(milliseconds)
UTC 기준(UTC+0) 1970년 1월 1일 0시 0분 0초에서 `milliseconds` 밀리초(1/1000초) 후의 시점이 저장된 `Date` 객체가 반환된다.
 ```js run
// 1970년 1월 1일 0시 0분 0초(UTC+0)를 나타내는 객체이다.
let Jan01_1970 = new Date(0);
alert(Jan01_1970);

// 1970년 1월 1일의 24시간 후는 1970년 1월 2일(UTC+0)이다.
let Jan02_1970 = new Date(24 * 3600 * 1000);
alert(Jan02_1970);
```

1970년의 첫날을 기준으로 흘러간 밀리초를 나타내는 정수를 <strong>타임스탬프(timestamp)</strong>라고 부른다.

타임스탬프를 사용하면 날짜를 숫자 형태로 간편하게 나타낼 수 있다. `new Date(timestamp)`를 사용하면 특정 날짜가 저장된 `Date` 객체를 손쉽게 만들 수 있고, `date.getTime()` 메서드를 사용하면 `Date` 객체에서 타임스탬프를 추출하는 것도 가능하다.

1970년 1월 1일 이전 날짜에 해당하는 타임스탬프 값은 음수로 나타낸다.
```js
// 31 Dec 1969
let Dec31_1969 = new Date(-24 * 3600 * 1000);
alert(Dec31_1969);
```

### new Date(datestring)
인수가 하나이고 문자열이라면 해당 문자열은 자동으로 구문 분석(parsed)된다. 구문 분석에 적용되는 알고리즘은 아래 [Date.parse](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95.md#dateparse%EC%99%80-%EB%AC%B8%EC%9E%90%EC%97%B4)에서 사용하는 것과 동일하다.

### new Date(year, month, date, hours, minutes, seconds, ms)
주어진 인수를 조합해 만들 수 있는 날짜가 저장된 객체가 반환된다(지역 시간대 기준). 첫 번째와 두 번째 인수만 필숫값이다.
-   `year`는 반드시 네 자리 숫자여야 한다.  `2013`은 가능하고 `98`은 안 된다.
-   `month`는  `0`(1월)부터  `11`(12월) 사이의 숫자여야 한다.
-   `date`는 일을 나타내는데, 값이 없으면 `1`일로 처리된다.
-   `hours/minutes/seconds/ms`는 값이 없으면  `0`으로 처리된다.
```js
new Date(2011, 0, 1, 0, 0, 0, 0); // 2011년 1월 1일, 00시 00분 00초
new Date(2011, 0, 1); // hours부터는 기본값인 0이 되므로 위와 동일하다.
```
최소 정밀도는 1밀리초(1/1000초)이다.
```js
let date = new Date(2011, 0, 1, 2, 3, 4, 567);
alert(date); // 2011년 1월 1일, 02시 03분 04.567초
```

## 날짜 구성요소 얻기
`Date` 객체의 메서드를 이용하면 연, 월, 일 등의 값을 얻을 수 있다.

[getFullYear()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getFullYear)  
연도(네 자릿수)를 반환한다.

[getMonth()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getMonth)  
월을 반환한다(**0 이상 11 이하**).

[getDate()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getDate)  
일을 반환한다(1 이상 31 이하). 메서드 이름을 주의해야 한다.

[getHours()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getHours),  [getMinutes()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getMinutes),  [getSeconds()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getSeconds),  [getMilliseconds()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getMilliseconds)  
시, 분, 초, 밀리초를 반환한다.

---
:warning: **`getYear()` 대신 `getFullYear()`를 사용해야 한다.**

여러 자바스크립트 엔진이 더는 사용되지 않는(deprecated) 비표준 메서드 `getYear()`을 구현하고 있다. 이 메서드는 두 자릿수 연도를 반환하는 경우가 있기 때문에 절대 사용해서는 안 된다. 연도 정보를 얻고 싶다면 `getFullYear()`를 사용해야 한다.

---

[getDay()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getDay)  
일요일을 나타내는  `0`부터 토요일을 나타내는  `6`까지의 숫자 중 하나를 반환한다. 몇몇 나라에서 요일의 첫날이 일요일이 아니지만, **getDay에서는 항상  `0`이 일요일을 나타낸다.**

**위에서 소개한 메서드는 모두 현지 시간을 기준으로 날짜 구성요소를 반환한다.**

메서드 이름의 ‘get’ 다음에 'UTC’를 붙여주면, 표준시(UTC+0) 기준의 날짜 구성 요소를 반환해주는 메서드 [getUTCFullYear()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCFullYear), [getUTCMonth()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCMonth), [getUTCDay()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCDay)를 만들 수 있다.

아래 두 메서드는 위에서 소개한 메서드와 달리 표준시(UTC+0) 기준의 날짜 구성 요소를 반환해주는 메서드가 없다.

[getTime()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getTime)  
주어진 일시와 1970년 1월 1일 00시 00분 00초 사이의 간격인 타임스탬프(밀리초)를 반환한다.

[getTimezoneOffset()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getTimezoneOffset)  
현지 시간과 표준 시간의 차이(분)를 반환한다.
```js
// UTC-1 시간대에서 이 예시를 실행하면 60이 출력된다.
// UTC+3 시간대에서 이 예시를 실행하면 -180이 출력된다.
alert(new Date().getTimezoneOffset());
```

## 날짜 구성요소 설정하기
-   [`setFullYear(year, [month], [date])`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/setFullYear)
-   [`setMonth(month, [date])`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/setMonth)
-   [`setDate(date)`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/setDate)
-   [`setHours(hour, [min], [sec], [ms])`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/setHours)
-   [`setMinutes(min, [sec], [ms])`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/setMinutes)
-   [`setSeconds(sec, [ms])`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/setSeconds)
-   [`setMilliseconds(ms)`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/setMilliseconds)
-   [`setTime(milliseconds)`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/setTime)(1970년 1월 1일 00:00:00 UTC부터 경과된 밀리초 단위의 날짜를 설정한다)

`setTime()`을 제외한 모든 메서드는  `setUTCHours()`같이 표준시에 따라 날짜 구성 요소를 설정해주는 메서드가 있다.

`setHours`와 같은 메서드는 여러 개의 날짜 구성요소를 동시에 설정할 수 있는데, 메서드의 인수에 없는 구성요소는 변경되지 않는다.
```js
let today = new Date();

today.setHours(0);
alert(today); // 날짜는 변경되지 않고 시만 0으로 변경된다.

today.setHours(0, 0, 0, 0);
alert(today); // 날짜는 변경되지 않고 시, 분, 초가 모두 변경된다(00시 00분 00초).
```

## 자동 고침
`Date` 객체에는 <strong>자동 고침(autocorrection)</strong>이라는 유용한 기능이 있다. 범위를 벗어나는 값을 설정하려고 하면 자동 고침 기능이 활성화되면서 값이 자동으로 수정된다.
```js
let date = new Date(2013, 0, 32); // 2013년 1월 32일은 존재하지 않는다.
alert(date); // 2013년 2월 1일이 출력된다.
```
입력받은 날짜 구성 요소가 범위를 벗어나면 초과분은 자동으로 다른 날짜 구성 요소에 배분된다.

'2016년 2월 28일’의 이틀 뒤 날짜를 구하고 싶다면 단순히 이틀을 더해주기만 하면 된다. 윤년 계산을 비롯한 나머지 작업은 `Date` 객체가 알아서 처리해 준다.
```js
let date = new Date(2016, 1, 28);
date.setDate(date.getDate() + 2);

alert(date); // 2016년 3월 1일
```
```js
let date = new Date();
date.setSeconds(date.getSeconds() + 70);

alert(date); // 70초 후의 날짜가 출력된다.
```
날짜 구성 요소에 0이나 음수를 설정하는 것도 가능하다.
```js
let date = new Date(2016, 0, 2); // 2016년 1월 2일

date.setDate(1); // 1일로 변경한다.
alert(date); // 01 Jan 2016

date.setDate(0); // 일의 최솟값은 1이므로 0을 입력하면 자동으로 이전 달의 마지막 날이 된다.
alert( date ); // 31 Dec 2015
```

## Date 객체를 숫자로 변경해 시간차 측정하기
`Date` 객체를 숫자형으로 변경하면 타임스탬프가 된다.
```js
let date = new Date();
alert(+date); // 타임스탬프(date.getTime()을 호출한 것과 동일하다)
```
이를 응용하면 날짜에 마이너스 연산자를 적용해 밀리초 기준 시차를 구할 수 있다.

다음은 스톱워치를 구현한 예시이다.
```js
let start = new Date(); // 측정 시작

// 원하는 작업을 수행한다.
for (let i = 0; i < 100000; i++) {
  let doSomething = i * i * i;
}

let end = new Date(); // 측정 종료

alert(`반복문을 모두 도는데 ${end - start} 밀리초가 걸렸습니다.`);
```

## Date.now()
시차를 측정하는 또 하나의 방법은 타임스탬프를 반환하는 메서드 `Date.now()`를 응용하는 것이다.

`Date.now()`는 `new Date().getTime()`과 의미는 동일하지만 중간에 `Date` 객체를 만들지 않는다는 점이 다르다. 따라서 `new Date().getTime()`를 사용하는 것보다 빠르고 가비지 컬렉터의 일을 덜어준다는 장점이 있다.

스톱워치 예시에 `Date.now()`를 사용하면 성능이 더 좋아진다.
```js
let start = Date.now(); // 1970년 1월 1일부터 현재까지 경과한 밀리초

// 원하는 작업을 수행한다.
for (let i = 0; i < 100000; i++) {
  let doSomething = i * i * i;
}

let end = Date.now(); // done

alert(`반복문을 모두 도는데 ${end - start} 밀리초가 걸렸습니다.`); // Date 객체가 아닌 숫자끼리 차감한다.
```

## 벤치마크 테스트
비슷한 기능을 수행하는 함수들 중 어느 것이 더 빠른지 알고 싶을 때 <strong>벤치마크(benchmark) 테스트</strong>를 시행한다.

신뢰할만한 벤치마크 테스트를 만들려면  `benchmark`를 번갈아 가면서 여러 번 돌려야 한다.
```js
function diffSubtract(date1, date2) {
  return date2 - date1;
}

function diffGetTime(date1, date2) {
  return date2.getTime() - date1.getTime();
}

function bench(f) {
  let date1 = new Date(0);
  let date2 = new Date();

  let start = Date.now();
  for (let i = 0; i < 100000; i++) f(date1, date2);
  return Date.now() - start;
}

let time1 = 0;
let time2 = 0;

// 함수 bench를 각 함수(diffSubtract, diffGetTime)별로 10번씩 시행한다.
for (let i = 0; i < 10; i++) {
  time1 += bench(diffSubtract);
  time2 += bench(diffGetTime);
}

alert('diffSubtract에 소모된 시간: ' + time1);
alert('diffGetTime에 소모된 시간: ' + time2);
```

모던 자바스크립트 엔진은 아주 많이 실행된 코드인 'hot code’를 대상으로 최적화를 수행한다. 위 예시에서 bench를 처음 실행했을 때는 최적화가 잘 되지 않은 상태이다. 따라서 메인 반복문을 실행하기 전에 예열용(heat-up)으로 bench를 실행할 수 있다.
```js
// 메인 반복문 실행 전, '예열용'으로 추가한 코드이다.
bench(diffSubtract);
bench(diffGetTime);

// 벤치마크 테스트 시작
for (let i = 0; i < 10; i++) {
  time1 += bench(diffSubtract);
  time2 += bench(diffGetTime);
}
```

## Date.parse와 문자열
메서드 [Date.parse(str)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/parse)를 사용하면 문자열에서 날짜를 읽을 수 있다.

단, 읽는 문자열의 형식은 `YYYY-MM-DDTHH:mm:ss.sssZ`이어야 한다.
-   `YYYY-MM-DD`: 날짜(연-월-일)
-   `"T"`: 구분 기호로 쓰인다.
-   `HH:mm:ss.sss`: 시:분:초.밀리초
-   `'Z'`(옵션): `+-hh:mm`  형식의 시간대를 나타낸다.  `Z`  한 글자인 경우에는 UTC+0을 나타낸다.

`YYYY-MM-DD`, `YYYY-MM`, `YYYY`같이 더 짧은 문자열 형식도 가능하다.

위 조건을 만족하는 문자열을 대상으로 `Date.parse(str)`를 사용하면 타임스탬프가 반환된다. 문자열의 형식이 조건에 맞지 않은 경우에는 `NaN`이 반환된다.
```js
let ms = Date.parse('2012-01-26T13:51:50.417-07:00');

alert(ms); // 1327611110417(타임스탬프)
```
