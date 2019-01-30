---
layout: post
title: Mybatis-config.xml 설정
category: Mybatis
---

&nbsp;Spring 환경에서 mybatis를 사용할때, query 결과가 NULL이면 ""(공백)으로 받게되는데 이것을 NULL로 그대로 받고싶을 때가 있다.<br/>
&nbsp;이때는 mybatis-config.xml에 설정해주면 해결할 수 있다.

1\. 우선 SqlSessionFactoryBean에 mybatis-config.xml파일을 불러올 수 있도록 맵핑해준다.

```xml
<bean id="SSOSqlSessionBean" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="dataSourceSpiedSSO" />
	<property name="configLocation" value="classpath:/config/mybatis/mybatis-config.xml"></property>
	<property name="mapperLocations" value="classpath:/mapper/sso/*_SQL.xml" />
</bean>
```

2\. 위 맵핑 경로에 mybatis-config.xml를 만들고, callSettersOnNulls를 설정해준다.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
		<settings>
    		<setting name="callSettersOnNulls" value="true"/>
		</settings>
	</configuration>
```

3\. 그 외 기타 mybatis 설정들은 다음과 같다. 여러가지 더 많은 mybatis-config mapper 설정이 있는데 자세한 사항은 mybatis3 공식 홈페이지인 <a href="http://www.mybatis.org/mybatis-3/ko/configuration.html" target="_blank">http://www.mybatis.org/mybatis-3/ko/configuration.html</a>이곳에 자세히 나와있다.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
		<settings>
			<setting name="callSettersOnNulls" value="true"/>
			<setting name="cacheEnabled" value="true"/>
			<setting name="lazyLoadingEnabled" value="true"/>
			<setting name="multipleResultSetsEnabled" value="true"/>
			<setting name="useColumnLabel" value="true"/>
			<setting name="useGeneratedKeys" value="false"/>
			<setting name="autoMappingBehavior" value="PARTIAL"/>
			<setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
			<setting name="defaultExecutorType" value="SIMPLE"/>
			<setting name="defaultStatementTimeout" value="25"/>
			<setting name="defaultFetchSize" value="100"/>
			<setting name="safeRowBoundsEnabled" value="false"/>
			<setting name="mapUnderscoreToCamelCase" value="false"/>
			<setting name="localCacheScope" value="SESSION"/>
			<setting name="jdbcTypeForNull" value="OTHER"/>
			<setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
		</settings>
	</configuration>
```
