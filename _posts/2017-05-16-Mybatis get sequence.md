---
layout: post
title: Mybatis Insert 후 sequence 가져오기
category: Mybatis
---

개발을 하다보면 방금 Insert된 data의 seq값을 써야 할때가 있다. 예를 들면 즐겨찾기 기능을 구현하거나, 데이터의 key값을 가지고 이미지를 다른 table에 저장하거나 할때 필요했다. Mybatis에서는 다음과 같은 방법이 있다.

```sql
<insert id="id"
        parameterType="Class"
        useGeneratedKeys="true"
        keyProperty="seq"
        keyColumn="seq">

    SELECT
      column_name
    FROM
      table_name
    WHERE
      conditions

</insert>
```

여기서 useGeneratedKeys, keyProperty, keyColumn의 설명을 보면 다음과 같다.

| Property								| 설명									|
| ------------------------------------- | ------------------------------------- |
| useGeneratedKeys 						| (입력(insert, update)에만 적용) 데이터베이스에서 내부적으로 생성한 키 (예를들어 MySQL또는 SQL Server와 같은 RDBMS의 자동 증가 필드)를 받는 JDBC getGeneratedKeys메소드를 사용하도록 설정하다. 디폴트는 false 이다. |
| keyProperty							| (입력(insert, update)에만 적용) getGeneratedKeys 메소드나 insert 구문의 selectKey 하위 엘리먼트에 의해 리턴된 키를 셋팅할 프로퍼티를 지정. 디폴트는 셋팅하지 않는 것이다. 여러개의 칼럼을 사용한다면 프로퍼티명에 콤마를 구분자로 나열할수 있다. |
| keyColumn								| (입력(insert, update)에만 적용) 생성키를 가진 테이블의 칼럼명을 셋팅. 키 칼럼이 테이블이 첫번째 칼럼이 아닌 데이터베이스(PostgreSQL 처럼)에서만 필요하다. 여러개의 칼럼을 사용한다면 프로퍼티명에 콤마를 구분자로 나열할수 있다. |

만약 seq의 값이 autoincrement이고, 위와 같이 쿼리를 작성했다면 Insert된 행의 seq값을 가져온다. 그리고 이후 이 seq는 따로 return을 지정해 주지 않아도 parameterType에 지정된 객체에 담겨온다(Mybatis 3.x 이상). 또한 VO를 사용하지 않고 HashMap을 사용하는 경우에도 동일하게 담겨 넘어오니 꺼내서 사용하기만 하면 된다.
