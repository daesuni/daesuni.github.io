---
layout: post
title: CSS opacity, visibility, display 의 차이점
category: CSS
keywords:
  - css
  - opacity
  - visibility
  - display
---

CSS style 에서 opacity, visibility, display 차이점을 정확히 알고 싶었다. 3가지 속성 모두 숨기기/보이기로 대충 알고있었는데, 이번기회에 정확하게 정리해 보자.

아래처럼 3가지 스타일 속성을 적용해보자.

```html
이 아래는 1번 박스가 있어요!
<div id="box-1" style="opacity: 0;">1번 박스</div>
이 아래는 2번 박스가 있어요!
<div id="box-2" style="visibility: hidden;">2번 박스</div>
이 아래는 3번 박스가 있어요!
<div id="box-3" style="display: none;">3번 박스</div>
여기는 끝입니다.
```

그러면 웹 페이지에서 위 3개 박스는 다음과 같이 보여지게 된다.

이 아래는 1번 박스가 있어요!
<div id="box-1" style="opacity: 0;">1번 박스</div>
이 아래는 2번 박스가 있어요!
<div id="box-2" style="visibility: hidden;">2번 박스</div>
이 아래는 3번 박스가 있어요!
<div id="box-3" style="display: none;">3번 박스</div>
여기는 끝입니다.

위를 살펴보면, `opacity: 0;`와 `visibility: hidden;`는 적용이 되어도 그 div가 차지하고 있는 영역이 그대로 유지되는 것을 볼 수 있다. 반면 `display: none;`은 영역이 사라진 것을 알 수 있다.

다른 차이점은 아래 표로 정리한다.

|속성|collapse|이벤트 적용|tab-order 적용|
|opacity: 0;|X|O|O|
|visibility: hidden;|X|X|X|
|display: none;|O|X|X|
