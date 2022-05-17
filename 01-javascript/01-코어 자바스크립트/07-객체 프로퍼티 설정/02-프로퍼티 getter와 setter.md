# 프로퍼티 getter와 setter
객체의 프로퍼티는 두 종류로 나뉜다.

1. <strong>데이터 프로퍼티(data property)</strong>: 지금까지 사용한 모든 프로퍼티는 데이터 프로퍼티이다. 
2. <strong>접근자 프로퍼티(accessor property)</strong>: 접근자 프로퍼티의 본질은 함수인데, 이 함수는 값을 획득(get)하고 설정(set)하는 역할을 담당한다. 그런데 외부 코드에서는 함수가 아닌 일반적인 프로퍼티처럼 보인다.

## getter와 setter
접근자 프로퍼티는 'getter(획득자)'와 ‘setter(설정자)’ 메서드로 표현된다. 객체 리터럴 안에서 getter와 setter 메서드는 `get`과 `set`으로 나타낼 수 있다.
```js
let obj = {
  get propName() {
    // getter, obj.propName을 실행할 때 실행되는 코드이다.
  },

  set propName(value) {
    // setter, obj.propName = value를 실행할 때 실행되는 코드이다.
  }
};
```
`user` 객체에서 `fullName`에 대한 getter 메서드는 다음과 같이 구현할 수 있다.
```js
let user = {
  name: 'John',
  surname: 'Smith',

  get fullName() {
    return `${this.name} ${this.surname}`;
  }
};

alert(user.fullName); // John Smith
```
바깥 코드에서는 접근자 프로퍼티를 일반 프로퍼티처럼 사용할 수 있다. 접근자 프로퍼티를 사용하면 함수처럼 호출하지 않고, 일반 프로퍼티에서 값에 접근하는 것처럼 평범하게 `user.fullName`을 사용해 프로퍼티 값을 얻을 수 있다.

`user.fullName`에 setter 메서드를 추가하면 다음과 같다.
```js run
let user = {
  name: 'John',
  surname: 'Smith',

  get fullName() {
    return `${this.name} ${this.surname}`;
  },

  set fullName(value) {
    [this.name, this.surname] = value.split(' ');
  }
};

// 주어진 값을 사용해 set fullName이 실행된다.
user.fullName = 'Alice Cooper';

alert(user.name); // Alice
alert(user.surname); // Cooper
```
이렇게 getter와 setter 메서드를 구현하면 객체에는 `fullName`이라는 '가상’의 프로퍼티가 생긴다. 가상의 프로퍼티는 읽고 쓸 수 있지만 실제로는 존재하지 않는다.

## 접근자 프로퍼티 설명자
접근자 프로퍼티에는 설명자 `value`와 `writable`가 없는 대신에 `get`과 `set`이라는 함수가 있다.

접근자 프로퍼티는 다음과 같은 설명자를 갖는다.
-   `get`: 인수가 없는 함수로 프로퍼티를 읽을 때 동작한다.
-   `set`: 인수가 하나인 함수로 프로퍼티에 값을 쓸 때 호출된다.
-   `enumerable`: 데이터 프로퍼티와 동일하다.
-   `configurable`: 데이터 프로퍼티와 동일하다.

다음과 같이 `defineProperty`에 설명자 `get`과 `set`을 전달하면 `fullName`을 위한 접근자를 만들 수 있다.
```js
let user = {
  name: 'John',
  surname: 'Smith'
};

Object.defineProperty(user, 'fullName', {
  get() {
    return `${this.name} ${this.surname}`;
  },

  set(value) {
    [this.name, this.surname] = value.split(' ');
  }
});

alert(user.fullName); // John Smith

for(let key in user) alert(key); // name, surname
```
프로퍼티는 접근자 프로퍼티(`get/set` 메서드를 가짐)나 데이터 프로퍼티(`value`를 가짐) 중 한 종류에만 속할 수 있다는 것을 유의해야 한다.

한 프로퍼티에 `get`과 `value`를 동시에 설정하면 에러가 발생한다.
```js
// Error: Invalid property descriptor.
Object.defineProperty({}, 'prop', {
  get() {
    return 1
  },

  value: 2
});
```

## getter와 setter 똑똑하게 활용하기
getter와 setter를 ‘실제’ 프로퍼티 값을 감싸는 래퍼(wrapper)처럼 사용하면, 프로퍼티 값을 원하는 대로 통제할 수 있다.

다음 예시에서는 `name`을 위한 setter를 만들어 `user`의 이름이 너무 짧아지는 걸 방지하고 있다. 실제 값은 별도의 프로퍼티 `_name`에 저장된다.
```js
let user = {
  get name() {
    return this._name;
  },

  set name(value) {
    if (value.length < 4) {
      alert('입력하신 값이 너무 짧습니다. 네 글자 이상으로 구성된 이름을 입력하세요.');
      return;
    }
    this._name = value;
  }
};

user.name = 'Pete';
alert(user.name); // Pete

user.name = ""; // 너무 짧은 이름을 할당하려 한다.
```
기술적으로는 외부 코드에서 `user._name`을 사용해 이름에 바로 접근할 수 있다. 그러나 **밑줄 `_`로 시작하는 프로퍼티는 객체 내부에서만 활용하고, 외부에서는 건드리지 않는 것이 관습이다.**

## 호환성을 위해 사용하기
접근자 프로퍼티는 언제든 getter와 setter를 사용해 데이터 프로퍼티의 행동과 값을 원하는 대로 조정할 수 있게 해준다는 점에서 유용하다.

다음 예시에서는 데이터 프로퍼티 `name`과 `age`를 사용해서 사용자를 나타내는 객체를 구현한다.
```js
function User(name, age) {
  this.name = name;
  this.age = age;
}

let john = new User('John', 25);

alert( john.age ); // 25
```
그런데 요구사항이 바뀌어서 `age` 대신에 `birthday`를 저장해야 한다고 가정해 보자.
```js
function User(name, birthday) {
  this.name = name;
  this.birthday = birthday;
}

let john = new User('John', new Date(1992, 6, 1));
```
이렇게 생성자 함수를 수정하면 기존 코드 중 프로퍼티 `age`를 사용하고 있는 코드도 모두 수정해야 한다.

기존의 코드를 수정하는 대신 `age`를 위한 getter를 추가해서 문제를 해결할 수 있다.
```js
function User(name, birthday) {
  this.name = name;
  this.birthday = birthday;

  // age는 현재 날짜와 생일을 기준으로 계산된다.
  Object.defineProperty(this, 'age', {
    get() {
      let todayYear = new Date().getFullYear();
      return todayYear - this.birthday.getFullYear();
    }
  });
}

let john = new User('John', new Date(1992, 6, 1));

alert(john.birthday); // birthday를 사용할 수 있다.
alert(john.age);      // age 역시 사용할 수 있다.
```
