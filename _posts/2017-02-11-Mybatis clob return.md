---
layout: post
title: Mybatis String이 CLOB으로 return될때
---

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="myfolder">

	<resultMap id="BoardResult" type="HashMap" >
		<result property="ab" column="ab" jdbcType="CLOB" javaType="java.lang.String" />
		<result property="inv_ti" column="inv_ti" jdbcType="CLOB" javaType="java.lang.String" />
	    <result property="exmp_cl" column="exmp_cl" jdbcType="CLOB" javaType="java.lang.String" />
	    <result property="appl_num" column="appl_num" jdbcType="CLOB" javaType="java.lang.String" />
	</resultMap>


	<select id="selectMyfolder" statementType="CALLABLE" parameterType="String" resultMap="BoardResult">

		{
		call IntranetServicedb.wipect.up_cluster_row_data_select_trns_kr (
			#{skeysList,javaType=java.lang.String,jdbcType=NVARCHAR,mode=IN},
			'EXCL,TI,AB',''
			)
		}

	</select>

</mapper>
```
