---
layout: post
title: Mybatis CLOB타입을 String으로 mapping하는 방법
category: Mybatis
---

&nbsp;Mybatis + mssql을 사용하면서 프로시져 리턴을 clob 형태로 받기 때문에 Java에서 String형태로 변환하는 과정에 에러가 난 적이 있다.<br/>
&nbsp;이럴 때는 resultMap을 사용해서 맵핑해주면 해결된다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="myfolder">

	<resultMap id="test" type="HashMap" >
		<result property="columnname" column="ab" jdbcType="CLOB" javaType="java.lang.String" />
	</resultMap>

	<select id="queryid" statementType="CALLABLE" parameterType="String" resultMap="test">
		...
	</select>

</mapper>
```
