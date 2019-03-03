---
layout: post
title: Mybatis Dynamic SQL 정리
category: Mybatis
---

Mybatis의 가장 강력한 기능 중 하나는 동적 SQL이다. 동적 SQL은 JSTL이나 XML을 사용해 본 사람이면 익숙할 것이다. Mybatis3 <a href="https://en.wikipedia.org/wiki/OGNL">OGNL</a> 기반의 표현식을 사용한다. 이번 포스트에서는 다음과 같은 동적 SQL들을 살펴본다.

- if
- choose (when, otherwise)
- trim (where, set)
- foreach

포스팅에 예로 자동차 테이블을 만들었고, 모든 행을 select한 결과가 다음과 같았다.

```sql
<select id="getCarList" resultType="Car">
SELECT
	car,
	size,
	fuel,
	color
FROM
	cars
</select>

car			|	size	|	fuel	|	color
------------------------------------------------
말리부		|	중형	|	휘발유	|	블랙
팰리세이드	|	대형	| 	디젤	| 	화이트
코란도		|	준중형	|	디젤	|	실버
아반떼		|	준중형	|	휘발유	|	블랙
K7			|	준대형	|	휘발유	|	블랙
```

### 1. if

if는 다른 언어들의 if와 같다고 생각하면 된다. 만약 자동차 중에 준중형 차량에서 parameter로 넘어온 색상으로 검색하고 싶다면 다음과 같이 사용할 수 있다.

```sql
<select id="findCar" resultType="Car">
SELECT
	*
FROM
	cars
WHERE
	size = '준중형'
	<if test="color != null">
   		OR color = #{color}
	</if>
</select>
```

이 sql은 준중형 차량 or 입력받은 색상의 차량을 검색한다. 만약에 color값이 없다면 준중형 차량만 가져올 것이다. parameter로 연료조건을 추가하려면 if문을 하나 더 추가하면 된다.

```sql
<select id="findCar" resultType="Car">
SELECT
	*
FROM
	cars
WHERE
	size = '준중형'
	<if test="color != null">
   		OR color = #{color}
	</if>
	<if test="fuel != null">
   		OR fuel = #{fuel}
	</if>
</select>
```

### 2. choose, when, otherwise

여러 조건에서 하나를 고르고 싶다면 choose를 사용하면 된다. java의 switch문이라고 생각하면 된다.

```sql
<select id="findCar" resultType="Car">
SELECT
	*
FROM
	cars
WHERE
	size = '준중형'
	<choose>
    <when test="color != null">
      OR color = #{color}
    </when>
    <when test="fuel != null">
      OR fuel = #{fuel}
    </when>
    <otherwise>
      AND color = '블랙'
    </otherwise>
  </choose>
</select>
```

위 sql은 color가 있으면 준중형 or 해당색상인 차를 검색하게된다. color가 없고 fuel이 있으면 준중형 or 해당연료인 차를 검색하며, fuel이 없으면 준중형 and 블랙색상인 차를 검색한다.

### 3. trim, where, set

간혹 sql들 중에 모든 parameter가 있거나 없을 경우가 생긴다. 아래와 같이 if문만 사용해서 sql문을 만들었다고 생각해 보자.

```sql
<select id="findCar" resultType="Car">
SELECT
	*
FROM
	cars
WHERE
	<if test="size != null">
			size = #{size}
	<if test="color != null">
   		OR color = #{color}
	</if>
	<if test="fuel != null">
   		OR fuel = #{fuel}
	</if>
</select>
```

만약에 모든 if조건을 만족하지 못하면 어떤 쿼리가 만들어지냐면 아래와 같은 sql이 완성된다.

```sql
SELECT
	*
FROM
	cars
WHERE
```

또한 size가 없을 경우 아래와 같은 경우도 발생될 수 있다.

```sql
SELECT
	*
FROM
	cars
WHERE
	OR color = '화이트'
```

이런 문제가 생기기 때문에 보통 편법을 많이 사용한다. where조건의 맨 앞에 무조건 참이되는 조건을 달아주는 방식이다.

```sql
<select id="findCar" resultType="Car">
SELECT
	*
FROM
	cars
WHERE
	1 = 1
	<if test="size != null">
			OR size = #{size}
	<if test="color != null">
   		OR color = #{color}
	</if>
	<if test="fuel != null">
   		OR fuel = #{fuel}
	</if>
</select>
```

WHERE절의 첫번째 조건에 `1 = 1`이라는 무조건 참인 경우를 달아줌으로써 뒤에 어떠한 경우가 오던 잘못된 쿼리가 만들어지지 않게 해준다.

```sql
SELECT
	*
FROM
	cars
WHERE
	1 = 1
```

`1 = 1`조건과 같은 기능을 하는 것이 where문이다.

```sql
<select id="findCar" resultType="Car">
SELECT
	*
FROM
	cars
<where>
	<if test="size != null">
			OR size = #{size}
	<if test="color != null">
   		OR color = #{color}
	</if>
	<if test="fuel != null">
   		OR fuel = #{fuel}
	</if>
</where>
</select>
```

where문은 내부에 조건이 있으면 sql에 WHERE을 추가해준다. 또한 내부 조건이 AND나 OR로 시작하면 그 AND나 OR를 지워준다. 같은 기능을 하는 것 중에 trim도 있다.

```sql
<select id="findCar" resultType="Car">
SELECT
	*
FROM
	cars
<trim prefix="WHERE" prefixOverrides="AND|OR">
	<if test="size != null">
			OR size = #{size}
	<if test="color != null">
   		OR color = #{color}
	</if>
	<if test="fuel != null">
   		OR fuel = #{fuel}
	</if>
</trim>
</select>
```

위와같이 작성하면, 내부 조건의 맨 앞에 AND나 OR를 WHERE으로 바꿔주는 역할을 한다. 즉, `prefixOverrides`에 입력한 텍스트를 `prefix`로 바꿔주는 것이다.

set역시 where와 유사한 기능을 한다.

```sql
<update id="updateCar">
  UPDATE cars
    <set>
      <if test="size != null">size = #{size},</if>
      <if test="fuel != null">fuel = #{fuel},</if>
      <if test="color != null">color = #{color}</if>
    </set>
  where car = '코란도'
</update>
```

여기서 set은 동적으로 SET을 붙혀주고 필요없는 콤마를 제거해 준다.

### 4. foreach

동적 sql에서 foreach문 역시 필수적인 것으로 배치처리를 할때도 쓰이는데, 일반적 for문이라고 생각하면 된다.

```sql
<insert id="addCar">
	INSERT INTO car (
		car, size, fuel, color
	) VALUES
	<foreach collection="list" item="item" separator=" , ">
      (#{item.car}, #{item.size}, #{item.fuel}, #{item.color})
	</foreach>
</insert>
```
