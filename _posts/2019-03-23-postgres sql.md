---
layout: post
title: Postgres 자주쓰는 쿼리 모음
category: Postgres
---

### 1. SELECT 결과 컬럼을 하나의 스트링으로 가져오기

여러 row들의 컬럼 중에 하나의 컬럼을 string으로 join하고 싶을 때 사용한다. array_agg는 배열 형태로 가져오는 것이고 array_to_string은 구분자로 join해 주는 역할을 한다.

```sql
SELECT
	array_to_string(array_agg(name), ',') as result
FROM
	table
```

### 2. similar to (multiple like 검색)

특정 키워드를 포함하는 검색을 할때 보통 like '%키워드%' 를 사용한다. 하지만 여러가지 키워드를 한꺼번에 할 수 없다. similar to문은 여러 키워드에 대해 like검색을 할 때 사용한다.

```sql
SELECT
	*
FROM
	table
WHERE
	name SIMILAR TO '%(lan|dik)%';
```

### 3. null값을 다른 값으로 대체

SELECT 결과가 null인 경우, 특정 값으로 대체하여 가져올 수 있다. 아래 쿼리의 경우 name이 null일 경우 name2를 가져오고, name2가 null인 경우 'No name'을 반환한다.

```sql
SELECT
	coalesce(name, name2, 'No name')
FROM
	table
```

### 4. 특정 컬럼으로 중복 제거

```sql
SELECT DISTINCT ON (name)
	name,
	adress
FROM
	table
```

### 5. Paging 처리하기

```sql
SELECT
	row_number() OVER () AS i,
	name
FROM
	table
OFFSET
	pageNum
LIMIT
	pageSize
```

### 6. Case when then else 문

```sql
SELECT
	CASE WHEN name = '김XX' then 1 ELSE 0 END as code
FROM
	table
```
