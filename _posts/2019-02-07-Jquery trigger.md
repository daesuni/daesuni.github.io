---
layout: post
title: Jquery click(); vs trigger('click');
category: Javascript/JQuery
---

Jquery에서 이벤트를 처리할때 크게 두가지 방법이 있다. 클릭 이벤트를 예로 들자면 하나는 `click();`이고 다른 하나는 `trigger('click');`이다. 이 둘의 차이는 뭘까?

### 1. click(); vs trigger('click');

`click();`의 Jquery의 내부 소스코드를 보면 다음과 같다. (<a target="_blank" href="https://j11y.io/jquery/#v=1.11.2&fn=$.fn.click">jQuery 1.11.2</a> 기준)

```javascript
function (data, fn) {
  return arguments.length > 0 ? this.on(name, null, data, fn) : this.trigger(name);
}
```

실제 내부적으로는 `trigger('click');`이 호출된다는 것을 알 수 있다. 두 이벤트 모두 똑같은 결과를 얻는다는 것이다.

### 2. 성능 비교

둘의 퍼포먼스 테스트 결과는?

![clicktrigger]({{"/images/posts/clicktrigger.png"| relative_url}})
*<a target="_blank" href="https://jsperf.com/eventtest13412">https://jsperf.com/eventtest13412</a>*

### 3. 결론

`click();`과 `trigger('click');` 모두 **같은 동작**을 한다.<br/>
단지 차이점이라고 한다면 `click();`메서드에는 trigger 전에 한단계의 코드가 더 있기 때문에 약간의 오버헤드가 발생하는 것을 알 수 있었다. 큰 차이는 없지만 테스트 결과 약간의 퍼포먼스 차이가 있었다.

따라서 조건이 같은 상황이면 `trigger()`을 사용하는 것이 좋다고 말할 수 있겠다.
