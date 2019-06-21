---
layout: post
title: Postgres를 이용한 full-text search
category: Postgres
keywords:
  - postgres
  - full text
  - seach
  - stemming
---

검색을 구현할 때는 RDBMS보다는 몽고DB같은 NoSQL이나 Solar같은 검색엔진을 사용한다.
lucene을 기반으로하는 elasticsearch 및 SOLR 검색엔진은 기본적으로 다음과 같은 유용한 기능들을 제공한다.
RDBMS로는 full-text search를 하려면 위와 같은 기능들도 없고, like검색을 해야하기 때문에 속도도 느리다.

- Stemming
- Ranking / Boost
- Support Multiple languages
- Fuzzy search for misspelling
- Accent support

하지만 Postgres는 위 기능들을 모두 제공한다. 이 글에서는 Postgres를 이용한 full-text search방법을 포스팅한다. 이 포스팅은 다음과 같은 사람들을 대상으로 한다.

- RDBMS에서 간단한 full-text검색 또는 검색기능을 넣고 싶다.
- 검색엔진을 개발하기에는 부담스럽다.
- 추가 소프트웨어 또는 라이브러리를 설치하기 싫다.
- Postgres를 사용한다.

### 1. 예제 테이블 생성

먼저, 예제에 사용할 테이블을 생성한다. idx(integer), words(text) 컬럼으로 구성된 테이블이다.

```sql
CREATE TABLE example (
  idx integer NOT NULL,
  words text NOT NULL,
  CONSTRAINT example_pk PRIMARY KEY (idx)
)
```

### 2. 데이터 생성

두번째로, 예제에 사용될 dummy data를 insert 해준다. 나는 테스트를 위해 1,000건 insert를 했지만, 줄여도 상관없다. 데이터는 영단어들이 구분자 `,`로 연결되어 있는 텍스트이다.

```sql
INSERT INTO example (idx, words)
  SELECT x.idx, 'Is the frighten north better than the branch? What if the bad pin ate the light?'
FROM
  generate_series(1, 1000) AS x(idx);
```

### 3. Like 검색

예제로 만든 테이블을 보자. 데이터들 중 `then`라는 단어가 포함된 행을 가져오는 일반적인 쿼리는 다음과 같다.

```sql
SELECT
  idx,
  words
FROM
  example
WHERE
  words LIKE '%then%' or
  words LIKE '%THEN%'
```

가장 일반적으로 생각할 수 있는 쿼리문이다. 영어의 경우, 대소문자 구분역시 해줘야 한다. 위처럼 or조건을 쓰던, LOWER, UPPER함수를 써서 가져오는 방식이다.

### 4. tsvector

Postgres의 데이터 타입에는 tsvector라는 형식이 있는데 <a href="https://www.postgresql.org/docs/10/datatype-textsearch.html#DATATYPE-TSVECTOR">Postgres doc</a>을 참조하자면 다음과 같이 설명이 있다.

A tsvector value is a sorted list of distinct lexemes, which are words that have been normalized to merge different variants of the same word (see Chapter 12 for details). Sorting and duplicate-elimination are done automatically during input, as shown in this example:

PostgreSQL은 전체 텍스트 검색을 지원하도록 고안된 두 가지 데이터 유형을 제공하며, 이는 질의에 가장 적합한 문서를 찾기 위한 자연어 문서 모음의 검색 활동이다. tsvector 유형은 텍스트 검색에 최적화된 형식의 문서를 나타내고, tsquery 유형은 텍스트 쿼리를 유사하게 나타낸다. 라고 한다.

글로 하는 설명은 어려우니 다음 쿼리를 실행해 보자.

``` sql
SELECT to_tsvector('a fat cat sat on a mat - it ate a fat rats');

                to_tsvector
-----------------------------------------------------
'a':1,6,10 'ate':9 'cat':3 'fat':2,11 'it':8 'mat':7 'on':5 'rats':12 'sat':4
```

조금 생소한 결과가 나온다. 대충 훑어보면 출현 단어와 원래 문장에서 그 단어의 위치들이 맵핑된것 처럼 보인다. 직접 찾아보고 위치를 세어보자. `a`는 첫번째, 6번째, 10번째에 총 3번 나왔다. `-` 문자도 제거되었다.

그렇다면 다음 쿼리를 실행해보자.

``` sql
SELECT to_tsvector('english', 'a fat cat sat on a mat - it ate a fat rats');

                to_tsvector
-----------------------------------------------------
'ate':10 'cat':4 'eat':1 'fat':3,12 'mat':8 'rat':13 'sat':5
```

`to_tsvector`함수의 첫번째 인자로 `english`를 전달했다. 첫번째 쿼리결과와 무엇이 다른가?

`a`와 `it`, `on`이 사라졌고, `ate`의 동사원형인 `eat`가 생겼다. 불용어 제거와 정규화가 실행됬다고 짐작할 수 있다. Postgres에서는 이 밖에도 가중치, 랭킹과 같이 검색엔진에서 제공하는 다양한 기능들을 제공하는데 이번 포스팅에서는 다루지 않는다.

그렇다면 이제 words 컬럼을 tsvector형으로 변환시켜 보자.

### 5. tsvector형 컬럼 생성

```sql
-- 컬럼 생성
ALTER TABLE
	example
ADD COLUMN
	tsvec_words tsvector

-- 데이터 업데이트
UPDATE
	example
SET
	tsvec_words = to_tsvector('english', words)

-- 인덱스 생성
CREATE INDEX
	example_idx
ON
	example
USING
	gin(tsvec_words)

-- 트리거 생성
CREATE TRIGGER
  example_trigger
BEFORE INSERT OR UPDATE ON
  example
FOR EACH ROW EXECUTE PROCEDURE
  tsvector_update_trigger('tsvec_words', 'english', 'words');
```

tsvec_words컬럼을 만들고 words컬럼을 tsvector화해서 update했고, 그걸로 인덱스를 잡아주었다. 또한 insert/update후에 자동으로 tsvec_words에 업데이트 하도록 트리거도 설정해 주었다.

그렇다면 다시 3번의 Like검색을 tsquery로 바꿔보자.

```sql
SELECT
  idx,
  words
FROM
  example
WHERE
  tsvec_words @@ to_tsquery('english', 'then')
```

이 포스팅에서 설명한 Postgres의 text-search는 극히 일부분이다. 정말 기본적인 개념과 실무 사용에 필요한 것만 포스팅했으니 좀더 궁금하신분들은 <a href="https://www.postgresql.org/docs/10/textsearch.html">Postgres 공식 사이트</a>를 참고하시거나 이 포스팅 작성에 많은 도움을 받은 <a href="http://rachbelaid.com/postgres-full-text-search-is-good-enough/">Rachid Belaid</a>님의 글을 참고하면 된다.
