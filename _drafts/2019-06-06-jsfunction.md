---
layout: post
title: Javascript function() vs var function
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

### 2. 함수 선언식

```javascript
// 함수 선언식 (function declarations)
welcome();
function welcome(name) {
  console.log('Hello, ' + name);
}
```

함수 선언식의 가장 큰 특징은 호이스팅의 영향을 받아 끌어올림된다는 것이다. 위와 같이 함수 선언이 되기전에 실행을 해도 정상적으로 작동이 된다.

### 3. 함수 표현식

```javascript
// 함수 표현식 (function expressions)
welcome();
var welcome = function(name) {
  console.log('Hello, ' + name);
};
```

함수 표현식은 일반적으로 변수선언 하는 방식과 같다. 하지만 호이스팅이 안되기 때문에 위같이 함수를 호출시에 에러가 발생한다.

### 4. 결론
