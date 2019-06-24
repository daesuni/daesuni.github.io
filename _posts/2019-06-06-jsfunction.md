---
layout: post
title: Javascript 함수 선언식 vs 함수 표현식
category: Javascript/JQuery
keywords:
  - javascript
  - 함수 표현식
  - 함수 선언식
  - 호이스팅
  - hoisting
  - function declarations
  - function expressions
  - javascript hoisting
---

Javscript에서 함수를 선언할 때 크게 두 가지 방법이 있다. 함수 선언식이라고 불리는 `function a()`과 함수 표현식이라 불리는 `var a = function()`이다. 대개 아무런 문제 없이 여러 명이 개발시에, 두가지를 혼합하여 사용하거나 혹은 하나만 사용하여도 큰 문제는 없다. 개인적으로 선호하는 방식도 있을 것이다. 하지만 제대로 알고 쓰지 않으면 대부분이 그러하듯 예상치 못한 에러로 고생할 수 있으니 정확하게 알고 쓰도록 하자.

```javascript
// 함수 선언식 (function declarations)
function welcome(name) {
  console.log('Hello, ' + name);
}
```

```javascript
// 함수 표현식 (function expressions)
var welcome = function(name) {
  console.log('Hello, ' + name);
};
```

이 둘을 설명하려면 먼저 알고 있어야 하는 개념이 있다. 바로 <a href="https://www.w3schools.com/js/js_hoisting.asp">javascript hoisting(호이스팅)</a>이다. hoisting의 사전적 의미는 '끌어올리다'라는 뜻이다.

### 1. Hoisting(호이스팅) 이란 ?

javascript 엔진은 소스를 해석할 때, 모든 선언을 현재 스코프(현재 스크립트 및 현재 함수)의 맨 위로 위치시킨다. 이게 무슨말이냐 하면,

```javascript
welcome(myname);

var myname = 'Anna';
function welcome(name) {
  console.log('Hello, ' + name);
}
```

위와 같은 소스를 개발자도구에서 실행시켜보자. `welcome(myname)`을 실행할 때에는 welcome함수와 myname이란 변수는 정의되지 않았음에도 불구하고 에러가 나지 않는다. 정상적으로 실행이 된다.

javascript 엔진에 의해 모든 선언이 상단으로 위치했기 때문이다. 실제로 javascript가 위 소스를 해석하는 순서는 다음과 같다.

```javascript
var myname = 'Anna';
function welcome(name) {
  console.log('Hello, ' + name);
}

welcome(myname);
```

호이스팅 개념을 이해하지 못하고 개발을 하면 버그가 생길수 있다. 호이스팅을 항상 염두해두고 개발하도록 하자.

### 2. 함수 선언식 (function declarations)

```javascript
// 함수 선언식 (function declarations)
welcome();
function welcome(name) {
  console.log('Hello, ' + name);
}
```

- 함수 선언은 `function`으로 시작함
- 함수 호출은 함수 이름을 사용해야 함
- 호이스팅의 영향을 받아 끌어올림
- 코드가 실행되기 전에 로드
- 위와 같이 함수 선언이 되기전에 실행을 해도 정상적으로 작동

### 3. 함수 표현식 (function expressions)

```javascript
// 함수 표현식 (function expressions)
welcome();
var welcome = function(name) {
  console.log('Hello, ' + name);
};
```

- 함수를 변수에 저장할 수 있음
- 함수 호출은 변수명을 사용함
- 호이스팅의 영향을 받지 않음
- 인터프리터가 해당 코드 행에 도달 할 때 로드
- 위같이 함수를 호출시에 에러가 발생
- 클로저로 사용 가능
- 콜백 인자로 사용 가능 (다른 함수의 인자로 넘길 수 있음)
- 즉시 호출 된 함수 식 (IIFE) 사용 가능

### 4. 결론

함수 표현식 `var`이 선언식 `function`에 비해 장점이 많고
<a href="https://github.com/airbnb/javascript#functions">AirBnb JS Style 가이드</a>에서도 함수 선언식보다는 함수 표현식을 지향하고 있다.
하지만 표현식과 선언식의 차이점을 인지하고 있는 상태라면 지극히 취향 차이라고 말하고 싶다. 어떤 방식이든 일관된 방식으로 구현하는 게 좋을 것 같다.
