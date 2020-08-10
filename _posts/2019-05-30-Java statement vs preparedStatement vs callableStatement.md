---
layout: post
title: Java statement vs preparedStatement vs callableStatement 비교
category: Java
keywords:
  - java
  - statement
  - prepared statement
  - callable statement
---

Java에서 JDBC를 이용하여 DB에 접속 하고자 할때 JDBC API는 3종류의 인터페이스를 제공한다.

1. statement - 일반적인 sql쿼리를 실행
2. preparedStatement - 동적 또는 매개변수가 필요한 sql쿼리를 실행
3. callableStatement - 저장된 프로시져를 실행

이 세가지 인터페이스는 비슷해보이지만 각각 사용 목적과 의도가 다르다. 이번 포스팅에서 3가지의 차이점을 살펴보자.

<br/>

### 1. statement

기본적은 사용법은 다음과 같다.

```java
String sql = "SELECT name, phone, address FROM classTable";
Statement s = conn.credateStatement();
ResultSet rs = s.executeQuerey(sql);
```

statement는 일반적인 sql 쿼리를 실행하는데 사용된다. 매개변수를 전달할 수도 없고 실행할 때마다 컴파일을 하게된다.
따라서 성능적인 면에서 안좋을 수 밖에 없고, 유연하지 못하다. 그래서 대부분의 statement 구문은 DDL문(create, alter, drop ...)에서 주로 사용된다.

statement는 string형태로 sql을 작성하기 때문에 sql 작성이 다른 인터페이스보다 편하고 직관적인 장점이 있다.

<br/>

### 2. preparedStatement

기본적인 사용방법은 다음과 같다.

```java
String sql = "UPDATE classTable SET name = ?, phone = ?, address = ?";
PreparedStatement ps = conn.prepareStatement(sql);
ps.setString(1, "마동석");
ps.setString(2, "010-1234-5678");
ps.setString(3, "서울시");
ResultSet rs = ps.executeQuerey();
```

prepared는 사전적 의미로 '준비된' 이란 뜻이다. 이름에서 알 수 있듯이 준비된 statement이며 여기서 준비 = 컴파일을 말한다. preparedStatement는 statement를 확장한다.

미리 컴파일이 되어 준비하고 있기 때문에 statement에 비해 성능상으로 우위에 있다.
매개변수 전달이 필요한 sql 작성이나, for loop 등으로 여러번 sql을 실행하는 경우에 주로 사용한다.

<br/>

### 3. callableStatement

기본적인 사용방법은 다음과 같다.

```java
int classNumber = 1;

CallableStatement cs = conn.prepareCall("{ call procGetStudents(?, ?, ?) }");
cs.setString(1, classNumber);
cs.registerOutputParameter(2, Types.VARCHAR);
cs.registerOutputParameter(3, Types.VARCHAR);
cs.execute();
ResultSet rs = cs.getResultSet();
```

CallableStatement는 미리 작성해둔 프로시저를 실행하는데 사용한다. CallableStatement는 PreparedStatement를 확장한다.

CallableStatement를 사용할때는 ? 매개변수 자리에 프로시져에서 사용하는 매개변수와, 가져올 값에 대한 셋팅을 동시에 해준다.
프로시져 실행에 필요한 매개변수는 setString, setInt 등으로 해주고, 프로시져 응답으로 받을 값들에 대한 데이터 타입을 registerOutputParameter으로 모두 지정해주어야 한다.

CallableStatement 역시 미리 컴파일되어 DB에 저장되어 있기 때문에 성능상으로 이점이 있다.

<br/>

### 4. JDBC-Postgres 사용 예

```java
package kr.co.test;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class Migration {

	@SuppressWarnings("resource")
	public static void main(String[] args) throws Exception {

		Statement s = null;
		PreparedStatement ps = null;
		ResultSet rs = null;

		try (Connection conn = DriverManager.getConnection("jdbc:postgresql://xxx.xxx.xxx.xxx:5432/wpan", "id", "password")) {

			// 기존 데이터 삭제
			s = conn.createStatement();
			s.addBatch("DELETE FROM tablename");
        	s.executeBatch();

        	// 기존 리스트 가져오기
            List<Map<String, Object>> docList = null;
            ps = conn.prepareStatement("SELECT col1, col2 FROM tablename");
            rs = ps.executeQuery();
            docList = rsConvertor(rs);

            s = conn.createStatement();
            for (int i = 0; i < docList.size(); i++) {
            	if (i > 0 && i % 100 == 0) {
            		s.executeBatch();
            		s = conn.createStatement();
            	};

            	System.out.println("--- " + (i + 1) + "/" + docList.size() + " ---");

            	Map<String, Object> doc = docList.get(i);

				ps = conn.prepareStatement("INSERT INTO tablename(col1, col2) VALUES (?, ?)");
				ps.setString(1, doc.get("col1").toString());
            	ps.setString(2, doc.get("col2").toString());
            	s.addBatch(ps.toString());

            	if (i + 1 == docList.size()) s.executeBatch();
			};
			ps.close();
			s.close();
			conn.close();
        } catch (SQLException e) {
            System.out.println("Connection failure.");
            e.printStackTrace();
        }
	}

	// resultset > list<map> convert
	public static List<Map<String, Object>> rsConvertor(ResultSet rs) throws Exception {
        ResultSetMetaData metaData = rs.getMetaData();
        int sizeOfColumn = metaData.getColumnCount();
        List<Map<String, Object>> list = new ArrayList<>();
        Map<String, Object> map;
        String column;
        while (rs.next()) {
            map = new HashMap<String, Object>();
            for (int indexOfcolumn = 0; indexOfcolumn < sizeOfColumn; indexOfcolumn++) {
                column = metaData.getColumnName(indexOfcolumn + 1);
                map.put(column, rs.getString(column));
            }
            list.add(map);
        }
        return list;
    }
}
```
