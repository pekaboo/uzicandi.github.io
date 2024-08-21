---
title: "4. 변수"
description:
date: 2024-08-04
update: 2024-08-04
tags:
  - Javascript
series: "모던 자바스크립트 Deep Dive"
---

#### 변수 선언

var vs let/const

- var의 단점

- 스코프를 알아야 함

자바스크립트의 변수 선언

1. 선언 단계: 이름을 등록해서 JS 엔진에 변수의 존재를 알린다
2. 초기화 단계: 값을 저장하기 위한 메모리 공간 확보, 암묵적으로 undefined를 할당에 초기화

```
var score;
```

- 선언단계를 통해 변수이름 score를 등록
- 초기화를 통해 score 변수에 암묵적으로 undefined를 할당해 초기화.

초기화? 변수가 선언된 이후 최초로 값을 할당하는 것

만약 선언하지 않은 식별자에 접근하면 ReferenceError(참조 에러)가 발생함.
ReferenceError는 식별자를 통해 값을 참조하려 했지만 자바스크립트 엔진이 등록된 식별자를 찾을 수 없을 때 발생하는 에러.

#### 변수이름은 어디에 등록되는가?

--
변수이름을 비롯한 모든 식별자는 **실행 컨텍스트**에 등록된다.
**실행컨텍스트: 자바스크립트 엔진이 소스코드를 평가하고 실행하기 위해 필요한 환경을 제공하고 코드의 실행결과를 실제로 관리하는 영역.**
자바스크립트 엔진은 실행 컨텍스트를 통해 식별자와 스코프를 관리함.

변수 이름과 변수 값은 실행 컨텍스트 내에 key/value 형식인 객체로 등록되어 관리됨.
-> 자바스크립트 엔진이 변수를 관리하는 메커니즘은 Scope, 실행 컨텍스트에서 자세히

#### 변수 선언의 실행 시점과 변수 호이스팅

> 모든 선언문은 런타임 이전 단계에서 먼저 실행된다.

```
console.log(score); // undefined

var score;
```

1. 변수 선언문 보다 변수를 참조하는 코드(console.log)가 앞에 있다.
2. JS는 인터프리터에 의해 한 줄씩 순차적으로 실행된다.
   2-1. 따라서 변수 참조시 참조 에러(ReferenceError)가 발생할 것 처럼 보인다.
3. 하지만 참조에러가 발생하지 않고 undefined가 출력된다.
4. 이유는 **변수 선언**이 소스코드가 한 줄씩 순차적으로 실행되는 시점, 즉 Runtime이 아니라, 그 **이전 단계에서 먼저 실행**되기 때문이다.

자바스크립트 엔진은 소스코드를 한줄씩 순차적으로 실행하기에 앞서 먼저
**소스코드의 평가 과정**을 거치면서 소스코드를 실행하기 위한 준비를 한다.
이때 **변수선언을 포함한 모든 선언문을 소스코드에서 찾아내 먼저 실행한다**
소스코드의 평가 과정이 끝나면 비로소 변수 선언을 포함한 모든 선언문을 제외하고 소스코드를 한 줄씩 순차적으로 실행한다.

> 즉, 자바스크립트 엔진은 변수 선언이 소스코드의 어디에 있든 상관없이

- 다른 코드보다 먼저 실행한다.
- 소스코드의 어디에 위치하는지 상관없이 어디서든 변수를 참조할 수 있다.

#### 값의 할당

> 변수 선언과 값의 할당의 실행시점이 다르다.

변수선언

- 소스코드가 순차적으로 실행되는 시점인 런타임 이전에 먼저 실행
  값의 할당
- 런타임에 실행

```
console.log(score); // undefined

var score; // 1. 변수 선언
score = 80; // 2. 값의 할당

console.log(score); // 80
```

score 변수에 값을 할당하면 score 변수의 값은 undefined에서 새롭게 할당한 숫자 값 80으로 변경(재할당)된다.

- 변수에 값을 할 당할 때는 이전 값 undefined가 저장되어 있던 메모리 공간을 지우고, 그 메모리 공간에 할당 값 80을 새로 저장하는 것이 아니라,
- 새로운 메모리 공간을 확보하고 그 곳에 할당 값 80을 저장한다.

#### 값의 재할당

```
var score = 80;
score = 90;
```

값을 재할당할 때도 메모리 공간을 새로 확보하고 그 공간에 저장하고, 식별자와 연결한다.
-> 불필요한 값들이 메모리에 저장되어 있음
-> 가비지 컬렉터에 의해 메모리에서 자동 해제된다.

**가비지컬렉터**

- 어떤 식별자도 참조하지 않는 메모리 공간을 검사하여 메모리 해제하는 기능
- JS는 가비지컬렉터를 내장하고 있는 **매니지드언어** 로서 Memery Leak을 방지한다.

**언매니지드 언어와 매니지드 언어**
메모리 관리 방식에 따라 분류

- UnManaged:
  - C언어, 개발자가 명시적으로 메모리를 할당하고 해제. malloc()과 free()같은 저수준 (low-level) 메모리 제어 기능을 제공함.
- Managed:
  - Javascript
    - 메모리 관리 기능을 언어차원에서 담당. 개발자가 명시적으로 할당, 해제 불가.
    - 일정한 생산성 확보 but 성능 면에서 어느정도의 손실

#### 식별자 네이밍 규칙

식별자(identifier): 어떤 값을 구별해서 식별해낼 수 있는 고유한 이름

- 식별자는 특수문자를 제외한 문자, 숫자, 언더스코어(\_), 달러 기호($)를 포함할 수 있음
- 단, 특수문자를 제외한 문자, 숫자, 언더스코어(\_), 달러 기호($)로 시작해야함. 숫자로 시작X
- 예약어는 식별자로 사용할 수 없음