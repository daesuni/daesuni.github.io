---
layout: post
title: for, foreach, filter, map, reduce 기능 및 성능 비교
category: Javascript/JQuery
---

Javascript와 jQuery를 주로 쓰면서도 궁금했다. 반복문에는 우리가 일반적으로 알고있는 for문, $.each, forEach, map, filter 등 정말 많은 종류가 있다.

주로 하나의 메서드로 대부분 것들을 할 수 있지만, 어떨때는 each를 써야 하고 또 어떤 경우에는 filter가 좋은지를 정확히 알고 쓰지 못했고 왜 이렇게 따로따로 만들어놨을까 라는 생각도 했다. 이번 포스트에서 각 메서드들을 어떨때 어떻게 사용해야하는지 정리하고 성능비교도 해보자!

### 1. for loop

일반적인 for문은 아래와 같이 쓴다.

```javascript
for (int i = 0; i < 10; i++) {
  console.log(arr[i]);
};
```

- 가장 빠르고 단순하다. 그래서 효율적이다.
- 모든 자료형에 대해 사용이 가능하다.
- 중간에 loop 건너뛰기나 종료가 가능하다. (continue or break)
- 반복범위 컨트롤이 가능하다. (i++, i--, i+=2*i 등)
- 변수를 활용할 수 있다. (var i 값을 사용할 수 있다)

### 2. forEach

일반적인 forEach문은 다음과 같다.

```javascript
arr.forEach(function(v, i, arr) {
  console.log(v);
});
```

1번에서 살펴본 일반 for문보다 훨씬 가독성이 좋다. 예를 들어보자.

```javascript
for (var i = 0; i <arr.length; i++) {
  console.log('element', i, arr[i]);
  console.log(arr[i].property1 + arr[i].property2);
  console.log(arr[i].property2);
};

arr.forEach (function (v, i) {
  console.log('element', i, v);
  console.log(v.property1 + v.property2);
  console.log(v.property2);
});
```

위와 아래 중 어떤것이 더 눈에 들어올까? 좀더 복잡해진다면 어떨까?
가독성이 좋다는 것은 개발에서 엄청난 차이를 가져온다. forEach는 복잡한 객체를 처리하는데 있어서 유리하다. 좀더 인간친화적인(?) 방법이라고 할 수 있다.

forEach문은 구문 밖으로 return값을 받지 못한다. 아래의 예를 보자.

```javascript
var arr = [1,2,3,4,5];
var newArr = arr.forEach(function(e, i) {
  return e;
});
// undefined
```

- 빠른편이다.
- Array객체에서 사용이 가능하다.
- 일반 for문보다 가독성이 좋고, 객체형을 다루기가 쉽다.
- for문과 다르게 중간에 끊을 방법이 없다. (return으로 skip가능)
- return값을 받지 못한다.

### 3. filter

일반적인 filter 사용법은 다음과 같다.

```javascript
var newArr = arr.filter(function(v, i, arr) {
  return condition;
});
```

filter의 가장 큰 특징은 boolean형태의 return값을 갖는다. return값이 true일경우, 그 요소를 반환하고 false일경우, 반환하지 않는다. 기본값은 false이다. 예를 들어보자.

```javascript
var arr = [1, 2, 3, 4, 5];
var newArr = arr.filter(function(v) {
  return v % 2 == 0;
});
// 2, 4

var distances = [
  {from: 'Seoul', to: 'Jejudo', distance: 23451},
  {from: 'Busan', to: 'Daegu', distance: 6457},
  {from: 'Daejeon', to: 'Incheon', distance: 233}
];
var filteredDistances = distances.filter(item => item.distance < 10000);
console.log(filteredDistances);
```

위의 두 가지 예를 보면 알겠지만, 깔끔하게 원하는 요소들만 필터링할 수 있는 유용한 메서드다. 또 다른 큰 특징은, 빈 요소를 반환하지 않는다는 것이다. 이것을 이용하면 유용하게 쓸 수 있다.

```javascript
var arr = [0, , , 1, , , , , 2, , , , 3];
var newArr = arr.filter(function() { return true; });
var newArr = arr.filter(function(el) { return el; });
// 0, 1, 2, 3
```

- 빠른편이다.
- Array객체에서 사용이 가능하다.
- chainable하다.
- 빈 배열 요소를 반환하지 않는다.
- 대용량 배열 처리시 메모리 overflow 가능성이 있다.
- return값은 true/false이며, 요소를 반환한다.

### 4. map

map은 기본적으로 다음과 같이 쓴다. filter와 문법은 똑같다.

```javascript
var newArr = arr.map(function(v, i, arr) {
  return condition;
});
```

filter와 다른점이라고 하면, filter는 return값으로 true/false만 쓸 수 있으며, 요소를 반환하지만, map의 경우 요소가 아닌 새로운 값을 만들어서 return할 수 있다.

```javascript
var arr = [1, 2, 3, 4, 5];
var newArr = arr.map(function(v, i, arr) {
  return v + 1;
});
// 2, 3, 4, 5, 6
```

- 빠른편이다.
- Array객체에서 사용이 가능하다.
- chainable하다.
- 대용량 배열 처리시 메모리 overflow 가능성이 있다.
- return값 자체를 반환한다.

### 5. reduce

reduce는 위에 나왔던 반복문들과는 약간 개념과 사용법이 다르다.

```javascript
var arr = [1, 2, 3, 4, 5];
var newArr = arr.reduce(function(acc, v, i, arr) {
  return acc + v;
});
// 15
```

reduce의 가장 큰 특징으로는 첫번째 인자인 accumulator 이다. accumulator 는 return값을 누적하는데, 계속해서 전달받아서 사용할 수도 있다.

두번째 특징은 accumulator의 초기값을 설정할 수 있다는 점이다. optional하며 생략시에는 첫번째 return값이 된다. 아래 예시를 보자.

```javascript
var arr = [1,2,3,4,5]
var newArr = arr.reduce(function(acc, v, i, arr) {
  return acc + v;
}, 100);
// 115
```

첫번째 예시와 다르게 초기값 때문에 115라는 값이 나오게 됬다. 어떻게 활용하느냐에 따라 reduce는 강력하고 확장성이 높다. accumulator의 값은 배열이 될수도 있고, object가 될수도 있다.

### 성능 비교

![loop performance test]({{"/images/posts/loopperformance.png"| relative_url}})
*<a target="_blank" href="https://jsperf.com/dslooppf">https://jsperf.com/dslooppf</a>*
