---
layout: post
title: for, foreach, filter, map, reduce, $.each 기능 및 성능 비교
category: Javascript/Jquery
---

&nbsp;Javascript와 Jquery를 주로 쓰면서도 궁금했다.<br/>
&nbsp;반복문에는 우리가 일반적으로 알고있는 for문, $.each, forEach, map, filter 등 정말 많은 종류가 있다.<br/>
&nbsp;주로 하나의 메서드로 대부분 것들을 할 수 있지만, 어떨때는 each를 써야 하고 또 어떤 경우에는 filter가 좋은지를 정확히 알고 쓰지 못했다.<br/>
&nbsp;이번 포스트에서 각 메서드들을 어떨때 어떻게 사용해야하는지 정리하고 성능비교도 해보자!

### 1. for loop

일반적인 for문은 아래와 같이 쓴다.

```javascript
for (int i = 0; i < 10; i++) {
  console.log(arr[i]);
};
```

- 가장 빠르고 단순하다. 그래서 효율적이다.
- 중간에 loop 종료가 가능하다. (continue or break)
- 반복범위 컨트롤이 가능하다.
- 변수를 활용할 수 있다. (var i 값을 사용할 수 있다)

### 2. forEach

일반적인 forEach문은 다음과 같다.

```javascript
arr.forEach(function(v, i, arr) {
  console.log(v);
});
```

&nbsp;1번에서 살펴본 일반 for문보다 훨씬 가독성이 좋다. 예를 들어보자.

```javascript
for (var i = 0; i <arr.length; i++) {
  console.log('element', i, arr[i]);
  console.log(arr[i].property1);
  console.log(arr[i].property2);
};

arr.forEach (function (v, i) {
  console.log('element', i, v);
  console.log(v.property1);
  console.log(v.property2);
});
```

&nbsp;위와 아래 중 어떤것이 더 눈에 들어올까? 좀더 복잡해진다면?<br/>
&nbsp;가독성이 좋다는 것은 개발에서 엄청난 차이를 가져온다. forEach는 복잡한 객체를 처리하는데 있어서 유리하다. 좀더 인간친화적인(?) 방법이라고 할 수 있다.<br/>
&nbsp;개인적으로 성능에 큰 문제가 없다면 되도록 forEach를 사용하는걸 추천한다. (성능에 대해서는 아래에서)

- 빠른편이다.
- 일반 for문보다 가독성이 좋고, 객체를 다루기가 쉽다.
- for문과 다르게 중간에 끊을 방법이 없다. (return으로 skip가능)
- 원래 배열을 훼손한다.

### 3. filter

일반적인 filter 사용법은 다음과 같다.

```javascript
var newArr = arr.filter(function(v, i, arr) {
  return condition;
});
```

&nbsp;filter의 가장 큰 특징은 boolean형태의 return값을 갖는다. return값이 true일경우, 그 요소를 반환하고 false일경우, 반환하지 않는다.
&nbsp;예를 들어보자.

```javascript
var arr = [1, 2, 3, 4, 5];
var newArr = arr.filter(function(v) {
  return v % 2 == 0;
});
// 2, 4
```

&nbsp;또다른 큰 특징은, 빈 요소를 반환하지 않는다는 것이다. 이것을 이용하면 유용하게 쓸 수 있다.

```javascript
var arr = [0, , , 1, , , , , 2, , , , 3];
var newArr = arr.filter(function() { return true; });
var newArr = arr.filter(function(el) { return el; });
// 0, 1, 2, 3
```

- 빠른편이다.
- chainable하다.
- 원래 배열을 훼손하지 않는다.
- 빈 배열 요소를 반환하지 않는다.
- 대용량 배열 처리시 메모리 overflow 가능성이 있다.
- return 값은 true/false 이며, 요소를 반환한다.

### 4. map

map은 기본적으로 다음과 같이 쓴다. filter와 문법은 똑같다.

```javascript
var newArr = arr.map(function(v, i, arr) {
  return condition;
});
```

&nbsp;filter와 다른점이라고 하면, filter는 return 값으로 true/false만 쓸 수 있으며, 요소를 반환한다.<br/>
하지만 map의 경우 요소가 아닌 새로운 값을 반환할 수 있다.

```javascript
var arr = [1, 2, 3, 4, 5];
var newArr = arr.map(function(v, i, arr) {
  return v + 1;
});
// 2, 3, 4, 5, 6
```

- 기본적인 특징은 filter와 같다.
- return 값 자체를 반환한다.

### 5. reduce

### 6. $.each

$('.rows').each(function(i, el) {
    // do something with ALL the rows
}).filter('.even').each(function(i, el) {
    // do something with the even rows
});

### 성능 비교

![Loop performance test]({{"/assets/images/posts/loopperformance.png"| relative_url}})
*<https://jsperf.com/dslooppf>*
