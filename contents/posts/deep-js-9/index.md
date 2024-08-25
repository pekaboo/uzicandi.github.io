---
title: "9. 타입 변환과 단축 평가(Type Coercion)"
description:
date: 2024-08-06
update: 2024-08-06
tags:
  - Javascript
series: "모던 자바스크립트 Deep Dive"
---

### 9.1 타입 변환

---

> `명시적 타입변환(explicit coercion)` 또는 `타입 캐스팅(type casting)`: 개발자가 의도적으로 값의 타입을 변환하는 것

> `암묵적 타입 변환(implicit coercion)` 또는 `타입 강제 변환(type coercion)`: 개발자의 의도와는 상관없이 표현식을 평가하는 도중에 타입이 자동 변환

- 타입 변환이 기존 원시 값을 직접 변경하는 것은 아니다. 원시 값은 변경 불가능한 값(immutable value)이므로 변경할 수 없다.
- 타입 변환이란 기존 원시 값을 사용해 다른 타입의 새로운 원시 값을 생성하는 것이다.

### 9.2 암묵적 타입 변환

---

> 자바스크립트 엔진이 표현식을 평가할 때 개발자의 의도와는 상관없이 코드의 문맥을 고려해 암묵적으로 데이터 타입을 강제 변환(암묵적 타입 변환)하는 것

- 암묵적 타입 변환이 발생하면 → `문자열(string)`, `숫자(number)`, `불리언(boolean)` 과 같은 `원시 타입(primitive type)` 중 하나로 타입을 자동 변환

### 문자열 타입으로 변환

```c
// 🎯 주의할 것 중심

// 숫자 타입
NaN + '';             // "NaN"
Infinity + ''         // "Infinity"

// null 타입
null + '';            // "null"

// undefined 타입
undefined + '';       // "undefined"

// 심벌 타입
(Symbol()) + '';      // TypeError: Cannot convert a Symbol value to a string

// 객체 타입
({}) + '';            // "[object Object]"
Math + '';            // "[object Math]"
[] + '';              // ""
[10, 20] + '';        // "10,20"
(function(){}_ + '';  // "function(){}"
Array + '';           // "function Array() { [native code] }"
```

### 숫자 타입으로 변환

```c
// 문자열 타입
+""; // 0
+"0"; // 0
+"1"; // 1
+"string" + // NaN
  // 불리언 타입
  true; // 1
+false; // 0

// null 타입
+null; // 0

// undefined 타입
+undefined; // NaN

// 심벌 타입
+Symbol(); // TypeError: Cannot convert a Symbol value to a number

// 객체 타입
+{}; // NaN
+[]; // 0
+[10, 20]; // NaN
+function () {}; // NaN
```

- 빈 문자열(''), 빈 배열([]), null, false → 0
- true → 1
- 객체, 빈 배열이 아닌 배열(= 값이 있는 배열), undefined → NaN ( 주의 ! )

### 불리언 타입으로 변환

> 자바스크립트 엔진은 불리언 타입이 아닌 값을 `Truthy 값(참으로 평가되는 값)` or `Falsy 값(거짓으로 평가되는 값)` 으로 구분한다.

```cs
// 💡 자바스크립트 엔진이 Falsy 값으로 판단하는 값

+ false
+ undefined
+ null
+ 0, -0
+ NaN
+ ''(빈 문자열)
```

- `Falsy` 값 을 제외한 모든 값은 `Truthy` 값

### 9.3 명시적 타입 변환

---

개발자의 의도에 따라 명시적으로 타입을 변경하는 방법

- `표준 빌트인 생성자 함수(String, Number, Boolean)`을 `new 연산자 없이 호출하는 방법`
- `빌트인 메서드`를 사용하는 방법
- 암묵적 타입 변환

### 문자열 타입으로 변환

1. `String` 생성자 함수를 `new 연산자` 없이 호출하는 방법

```c
String(1); // "1"
String(true) // "true"
```

2. `Object.prototype.toString` 메서드를 사용하는 방법

```c
(Infinity).toString() // "Infinity"
(true).toString();
```

3. `문자열 연결 연산자`를 이용하는 방법

```c
NaN + ''; // "NaN"
Infinity + ''; // "Infinity"
```

### 숫자 타입으로 변환

1. `Number` 생성자 함수를 `new` 연산자 없이 호출하는 방법

```c
Number('0'); // 0
Number(true); // 1
Number(false) // 0
```

2. `parseInt`, `parseFloat` 함수를 사용하는 방법(**문자열만** 숫자 타입으로 변환 가능)

```c
parseInt('0'); // 0
```

3. 단항 산술 연산자를 이용하는 방법

```c
+ '0';  // 0
+ true  // 1
```

4. 산술 연산자를 이용하는 방법

```c
'0' + 1; // 0
true + 1; // 1
```

### 불리언 타입으로 변환

1. Boolean 생성자 함수를 new 연산자 없이 호출하는 방법

```c
// 문자열 타입
Boolean('x'); // true
Boolean(''); // false

// 숫자 타입
Boolean(0); // false
Boolean(NaN); // false
Boolean(Infinity); // true

// null 타입
Boolean(null);  // false

// undefined 타입
Boolean(undefined);  // false

// 객체 타입
Boolean({});   // true
Boolean([]);   // true
```

2. ! 부정 논리 연산자를 두 번 사용하는 방법

```c
!!'x'   // true
!!''    // false
!!0;    // false
!!1;    // true
```

### 9.4 단축 평가

---

### 논리 연산자를 이용한 단축평가

> 논리곱(&&) = 논리 연산의 결과를 결정하는 것은 두 번째 피연산자

```c
'Cat' && 'Dog' // 'Dog'
```

> 논리합(||) = 논리 연산의 결과를 결정하는 것은 첫 번째 피연산자

```c
'Cat' && 'Dog' // 'Cat'
```

```c
| 단축 평가 표현식  | 평가 결과 |
| ----------------- | -------- |
| true  || anything | true     |
| false || anything | anything |
| true && anything  | anything |
| false && anything | false    |
```

💡 **단축 평가를 사용하면 if문을 대체할 수 있다.**

- 어떤 조건이 Truthy 값(참으로 평가되는 값)일때 무언가를 해야한다면 논리곱(&&) 연산자 표현식으로 if문을 대체한다

```cs
var done = true;
var message = "";

// 💩 조건문으로 값 할당
if (done) message = "값";

// 👍 논리 연산자(논리곱)으로 값 할당
message = done && "값";
```

- 반대로 조건이 Falsy값일 때 무언가를 해야 한다면 논리합(||) 연산자 표현식으로 if문을 대체한다.

```c
// 💩 조건문으로 값 할당
if (!done) message = "값";

// 👍 논리 연산자(논리합)으로 값 할당
message = done || "값";
```

### "객체"를 가리키기를 기대하는 변수가 null 또는 undefined 가 아닌지 확인하고 프로퍼티를 참조할 때

- 객체는 key와 value로 구성된 프로퍼티(property)의 집합이다.
- 만약 객체를 가리키기를 기대하는 변수의 값이 객체가 아니라 null 또는 undefined인 경우 객체의 프로퍼티를 참조하면 타입 에러가 발생한다.
- **이 때 단축평가를 사용하면 에러를 발생시키지 않는다.**

```cs
// 💩
var elem = null;
var value = elem.value; // TypeError: Cannot read property 'value' of null

// 👍
// elem이 null 또는 undefined 같은 "Falsy 값"이면 elem 값으로 평가
// elem이 "Truthy 값"이면 elem.value 로 평가
var elem = null;
var elem = elem && elem.value; // null
```

### 함수 매개변수에 기본값을 설정할 때

- 함수를 호출할 때 인수를 전달하지 않으면 매개변수에는 undefined가 할당된다
- **단축 평가를 사용해 매개변수의 기본값을 설정하면 undefined로 인해 발생할 수 있는 에러를 방지할 수 있다.**

```cs
// 💩 인수를 전달하지 않을 경우
function getStringLength(str) {
  return str.length;
}
getStringLength(); // TypeError: Cannot read property 'length' of undefined

// 👍 단축 평가를 사용한 매개변수의 기본값 설정
function getStringLength(str) {
  str = str || "";
  return str.length;
}
getStringLength(); // 0

// 👍 Es6의 매개변수 default parameter 설정
function getStringLength(str = "") {
  return str.length;
}
getStringLength(); // 0
```

### 옵셔널 체이닝 연산자 `?.`

- 좌항의 피연산자가 null 또는 undefined → `undefined` 반환
- 그렇지 않은 경우 → 우항의 프로퍼티를 참조

```cs
var elem = null;
var value = elem?.value; // undefined
```

- **객체를 가리키기를 기대하는 변수가 null 또는 undefined가 아닌지 확인하고 프로퍼티를 안전하게 참조할 때 유용**
- 옵셔널 체이닝 도입 이전에는
  - `논리곱(&&)`을 사용한 `단축 평가`를 통해 → 변수가 null 또는 undefined 인지 확인했음

```cs
// 💡 논리곱(&&) 연산자 vs 옵셔널 체이닝 연산자

// 💩 논리곱(&&) 연산자 = 좌항 피연산자가 Falsy값이면, 좌항 피연산자를 그대로 반환한다. (단, 0 또는 ''은 객체로 평가될 때도 있다.)
var str = "";
var length = str && str.length; // ''

// 👍 옵셔널 체이닝 = 좌항 피연산자가 Falsy값이라도 null 또는 undefined 만 아니면, 우항의 프로퍼티를 참조한다.
var str = "";
var length = str?.length; // 0
```

### null 병합 연산자 `??`

- 좌항의 피연산자가 null 또는 undefined → 우항의 피연산자를 반환
- 그렇지 않은 경우 → 좌항의 프로퍼티를 참조

```cs
var foo = null ?? "default string"; // "default string"
```

- 변수에 기본값을 설정할 때 유용 !
- null 병합 연산자 도입 이전에는
  - `논리합(||)을 사용한 단축 평가` 를 통해 → 변수에 기본값을 설정했음

```cs
// 💡 논리합(||) 연산자 vs null 병합 연산자

// 💩 논리합(||) 연산자 = 좌항의 피연산자가 Falsy값이면, 우항의 피연산자를 반환
(단, 0 이나 ''은 기본값으로서 유호하다면 예기치 않은 동작이 발생 !)
var foo = "" || "default string"; // "default string"

// 👍 null 병합 연산자 = 좌항의 피연산자가 Falsy값이라도 null 또는 undefined 가 아니면, 좌항의 피연산자를 그대로 반환한다.
var foo = "" ?? "default string"; // ''
```
