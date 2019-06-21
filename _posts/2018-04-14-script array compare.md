---
layout: post
title: Javascript/JQuery array compare
category: Javascript/JQuery
keywords:
  - javascript
  - jQuery
  - 배열 비교
  - array compare
---

서로 다른 두 배열이 같은지 비교

```javascript
	function arrayCompare(a, b) {
		var bool = a.length == b.length && a.every(function(e, i) {
			return e === b[i];
		});
		return bool;
	};
```
