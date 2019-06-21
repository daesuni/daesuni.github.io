---
layout: post
title: Javscript/JQuery data-url를 이미지 파일로 다운로드
category: Javascript/JQuery
keywords:
  - javascript
  - jQuery
  - data-url
---

```html
<html>
	<body>
		<button id="button">Download</button>

		<script>
			var img = 'data:image/jpeg;base64,~~~~~~~';

			$('#button').on('click', function(){
				var download = document.createElement('a');
			    download.href = img;
			    download.target = '_blank';
			    download.download = 'test';
			    var evt = document.createEvent('MouseEvents');
			    evt.initMouseEvent('click', true, true, window, 1, 0, 0, 0, 0, false, false, false, false, 0, null);
			    download.dispatchEvent(evt);
			});
		</script>
	</body>
</html>
```
