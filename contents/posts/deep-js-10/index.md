---
title: "10. 객체 리터럴 (Object Literal)"
description:
date: 2024-08-07
update: 2024-08-07
tags:
  - Javascript
series: "모던 자바스크립트 Deep Dive"
---

### 10.1 객체란?

---

> 자바스크립트는 `객체(object)` 기반의 프로그래밍 언어

- [원시값(primitive value)](https://developer.mozilla.org/ko/docs/Glossary/Primitive) 을 제외한 나머지 값( 함수, 배열, 정규 표현식 등 )은 모두 객체
- 원시 타입은 하나의 값만 나타내지만 `객체 타입(object / reference type)` 은 `다양한 타입의 값(원시 값 또는 다른 객체)`을 하나의 단위로 구성
- 원시값은 `변경 불가(immutable)` 한 값이지만, 객체는 `변경 가능(mutable)` 한 값
- 객체는 0개 이상의 `프로퍼티(property)` 로 구성된 집합이며, 프로퍼티는 `키(key) : 값(value)` 로 구성
- 자바스크립트에서 `함수(function)` 도 프로퍼티의 값으로 설정가능 → `메서드(method)`

```cs
var counter = {
	num: 0,  // 프로퍼티
  increase: function () { ... )  // 메서드
}
```

```s
[ 💡 NOTE ]

프로퍼티(property) = 객체의 상태를 나타내는 값(data)
메서드(method) = 프로퍼티(상태 데이터)를 참조하고 조작할 수 있는 동작(behavior)
```

### 10.2 객체 리터럴에 의한 객체 생성

---

1. 객체지향 언어(ex. C++, Java)는 클래스를 사전에 정의한다.
2. 필요한 시점에 new 연산자와 함께 생성자(constructor)를 호출하여 인스턴스를 생성하는 방식으로 객체를 생성한다.

```s
[ 💡 인스턴스 ]
+ 클래스에 의해 생성되어 메모릴에 저장된 실체
+ 객체지향 프로그래밍에서 객체는 클래스와 인스턴스를 포함한 개념.
+ 클래스는 인스턴스를 생성하기 위한 템플릿의 역할을 한다.
+ 인스턴스는 객체가 메모리에 저장되어 실제로 존재하는 것에 초점을 맞춘 용어
```

> 자바스크립트는 프로토타입 기반 객체지향 언어
> 자바와 같은 클래스 기반 객체지향 언어와는 달리 다양한 객체 생성 방법을 지원한다.

- `객체 리터럴`
- `Object 생성자 함수`
- `생성자 함수`
- `Object.create 메서드`
- `클래스(ES6)`

> 객체리터럴: 중괄호({...})내에 0개 이상의 프로퍼티를 정의한다.
> 변수에 할당되는 시점에 자바스크립트 엔진은 객체 리터럴을 해석해 객체를 생성한다

- 자바스크립트의 유연함과 강력함을 대표하는 객체 생성 방식
- 객체를 생성하기 위해 클래스를 먼저 정의하고 new 연산자와 함께 생성자를 호출할 필요가 없다.

### 10. 3 프로퍼티

---

> 객체는 프로퍼티의 집합, 프로퍼티는 키와 값으로 구성된다.

```cs
var person = {
  // 프로퍼티 키는 name, 프로퍼티 값은 'Lee'
  name: 'Lee',
  age: 20
}
```

- 프로퍼티 `키(key)` : 빈 문자열( '' ) 을 포함하는 모든 `문자열(string)` 또는 `심벌(symbol)` 값
- 프로퍼티 `값(value)` : 자바스크립트에서 사용할 수 있는 모든 값

> 프로퍼티 키는 프로퍼티 값에 접근할 수 있는 이름으로서 `식별자 역할`

- 하지만 반드시 식별자 네이밍 규칙 을 따라야 하는 것은 아니다.
- 자바스크립트에서 사용 가능한 유요한 이름인 경우, 따옴표( '' or "") 를 생략할 수 있다.
- 반대로 말하면 식별자 네이밍 규칙을 따르지 않는 이름에는 반드시 따옴표를 사용해야 한다.

```cs
var person = {
	firstName: 'Ung-mo',  // 식별자 네이밍 규칙을 준수한 프로퍼티 키
	'last-name': 'Lee',       // 식별자 네이밍 규칙을 준수하지 않은 프로퍼티 키 ( 따옴표를 사용해 문자열 형태 유지 )
  last-name: 'Lee'          // SyntaxError: Unexpected token ( 식별자 네이밍 규칙을 준수하지 않은 프로퍼티 키 ( 따옴표를 사용하지 않을 경우 - 표현식으로 해석 ) )
};
```

- 문자열 또는 문자열로 평가할 수 있는 표현식을 사용해 프로퍼티 키를 동적으로 생성할 수도 있다.
- 이 경우에는 프로퍼티 키로 사용할 표현식을 대괄호([...])로 묶어야 한다.

```cs
var obj = {};
var key = 'hello';

// ES5: 프로퍼티 키 동적 생성
obj[key] = 'hello';
// ES6: 계산된 프로퍼티 이름
// var obj = {[key]:'world'};

console.log(obj); // {hello: 'world'}
```

- 프로퍼티에 문자열이나 심벌 값 외의 값을 사용하면 암묵적 타입 변환 을 통해 문자열이 된다.

```cs
var foo = {
  0: 1,
  1: 2,
  2: 3,
};

console.log(foo); // { '0': 1, '1': 2, '2': 3 } << 키값이 문자열 형태로 암묵적 타입 변환

```

- var, function같은 예약어를 프로퍼티 키로 사용해서 에러가 발생하지는 않는다.
- 하지만 예상치못한 에러가 발생할 여지가 있으므로 권장하지 않는다.

- 이미 존재하는 프로퍼티 키를 중복 선언하면 나중에 선언한 프로퍼티가 먼저 선언한 프로퍼티를 덮어쓴다. ( 에러 발생하지 않음 )

```cs
var foo = {
  name: 'Lee',
  name: 'Kim
}

console.log(foo); // {name: 'Kim'}
```

### 10.4 메서드

---

- 프로퍼티 값이 함수일 경우 일반함수와 구분하기 위해 method라 부른다.

### 10.5 프로퍼티 접근

---

- 마침표(.) 프로퍼티 접근 연산자를 사용하는 방법 -> 마침표 표기법 (dot notation)
- 대괄호([...]) 프로퍼티 접근 연산자를 사용하는 방법 -> 대괄호 표기법(bracket notation)`

```cs
console.log(person.name); // "WI" ( 마침표 표기법 )
console.log(person["name"]); // "WI" ( 대괄호 표기법 )

// 💩 대괄호 프로퍼티 접근 연산자 내에 문자열 형태가 아닌 프로퍼티 키로 사용하면 자바스크립트 엔진은 "식별자"로 해석
console.log(person[name]); // ReferenceError: name is not defined

// 💩 객체에 존재하지 않는 프로퍼티에 접근시 -> undefined
console.log(person.age); // undefined

```

- 프로퍼티 키가 식별자 네이밍 규칙을 준수하지 않는 이름이면, 반드시 대괄호 표기법을 사용해야 한다.

```c
person.'last-name'; // SyntaxError: Unexpected string
person.last-name; // 브라우저 환경: NaN
                  // node.js 환경: ReferenceError: name is not defined
person[last-name]; // referenceError: last is not defined
person['last-name']; // Lee
```

- 프로퍼티 키가 숫자로 이뤄진 문자열인 경우 따옴표를 생략할 수 있다.

```c
[ 💡 Note ]
브라우저환경에는 전역객체 window의 프로퍼티 name이 있다.
때문에 Node.js 환경과 브라우저내의 에러가 다르다.
```

### 10.6 ~ 10.8 프로퍼티 동적 생성 & 삭제

---

```cs
var person = {
  name: "Lee",
};

person.name = 'Kim';
// person 객체에 Name 프로퍼티가 존재하므로 name 프로퍼티의 값이 갱신된다.
console.log(person); // { name: 'Kim' }

person.age = 20;
// age프로퍼티가 존재하지 않는다.
// age 프로퍼티가 동적으로 생성되고 값이 할당된다.

delete person.age;
// age 라는 프로퍼티 키가 있고 -> 해당 프로퍼티 삭제
delete person.job;
// job 이라는 프로퍼티 키는 없음 -> 에러는 발생하지 않음

```

### 10.9 ES6에서 추가된 객체 리터럴의 확장 기능

---

### 프로퍼티 축약표현

- `ES5`

```cs
var x = 1, y = 2;

var obj = {
  x: x,
  y: y
}

console.log(obj); // {x: 1, y:2}
```

- `ES6`

```cs
let x = 1, y = 2;

// 프로퍼티 축약 표현
const obj = { x, y };

console.log(obj); {x:1, y:2}
```

### 계산된 프로퍼티 이름

- `ES5`

```cs
var prefix = "prop";
var i = 0;

var obj = {};

// "계산된 프로퍼티 이름"으로 프로퍼티 키 동적 생성
obj[prefix + "-" + ++i] = i;
obj[prefix + "-" + ++i] = i;
obj[prefix + "-" + ++i] = i;

console.log(obj); // { 'prop-1': 1, 'prop-2': 2, 'prop-3': 3 }
```

- `ES6`

```cs
const prefix = "prop";
let i = 0;

// "객체 리터럴 내부"에서 계산된 프로퍼티 이름으로 프로퍼티 키를 동적 생성
const obj = {
  [`${prefix}-${++i}`]: i,
  [`${prefix}-${++i}`]: i,
  [`${prefix}-${++i}`]: i,
};

console.log(obj); // { 'prop-1': 1, 'prop-2': 2, 'prop-3': 3 }

```

### 메서드 축약 표현

- `ES5`

```cs
var obj = {
  name: "Lee",

  // 프로퍼티 값으로 "함수를 할당"
  sayHi: function () {
    console.log(`Hi! ${this.name}`);
  },
};

obj.sayHi(); // Hi! Lee
```

- `ES6`
  - 단, 메서드 축약 표현 으로 정의한 메서드는 프로퍼티에 할당한 함수(ES5) 와 다르게 동작한다.

```cs
const obj = {
  name: "Lee",

  // 메서드 축약 표현 ( 함수 선언식 필요 X )
  sayHi() {
    console.log(`Hi! ${this.name}`);
  },
};

obj.sayHi(); // Hi! Lee

```
