---
layout: post
title: Mybatis Dynamic SQL 정리
category: Mybatis
---

<select id="getKwdList" parameterType="hashmap" resultType="hashmap">
		SELECT
			row_number() OVER (
				ORDER BY cnt DESC
			) AS i,
			seq,
			LOWER(rword) AS rword,
			LOWER(sword) AS sword,
			cnt
		FROM
			new_srch_frmla_20181108_simil_word
		WHERE
			1 = 1
			<if test="duplicate == 'false'">
				AND removed = false
			</if>
			<if test="search != null and search != ''">
			<choose>
               	<when test="searchField == 'core'">
					AND rword LIKE '%'||UPPER(#{search})||'%'
               	</when>
               	<when test="searchField == 'etc'">
					AND sword LIKE '%'||UPPER(#{search})||'%'
               	</when>
               	<when test="searchField == 'all'">
					AND rword LIKE '%'||UPPER(#{search})||'%'
					OR sword LIKE '%'||UPPER(#{search})||'%'
               	</when>
           	</choose>
		</if>
		OFFSET
			#{startSeq}
		LIMIT
			cast(#{pageSize} as int)
	</select>

Mybatis의 가장 강력한 기능 중 하나는 동적 SQL을 처리하는 방법이다. JDBC나 다른 유사한 프레임워크를 사용해본 경험이 있다면 동적으로 SQL 을 구성하는 것이 얼마나 힘든 작업인지 이해할 것이다. 간혹 공백이나 콤마를 붙이는 것을 잊어본 적도 있을 것이다. 동적 SQL 은 그만큼 어려운 것이다.

동적 SQL 엘리먼트들은 JSTL이나 XML기반의 텍스트 프로세서를 사용해 본 사람에게는 친숙할 것이다. Mybatis의 이전 버전에서는 알고 이해해야 할 엘리먼트가 많았다. Mybatis 3 에서는 이를 크게 개선했고 실제 사용해야 할 엘리먼트가 반 이하로 줄었다. Mybatis의 다른 엘리먼트의 사용을 최대한 제거하기 위해 <a href="https://en.wikipedia.org/wiki/OGNL">OGNL</a> 기반의 표현식을 가져왔다.


if
choose (when, otherwise)
trim (where, set)
foreach
if
동적 SQL 에서 가장 공통적으로 사용되는 것으로 where의 일부로 포함될 수 있다. 예를 들면:

<select id="findActiveBlogWithTitleLike"
     resultType="Blog">
  SELECT * FROM BLOG
  WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
이 구문은 선택적으로 문자열 검색 기능을 제공할 것이다. 만약에 title 값이 없다면 모든 active 상태의 Blog 가 리턴될 것이다. 하지만 title 값이 있다면 그 값과 비슷한 데이터를 찾게 될 것이다.

title과 author를 사용하여 검색하고 싶다면? 먼저 의미가 좀더 잘 전달되도록 구문의 이름을 변경할 것이다. 그리고 다른 조건을 추가한다.

<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
choose, when, otherwise
우리는 종종 적용 할 모든 조건을 원하는 대신에 한가지 경우만을 원할 수 있다. 자바에서는 switch 구문과 유사하며 마이바티스에서는 choose 엘리먼트를 제공한다.

위 예제를 다시 사용해보자. 지금은 title만으로 검색하고 author가 있다면 그 값으로 검색된다. 둘다 제공하지 않는다면 featured 상태의 blog가 리턴된다.

<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
trim, where, set
앞서 예제는 악명높게 다양한 엘리먼트가 사용된 동적 SQL 이다. “if” 예제를 사용해보자.

<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  WHERE
  <if test="state != null">
    state = #{state}
  </if>
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
어떤 조건에도 해당되지 않는다면 어떤 일이 벌어질까? 아마도 다음과 같은 SQL 이 만들어질 것이다.

SELECT * FROM BLOG
WHERE
아마도 이건 실패할 것이다. 두번째 조건에만 해당된다면 무슨 일이 벌어질까? 아마도 다음과 같은 SQL이 만들어질 것이다.

SELECT * FROM BLOG
WHERE
AND title like ‘someTitle’
이것도 아마 실패할 것이다. 이 문제는 조건만 가지고는 해결되지 않았다. 이렇게 작성했다면 다시는 이렇게 작성하지 않게 될 것이다.

실패하지 않기 위해서 조금 수정해야 한다. 조금 수정하면 아마도 다음과 같을 것이다.

<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
where 엘리먼트는 태그에 의해 컨텐츠가 리턴되면 단순히 “WHERE”만을 추가한다. 게다가 컨텐츠가 “AND”나 “OR”로 시작한다면 그 “AND”나 “OR”를 지워버린다.

만약에 where 엘리먼트가 기대한 것처럼 작동하지 않는다면 trim 엘리먼트를 사용자 정의할 수도 있다. 예를들어 다음은 where 엘리먼트에 대한 trim 기능과 동일하다.

<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
override 속성은 오버라이드하는 텍스트의 목록을 제한한다. 결과는 override 속성에 명시된 것들을 지우고 with 속성에 명시된 것을 추가한다.

다음 예제는 동적인 update 구문의 유사한 경우이다. set 엘리먼트는 update 하고자 하는 칼럼을 동적으로 포함시키기 위해 사용될 수 있다. 예를 들어:

<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
여기서 set 엘리먼트는 동적으로 SET 키워드를 붙히고 필요없는 콤마를 제거한다.

아마도 trim 엘리먼트로 처리한다면 아래와 같을 것이다.

<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
이 경우 접두사는 추가하고 접미사를 오버라이딩 한다.

foreach
동적 SQL 에서 공통적으로 필요한 것은 collection 에 대해 반복처리를 하는 것이다. 종종 IN 조건을 사용하게 된다. 예를들면

<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
