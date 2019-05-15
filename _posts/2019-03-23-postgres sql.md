---
layout: post
title: Pgsql 자주쓰는 쿼리 모음
category: Postgres
---

### 1. 테스트 데이터

```sql
-----------------------------------------------------
id	|	name		|	age	|	address		|	salary
-----------------------------------------------------
1	|	"Ramesh"	|	32	|	"Ahmedabad"	|	2000
2	|	"Khilan"	|	25	|	"Delhi"		|	1500
3	|	"kaushik"	|	23	|	"Kota"		|	2000
4	|	"Chaitali"	|	25	|	"Mumbai"	|	6500
5	|	"Hardik"	|	27	|	"Bhopal"	|	8500
6	|	"Komal"		|	22	|	"MP"		|	4500
7	|	"Muffy"		|	24	|	"Indore"	|	10000
```


### 2. 컬럼을 하나의 스트링으로 가져오기

여러 row들의 컬럼 중에 하나의 컬럼을 string으로 join하고 싶을 때 사용한다. array_agg는 배열 형태로 가져오는 것이고 array_to_string은 구분자로 join해 주는 역할을 한다.

```sql
SELECT
	array_to_string(array_agg(name), ',') as result
FROM
	customers

------------------------------------------------
result
------------------------------------------------
Ramesh,Khilan,kaushik,Chaitali,Hardik,Komal,Muffy
```

### 3. similar to (multiple like 검색)

특정 키워드를 포함하는 검색을 할때 보통 like '%키워드%' 를 사용한다. 하지만 여러가지 키워드를 한꺼번에 할 수 없다. similar to문은 여러 키워드에 대해 like검색을 할 때 사용한다.

```sql
SELECT
	*
FROM
	customers
WHERE
	name SIMILAR TO '%(lan|dik)%';

-----------------------------------------------------
id	|	name		|	age	|	address		|	salary
-----------------------------------------------------
2	|	"Khilan"	|	25	|	"Delhi"		|	1500
5	|	"Hardik"	|	27	|	"Bhopal"	|	8500
```
