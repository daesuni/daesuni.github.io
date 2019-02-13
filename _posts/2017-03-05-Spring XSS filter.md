---
layout: post
title: Spring XSS filter 적용하기
category: Spring
---

### 크로스사이트 스크립팅(XSS) 이란?

**사이트 간 스크립팅**(또는 크로스 사이트 스크립팅, 영문 명칭 cross-site scripting, 영문 약어 XSS)은 웹 애플리케이션에서 많이 나타나는 취약점의 하나로 웹사이트 관리자가 아닌 이가 웹 페이지에 악성 스크립트를 삽입할 수 있는 취약점이다.

주로 여러 사용자가 보게 되는 전자 게시판에 악성 스크립트가 담긴 글을 올리는 형태로 이루어진다.

이 취약점은 웹 애플리케이션이 사용자로부터 입력 받은 값을 제대로 검사하지 않고 사용할 경우 나타난다. 이 취약점으로 해커가 사용자의 정보(쿠키, 세션 등)를 탈취하거나, 자동으로 비정상적인 기능을 수행하게 하거나 할 수 있다. 주로 다른 웹사이트와 정보를 교환하는 식으로 작동하므로 사이트 간 스크립팅이라고 한다.

### 크로스사이트 스크립팅(XSS) Filter 설정하기

Spring에서 크로스사이트 스크립팅 필터 설정을 위해선 3가지 설정이 필요하다. 2개의 Java Class와 web.xml 설정이다.

1\. CrossScriptingFilter.java

```java
import java.io.IOException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
public class CrossScriptingFilter implements Filter {
    public FilterConfig filterConfig;
    public void init(FilterConfig filterConfig) throws ServletException {
        this.filterConfig = filterConfig;
    }
    public void destroy() {
        this.filterConfig = null;
    }
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        chain.doFilter(new RequestWrapper((HttpServletRequest) request), response);
    }
}
```

2\. RequestWrapper.java

```java
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;
public class RequestWrapper extends HttpServletRequestWrapper {
    public RequestWrapper(HttpServletRequest servletRequest) {
        super(servletRequest);
    }
    public String[] getParameterValues(String parameter) {
        String[] values = super.getParameterValues(parameter);
        if (values == null) {
            return null;
        }
        int count = values.length;
        String[] encodedValues = new String[count];
        for (int i = 0; i < count; i++) {
            encodedValues[i] = cleanXSS(values[i]);
        }
        return encodedValues;
    }
    public String getParameter(String parameter) {
        String value = super.getParameter(parameter);
        if (value == null) {
            return null;
        }
        return cleanXSS(value);
    }
    public String getHeader(String name) {
        String value = super.getHeader(name);
        if (value == null) {
            return null;
        }
        return cleanXSS(value);
    }
    private String cleanXSS(String value) {
        value = value.replaceAll("<", "& lt;").replaceAll(">", "& gt;");
        value = value.replaceAll("\\(", "& #40;").replaceAll("\\)", "& #41;");
        value = value.replaceAll("'", "& #39;");
        value = value.replaceAll("eval\\((.*)\\)", "");
        value = value.replaceAll("[\\\"\\\'][\\s]*javascript:(.*)[\\\"\\\']", "\"\"");
        value = value.replaceAll("script", "");
        return value;
    }
}
```

3\. web.xml 설정

```xml
<filter>
    <filter-name>XSS</filter-name>
    <filter-class>project.path.CrossScriptingFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>XSS</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```
