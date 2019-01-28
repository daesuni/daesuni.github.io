---
layout: post
title: Spring Profiles 사용해서 property 파일 개발, 운영 자동연동 설정하기
---

1\. context-common.xml 에 프로퍼티 파일 설정 (3.1 이상)

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

2\. web.xml 에 프로필 설정 (3.1 이상)

```xml
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns="http://xmlns.jcp.org/xml/ns/javaee"
xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd" version="3.1">

<context-param>
    <param-name>spring.profiles.active</param-name>
    <param-value>infra</param-value> <!-- 이부분에 사용할 프로필 입력 -->
</context-param>
```

3\. 사용하기

3-1\. jsp에서 사용하기

```jsp
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<script src="<spring:eval expression="@config['js.path']"/>/jquery-1.7.1.js" type="text/javascript"></script>
```

3-2\. java에서 사용하기

```java
@Value("#{config['root.path']}") public String RootPath;
```

3-3\. javascript에서 사용하기

```javascript
<c:set var="MALL_URL">
    <spring:eval expression="@property['MALL.URL.SSO']"/>
</c:set>
var mall_url = '<c:out value="${MALL_URL}" />';
```

3-4\. xml에서 사용하기

```xml
#{config['db.sso.pw']}
```

3-5\. java에서 현재 사용중인 spring profile 가져오기

```java
@Autowired
Environment environment;
String profile = environment.getActiveProfiles()[0];
servletContextEvent.getServletContext().getInitParameter("spring.profiles.active"));
```
