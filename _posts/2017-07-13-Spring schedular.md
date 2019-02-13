---
layout: post
title: Spring을 이용한 스케쥴러
category: Spring
---

특정 시간마다 DB insert를 실행한다거나, 주기적으로 반복적인 작업을 해야 할 때가 있다. 예를 들면 기상청에서 날씨정보를 매 시간 받아와서 DB에 Insert 해주어야 하는 경우라던지 매일 주식 정보를 가져오는 등의 작업을 말한다.

스프링 프레임워크는 TaskExecutor 인터페이스와 TaskScheduler 인터페이스로 태스크의 비동기 시행과 스케줄링에 대한 추상화를 각각 제공한다. 여기서는 Spring 을 이용한 간단한 배치 생성과 DB 연동까지 해보도록 한다~!

1\. mvc-config.xml : beans 태그를 다음과 같이 변경한다.

```xml
<beans  xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:mvc="http://www.springframework.org/schema/mvc"
        xmlns:context="http://www.springframework.org/schema/context"
        xmlns:task="http://www.springframework.org/schema/task"
        xsi:schemaLocation="http://www.springframework.org/schema/mvc
                            http://www.springframework.org/schema/mvc/spring-mvc.xsd
                            http://www.springframework.org/schema/beans
                            http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/context
                            http://www.springframework.org/schema/context/spring-context.xsd
                            http://www.springframework.org/schema/task
                            http://www.springframework.org/schema/task/spring-task-3.1.xsd">
```

2\. beans 태그 내에 다음 코드를 추가한다.

```xml
<!-- 스케쥴러 파일이 존재하는 패키지 설정 -->
<context:component-scan base-package="com.crontab" />
<!-- 해당 태그들은 크론탭을 설정에 필요한 태그들 -->
<task:scheduler id="jobScheduler" pool-size="10" />
<task:annotation-driven scheduler="jobScheduler" />
```

3\. 위에서 base-package에 설정한 경로 대로 클래스를 생성한다.

스케쥴 설정은 cron, Fixed rate, Fixed Delay 3가지 방식이 있으며, 사용방법은 다음과 같다.

```java
package com.crontab;

import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class Scheduler {

    // 초 분 시 일 월 요일
    @Scheduled(cron = "* * * * * *")
    public void test1(){
        System.out.println("매 초마다 실행");
    }

    @Scheduled(cron="*/5 * * * * MON-FRI")
    public void test2() {
        System.out.println("평일에만 실행");
    }

    // 최초수행시 1초 후, 이전 호출이 완료된 후부터 5초마다 실행
    @Scheduled(fixedDelay=5000, initialDelay = 1000)
    public void test3() {
         System.out.println("FixedDely 실행");
    }

    // 이전 호출 완료에 상관 없이 무조건 5초마다 실행
    @Scheduled(fixedRate=5000)
    public void test4() {
         System.out.println("FixedRate 실행");
    }
}
```

4\. POM.xml에 다음을 추가한다.

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.2.7</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.2.2</version>
</dependency>
<dependency>
    <groupId>commons-dbcp</groupId>
    <artifactId>commons-dbcp</artifactId>
    <version>1.4</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.31</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>${spring-framework.version}</version>
</dependency>
```

5\. resource > spring > application-config.xml에 다음을 추가한다.

```xml
<context:component-scan base-package="com.spring.web"/>
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
        <property name="driverClassName" value="com.mysql.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://localhost:3306/db명" />
        <property name="username" value="아이디" />
        <property name="password" value="패스워드" />
</bean>
<!-- SESSIONFACTORY 설정 -->
 <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!-- ibatis와 다르게 sql디렉토리에 xml 추가만 해주면 별도추가없이 알아서 xml내의 쿼리 인식 -->
        <property name="mapperLocations" value="classpath*:sql/**/*.xml" />
 </bean>
 <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg ref="sqlSessionFactory" />
 </bean>
```

6\. resource 폴더에 sql.xml 파일을 생성하고 다음과 같이 추가한다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="sql">
    <select id="queryTest" resultType="java.util.Map">
        쿼리문
    </select>
</mapper>
```

7\. DAO.java 클래스를 생성한다.

```java
@Repository
public class Dao {

    @Autowired
    private SqlSession sql;

    public String testquery() throws SQLException {
        return sql.selectOne("sql.testquery");
    }
}
```
