---
layout: post
title: 브라우저 캐시 방지하기
category: Javascript/JQuery
keywords:
  - browser cache
  - 브라우저 캐시
  - 캐시 새로고침
  - Web cache
  - 캐시
  - 캐시 방지
  - css 캐시
  - css 새로고침
  - css 변경
  - css 적용
  - cache refresh
  - javascript 캐시
  - js 캐시
  - javascript 새로고침
  - javascript 변경
  - javascript 적용
---

### 1. 브라우저 캐시에 대해서

우리가 사용하는 웹브라우저는 빠른 성능과 효율을 위해서 여러가지 일을 하는데 그 중에 한가지가 브라우저 캐시다.
브라우저는 서버에서 내려받은 파일을 캐시에 저장하고, 동일한 파일이 필요한 경우에 브라우저 캐시의 파일을 이용함으로써 다시 서버에서 내려받는 것보다 훨씬 속도를 높일 수 있다. 하지만 캐시 때문에 웹 개발을 하는데 귀찮은 상황이 자주 일어난다.

css, javascript 파일을 수정 후 반영을 하고, 새로고침을 해도 변경되지 않는 상황이 발생한다. 이럴 경우에 대표적으로 이용하는 방법은 다음과 같다.

1. 브라우저 캐시를 수동 삭제
2. CTRL + F5 강제 새로고침
3. 수정한 파일의 이름 변경

이번 포스팅에서는 수정한 파일의 이름을 변경해서 브라우저가 다른 파일로 인식하게 하고, 캐시를 방지하는 방법을 알아보자!

### 2. 직접 쿼리스트링 붙이기

뭐 간단한 페이지 같은 경우, 직접 붙여도 무관하다. js/css 파일을 불러올때, 뒤에 suffix를 붙여주면 된다.

변경 전


```html
<script type="text/javascript" src="sample.js"></script>
<link rel="stylesheet" type="text/css" href="sample.css">
```


변경 후
```html
<script type="text/javascript" src="sample.js?ver=1.0"></script>
<link rel="stylesheet" type="text/css" href="sample.css?anything=1.1">
```

뒷부분의 `?ver=1.0` 이나, `?anything=1.1` 을 변경시마다 바꿔주어 브라우저에서 다른 파일이라고 인식하게 하는 방법이다.
하지만 프로젝트가 조금만 복잡해지면 이런 무식한 방법보다 좀더 효율적인 방법이 있다.


### 3. 자동으로 쿼리스트링 붙이기 (with JSTL)

브라우저에서 js/css 파일을 불러올때마다 자동으로 suffix를 붙여주면 된다.
불러올 때의 datetime을 붙여주면, 브라우저는 매번 새로운 파일로 인식하게 되고 한번 설정해 놓으면 편하게 캐시를 방지할 수 있다.


```html
<!-- jstl -->
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>

<jsp:useBean id="now" class="java.util.Date" />
<fmt:formatDate var="now" value="${now}" pattern="yyyyMMddHHmmss" />

<script type="text/javascript" src="sample.js?v=${now}"></script>
<link rel="stylesheet" type="text/css" href="sample.css?v=${now}">
```

위처럼 jstl formatDate를 이용해서 yyyyMMddHHmmss로 버전을 붙여준다. 브라우저에 접속할 때마다 시간이 변하므로 항상 새로운 파일로 인식하게 되고 캐시를 방지할 수 있다. 한번 설정해 놓으면 신경쓰지 않아도 되서 편하다.
