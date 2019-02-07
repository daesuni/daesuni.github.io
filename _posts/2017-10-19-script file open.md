---
layout: post
title: Javascript로 서버 없이 파일 읽기
category: Javascript/JQuery
---

Javascript를 이용해서 서버 없이 파일 읽는 방법

```javascript
var reader = new XMLHttpRequest();
	reader.open('GET', G_CONTEXT_PATH + '/data/sample.txt', true);
	reader.overrideMimeType('text/plain; charset=utf-8');
	reader.onload = function() {
		if (reader.status == 200) {
			console.log(reader.responseText);
		};
	};
reader.send(null);
```
