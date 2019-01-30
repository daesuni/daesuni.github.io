---
layout: post
title: Postgres 락 확인/제거하기
category: Postgres
---

##### 락 확인
```xml
	SELECT t.relname,
	       l.locktype,
	       page,
	       virtualtransaction,
	       pid,
	       mode,
	       granted
	FROM pg_locks l,
	     pg_stat_all_tables t
	WHERE l.relation = t.relid
	ORDER BY relation ASC;
```

##### 락 제거
```xml
	SELECT pg_terminate_backend(pid) FROM pg_stat_activity;
```
