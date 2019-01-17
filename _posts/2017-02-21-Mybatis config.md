---
layout: post
title: mybatis-config.xml 설정
---

##### SqlSessionFactoryBean에 configLocation.xml mapping하기

```xml
<bean id="SSOSqlSessionBean" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="dataSourceSpiedSSO" />
	<property name="configLocation" value="classpath:/config/mybatis/mybatis-config.xml"></property>
	<property name="mapperLocations" value="classpath:/mapper/sso/*_SQL.xml" />
</bean>
```

##### 해당 경로에 mybatis-config.xml 생성

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">

	<configuration>

		<settings>
    		<setting name="callSettersOnNulls" value="true"/> // 값이 NULL일때, NULL로 받는다
		</settings>

	</configuration>
```
