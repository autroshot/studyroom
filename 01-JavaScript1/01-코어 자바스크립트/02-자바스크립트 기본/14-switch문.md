# switch문
`switch`문은 하나 이상의 `case`문으로 구성되며 `default`문은 생략 가능하다.
```js
switch (x) {
  case 'value1':  // if (x === 'value1')
    ...
    [break]
    
  case 'value2':  // if (x === 'value2')
    ...
    [break]
    
  default:
    ...
    [break]
}
```
- 다중 `else if`문은 `switch`문으로 바꾸는 것이 가독성이 좋다.
- `case`문 안에 `break`문이 없으면 뒤의 `case`문도 실행된다. 이것을 이용해 여러 개의 `case`문을 묶는 것이 가능하다.
- `switch`문은 일치 비교(`===`)로 조건을 확인하기 때문에 자료형을 확인하는 것이 중요하다.
- `case`문이 추가될 것을 대비해 마지막 `case`문에도 `break`문을 작성하는 것이 좋다.
