---
layout: post
title: Java 오브젝트 리스트를 속성으로 정렬하기
category: Java
keywords:
  - java
  - object list
  - order
  - sort
  - 정렬
---

```java
Collections.sort(prjList, new Comparator<Map<String, Object>>() {
    @Override
    public int compare(final Map<String, Object> map1, final Map<String, Object> map2) {
    	Date saveTm1 = (Date) map1.get("save_tm");
		Date saveTm2 = (Date) map2.get("save_tm");
		//return saveTm1.compareTo(saveTm2); // asc
		return saveTm2.compareTo(saveTm1); // desc
    }
});
```
