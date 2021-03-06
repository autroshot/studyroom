# Chrome으로 디버깅하기
디버깅(debugging)은 스크립트 내 에러를 검출해 제거하는 일련의 과정을 의미한다. 디버깅 툴은 Chrome 브라우저에서 제공하는 디버깅 툴을 이용한다.
- 중단점(breakpoint): 자바스크립트의 실행이 중단되는 코드 내 지점을 의미한다.
- 조건부 중단점(conditional breakpoint): 표현식을 작성할 수 있으며 해당 표현식이 참인 경우에만 실행을 중지시킬 수 있다. 변수에 특정 값이 할당될 때나 함수의 매개 변수에 특정 값이 들어올 때만 실행을 중단시킬 수 있어 디버깅 시 유용하게 활용할 수 있다.

스크립트 내에 `debugger` 명령어를 적어주면 중단점을 설정한 것과 같은 효과를 볼 수 있다.
```js
function hello(name) {
  let phrase = `Hello, ${name}!`;

  debugger;  // <-- 여기서 실행이 멈춘다.

  say(phrase);
}
```

스크립트 실행이 중단되는 경우는 다음과 같다.
1. 중단점을 만났을 때
2. `debugger`문을 만났을 때
3. 에러가 발생했을 때(개발자 도구가 열려있고 해당 기능이 활성화 되어있는 경우)

`console.log` 함수를 이용하면 원하는 것을 콘솔에 출력할 수 있다.
```js
// 콘솔창에서 결과를 확인할 수 있다.
for (let i = 0; i < 5; i++) {
  console.log("숫자", i);
}
```

크롬 디버깅 툴의 자세한 사용법은 다음 링크를 참조한다.
- [모던 JavaScript 튜토리얼](https://ko.javascript.info/debugging-chrome)
- [구글의 공식 매뉴얼](https://developers.google.com/web/tools/chrome-devtools)
