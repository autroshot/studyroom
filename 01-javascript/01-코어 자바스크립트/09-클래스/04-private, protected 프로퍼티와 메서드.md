# private, protected 프로퍼티와 메서드
객체 지향 프로그래밍에서 가장 중요한 원리 중 하나는 ***내부 인터페이스와 외부 인터페이스를 구분 짓는 것***이다.

프로그래밍에서 객체는 커피 머신과 같다. 프로그래밍에서는 보호 커버를 사용하는 대신 특별한 문법과 컨벤션을 사용해 안쪽 세부 사항을 숨긴다.

## 내부 인터페이스와 외부 인터페이스
객체 지향 프로그래밍에서 프로퍼티와 메서드는 두 그룹으로 분류된다.
- <strong>내부 인터페이스(internal interface)</strong>: 동일한 클래스 내의 다른 메서드에서는 접근할 수 있지만, 클래스 밖에서는 접근할 수 없는 프로퍼티와 메서드이다.
- <strong>외부 인터페이스(external interface)</strong>: 클래스 밖에서도 접근 가능한 프로퍼티와 메서드이다.

자바스크립트에는 아래와 같은 두 가지 타입의 객체 필드(프로퍼티와 메서드)가 있다.
-   **public**: 어디서든지 접근할 수 있으며 외부 인터페이스를 구성한다. 지금까지 다룬 프로퍼티와 메서드는 모두 public이다.
-   **private**: 클래스 내부에서만 접근할 수 있으며 내부 인터페이스를 구성할 때 쓰인다.

자바스크립트 이외의 다수 언어에서는 클래스 자신과 자손 클래스에서만 접근을 허용하는 ‘protected’ 필드를 지원한다. 자손 클래스의 필드에 접근해야 하는 경우가 많기 때문에, **protected 필드는 private 필드보다 조금 더 광범위하게 사용된다.**

자바스크립트는 protected 필드를 지원하지 않지만, protected를 사용하면 편리한 점이 많기 때문에 이를 모방해서 사용하는 경우가 많다.

## 프로퍼티 보호하기
간단한 커피 머신 클래스를 만들어 보겠다.
```js
class CoffeeMachine {
  waterAmount = 0; // 물통에 차 있는 물의 양

  constructor(power) {
    this.power = power;
    alert(`전력량이 ${power}인 커피머신을 만듭니다.`);
  }
}

// 커피 머신을 생성한다.
let coffeeMachine = new CoffeeMachine(100);

// 물을 추가한다.
coffeeMachine.waterAmount = 200;
```
현재 프로퍼티 `waterAmount`와 `power`는 public이다. 두 프로퍼티는 쉽게 읽고 수정할 수 있다.

**protected 프로퍼티 이름 앞에는 밑줄  `_`이 붙는다.**

자바스크립트에서 강제한 사항은 아니지만, 밑줄은 프로그래머 사이에서 외부 접근이 불가능한 프로퍼티나 메서드를 나타낼 때 쓴다.

이제 `waterAmount`를 protected로 바꿔서 `waterAmount`를 통제해 보겠다.
```js
class CoffeeMachine {
  _waterAmount = 0;

  set waterAmount(value) {
    if (value < 0) throw new Error('물의 양은 음수가 될 수 없습니다.');
    this._waterAmount = value;
  }

  get waterAmount() {
    return this._waterAmount;
  }

  constructor(power) {
    this._power = power;
  }

}

// 커피 머신을 생성한다.
let coffeeMachine = new CoffeeMachine(100);

// 물을 추가한다.
coffeeMachine.waterAmount = -10; // Error: 물의 양은 음수가 될 수 없습니다.
```

## 읽기 전용 프로퍼티
프로퍼티를 생성할 때만 값을 할당할 수 있고, 그 이후에는 값을 절대 수정하지 말아야 할 때 읽기 전용 프로퍼티를 사용하면 된다.

읽기 전용 프로퍼티를 만들려면 setter(설정자)는 만들지 않고 getter(획득자)만 만들어야 한다.
```js
class CoffeeMachine {
  // ...

  constructor(power) {
    this._power = power;
  }

  get power() {
    return this._power;
  }
}

// 커피 머신을 생성한다.
let coffeeMachine = new CoffeeMachine(100);

alert(`전력량이 ${coffeeMachine.power}인 커피머신을 만듭니다.`); // 전력량이 100인 커피머신을 만든다.

coffeeMachine.power = 25; // Error (setter가 없다)
```
---
:information_source: **getter와 setter 함수**

위에서는 get, set 문법을 사용해서 getter와 setter 함수를 만들었다.

하지만 대부분은 아래와 같이  `get.../set...`  형식의 함수가 선호된다.
```js
class CoffeeMachine {
  _waterAmount = 0;

  setWaterAmount(value) {
    if (value < 0) throw new Error('물의 양은 음수가 될 수 없습니다.');
    this._waterAmount = value;
  }

  getWaterAmount() {
    return this._waterAmount;
  }
}

new CoffeeMachine().setWaterAmount(100);
```
이렇게 함수를 선언하면 다수의 인자를 받을 수 있기 때문에 좀 더 유연하다.  반면 get, set 문법을 사용하면 코드가 짧아진다는 장점이 있다. 

어떤 것을 사용해야 한다는 규칙은 없으므로 원하는 방식을 선택해서 사용한다.

---
:information_source: **protected 필드는 상속된다.**

protected 필드는 아래에서 보게 될 private 필드와 달리 상속된다.

---

## private 프로퍼티
---
:warning: 최근에 추가됨

스펙에 추가된 지 얼마 안 된 문법이다. 이 문법을 지원하지 않거나 부분적으로만 지원하는 엔진을 사용 중이라면 폴리필을 구현해야 한다.

---
private 프로퍼티와 메서드는 `#`으로 시작한다. `#`이 붙으면 클래스 안에서만 접근할 수 있다.

물 용량 한도를 나타내는 private 프로퍼티 `#waterLimit`과 남아있는 물의 양을 확인해주는 private 메서드 `#checkWater`를 구현해 보겠다.
```js
class CoffeeMachine {
  #waterLimit = 200;

  #checkWater(value) {
    if (value < 0) throw new Error('물의 양은 음수가 될 수 없습니다.');
    if (value > this.#waterLimit) throw new Error('물이 용량을 초과합니다.');
  }
}

let coffeeMachine = new CoffeeMachine();

// 클래스 외부에서 private에 접근할 수 없다.
coffeeMachine.#checkWater(); // Error
coffeeMachine.#waterLimit = 1000; // Error
```
`#`은 자바스크립트에서 지원하는 문법으로 private 필드를 의미한다. private 필드는 클래스 외부나 자손 클래스에서 접근할 수 없다.

private 필드는 public 필드와 충돌하지 않는다. private 프로퍼티 `#waterAmount`와 public 프로퍼티 `waterAmount`를 동시에 가질 수 있다.

`#waterAmount`의 접근자 `waterAmount`를 만들어 보겠다.
```js
class CoffeeMachine {
  #waterAmount = 0;

  get waterAmount() {
    return this.#waterAmount;
  }

  set waterAmount(value) {
    if (value < 0) throw new Error('물의 양은 음수가 될 수 없습니다.');
    this.#waterAmount = value;
  }
}

let machine = new CoffeeMachine();

machine.waterAmount = 100;
alert(machine.#waterAmount); // Error
```
protected 필드와 달리, private 필드는 언어 자체에 의해 강제된다는 점이 장점이다.

그런데  `CoffeeMachine`을 상속받는 클래스에서는  `#waterAmount`에 직접 접근할 수 없다.  `#waterAmount`에 접근하려면  `waterAmount`의 getter와 setter를 통해야 한다.
```js
class MegaCoffeeMachine extends CoffeeMachine {
  method() {
    alert(this.#waterAmount); // Error: CoffeeMachine을 통해서만 접근할 수 있다.
  }
}
```
다양한 상황에서 이런 제약사항은 너무 엄격하다. `CoffeeMachine`을 상속받는 클래스에서는 `CoffeeMachine`의 내부에 접근해야 하는 정당한 사유가 있을 수 있다. 언어 차원에서 protected 필드를 지원하지 않아도 더 자주 쓰이는 이유가 바로 여기에 있다.

---
:warning: **private 필드는 this[name]로 사용할 수 없다.**

private 필드는 특별하다.

알다시피 보통은 `this[name]`을 사용해 필드에 접근할 수 있다.
```js
class User {
  ...
  sayHi() {
    let fieldName = 'name';
    alert(`Hello, ${this[fieldName]}`);
  }
}
```
하지만 private 필드는 `this[name]`으로 접근할 수 없다. 이런 문법적 제약은 필드의 보안을 강화하기 위해 만들어졌다.

---

## 요약
객체 지향 프로그래밍에선 내부 인터페이스와 외부 인터페이스를 구분하는 것을 <strong>캡슐화(encapsulation)</strong>라는 용어를 사용해 설명한다.

캡슐화는 이점은 다음과 같다.
### 사용자가 자신의 발등을 찍지 않도록 보호한다.
외부에서 의도치 않게 클래스를 조작하게 되면 그 결과는 예측할 수 없게 된다.

### 지원 가능
내부 인터페이스를 엄격하게 구분하면, 클래스 개발자들은 사용자에게 알리지 않고도 자유롭게 내부 프로퍼티와 메서드들을 수정할 수 있다.

### 복잡성 은닉
구현 세부 사항이 숨겨져 있으면 간단하고 편리해진다. 외부 인터페이스에 대한 설명도 문서화하기 쉬워진다.

내부 인터페이스를 숨기려면 protected나 private 프로퍼티를 사용하면 된다.
-   protected 필드는  `_`로 시작한다.  `_`은 자바스크립트에서 지원하는 문법은 아니지만 protected 필드를 나타낼 때 관습처럼 사용된다. 개발자는 protected 프로퍼티가 정의된 클래스와 해당 클래스를 상속받는 클래스에서만  `_`가 붙은 필드에 접근해야 한다.
-   private 필드는  `#`로 시작하며 자바스크립트에서 지원하는 문법이다.  `#`로 시작하는 필드는 해당 필드가 정의된 클래스 내부에서만 접근 가능하다.
