---
layout: post
title: Spring Profiles 사용해서 property 파일 개발, 운영 자동연동 설정하기
category: Spring
---

Spring 프레임워크로 개발하다 보면 로컬환경, 개발환경, 운영환경에 따라 DB라던지, url이나 파일 경로등이 각각 달라지는 경우가 있다. 소스코드를 제외한 이런 코드만 바꾸는 방법으로는 여러가지가 있고, 본인은 jenkins로 배포시 script로 property 파일을 수정하는 방법으로 사용해 왔다.

하지만 이 방법은 프로퍼티 파일명이 바뀌거나 경로가 바뀌면 모두 수정해주어야 하고 한동안 안보다가 보면 알아보기 쉽지 않다. 그래서 spring profile을 사용하면서 정리하는 포스팅을 하기로 했다.

### 1. Spring profile이란?

<a href="https://spring.io/understanding/profiles">스프링 공식 홈페이지</a>를 참고하자면, 다음과 같다.<br/>
A Spring ApplicationContext can run in different profiles, defined by the user. You can use profiles to control the contents of the application depending on the environment, for instance to run a different set of beans in a test, or in a cloud platform.

- 애플리케이션을 각개 다른 환경으로 세팅할 수 있고 원하는 환경으로 선택해서 실행할 수 있는 기능
- Spring 3.1 이후부터 등장한 기능이다.
- 주로 애플리케이션의 로컬/개발/운영 환경설정에 자주 쓰인다.

### 1. context-common.xml에 프로퍼티 파일 설정 (3.1 이상)

제일 먼저 property파일 경로를 잡아준다. 본인은 파일명을 active환경명.properties로 했다.

```xml
<beans xmlns:context="http://www.springframework.org/schema/context"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:util="http://www.springframework.org/schema/util"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.1.xsd
       http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
       http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
       http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-3.1.xsd">

<!-- Properties 설정 -->
<util:properties id="config" location="classpath:config/properties/${spring.profiles.active}.properties"/>
```

### 2. web.xml 에 프로필 설정 (3.1 이상)

web.xml설정에는 사용하고자 하는 환경명을 입력한다. 나중에는 이부분의 환경명만 바꾸어주면 1번에서 active환경명을 불러오도록 설정했기 때문에 자동으로 변경된다.

```xml
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns="http://xmlns.jcp.org/xml/ns/javaee"
xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd" version="3.1">

<context-param>
    <param-name>spring.profiles.active</param-name>
    <param-value>infra</param-value> <!-- 이부분에 사용할 프로필 입력 -->
</context-param>
```

### 3. 사용하기

1\. jsp에서 사용하기

```jsp
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<script src="<spring:eval expression="@config['js.path']"/>/jquery-1.7.1.js" type="text/javascript"></script>
```

2\. java에서 사용하기

```java
@Value("#{config['root.path']}") public String RootPath;
```

3\. javascript에서 사용하기

```javascript
<c:set var="MALL_URL">
    <spring:eval expression="@property['MALL.URL.SSO']"/>
</c:set>
var mall_url = '<c:out value="${MALL_URL}" />';
```

4\. xml에서 사용하기

```xml
#{config['db.sso.pw']}
```

5\. java에서 현재 사용중인 spring profile 가져오기

```java
@Autowired
Environment environment;
String profile = environment.getActiveProfiles()[0];
servletContextEvent.getServletContext().getInitParameter("spring.profiles.active"));
```
