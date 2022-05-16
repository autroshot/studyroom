# alert, prompt, confirm을 이용한 상호작용
## alert
`alert` 함수는 메시지를 보여주는 모달 창을 띄운다.
```js
alert('Hello');
```

## prompt
`prompt` 함수는 입력 필드가 있는 모달 창을 띄운다. 사용자가 확인 버튼을 누르면 함수는 입력 필드의 문자열을, 그 외의 경우는 `null`을 반환한다.
```js
result = prompt(title, [default]);
```
```js
let age = prompt('나이를 입력해주세요.', 100);

alert(`당신의 나이는 ${age}살 입니다.`); // 당신의 나이는 100살입니다.
```

## confirm
`confirm` 함수는 사용자가 모달 창의 확인 버튼을 누르면 `true`를, 그 외의 경우는 `false`를 반환한다.
```js
result = confirm(question);
```
