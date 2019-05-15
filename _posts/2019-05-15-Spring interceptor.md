---
layout: post
title: Spring Interceptor/스프링 인터셉터를 이용한 세션관리
category: Spring
---

어떤 웹 프로젝트를 진행하건 로그인 기능은 거의 필수로 적용된다. 세션 역시 유효시간을 설정하게 되며 만료된 세션으로는 웹서비스에 접근할 수 없어야 한다. 개발을 시작했을때, 세션체크를 위해 모든 서버로직에 같은 코드를 복사했던게 생각난다. 나와같은 실수를 하는 사람은 없겠지만 혹시모를 누군가를 위해 이번 포스트에서는 Spring framework 기반으로 웹 프로젝트를 진행할때, Spring Interceptor를 이용한 세션/로그인 관리방법에 대해서 알아본다.

### 1. Spring Interceptor 란?

Interceptor의 사전적 의미는 가로채다, 가로막다 인데, 스프링 인터셉터 역시 같은 역할을 하는 것으로 생각하면 된다. 다음 그림을 보면서 이해해보자.

![interceptor]({{"/images/posts/interceptor.png"| relative_url}})
*<a href="https://addio3305.tistory.com/43?category=772645">사진 출처</a>*

위 그림을 보면 빨간색 박스부분에 인터셉터가 위치하게 되는데, 서버에 요청을 하거나 서버에서 응답을 받기 전 가로채는 역할을 한다. 정식 명칭은 핸들러 인터셉터이고, DispatcherServlet이 컨트롤러를 호출하기 전과 후에 요청과 응답을 참조하거나 가공할 수 있는 일종의 필터 역할을 한다.

### 2. 체크 포인트

일반적인 웹 프로젝트에서 세션체크를 해야 하는 곳은 크게 3가지이다.

- 로그인, 로그아웃 관련
- 모든 Ajax를 이용한 request
- 페이지 이동시

### 3. 모든 Ajax 요청에 표시 남기기

```javascript
// ajax 전송 전
$(document).ajaxSend(function(event, request, settings) {
		request.setRequestHeader('AJAX', 'true');
});

// ajax 에러처리
$(document).ajaxError(function(event, request, settings, thrownError) {
  if (request.status == 401 || request.status == 0) {
    alert('세션이 종료 되었습니다. 다시 로그인 해주세요.');
    location.href = contextPath + '/login.jsp';
  } else {
    alert('다음 위치에서 에러가 발생하였습니다. 관리자에게 문의해주세요.\n' + settings.url);
  };
});
```

Front에서 요청하는 모든 ajax요청에 대해 세션 체크를 하기 위해 jQuery 이벤트를 이용한다.
- `ajaxSend`: ajax 전송할때 헤더에 공통적으로 표시를 남긴다. interceptor에서 받은 요청이 ajax 요청인지 판별할 수 있도록 하기 위함이다.
- `ajaxError`: ajax 전송 결과에 따른 공통 대응한다. front에서 받은 응답이 interceptor에서 보낸 응답인지 판별해서 특정 페이지로 돌려보내기 위함이다.

### 4. LoggerInterceptor.java 생성

이제 interceptor를 직접 구현해 보자.

```java
package test.cmm.logger;

import java.util.Random;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;

public class LoggerInterceptor extends HandlerInterceptorAdapter {

	protected Log log = LogFactory.getLog(LoggerInterceptor.class);

	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

		HttpSession session = request.getSession();
		String webRoot = request.getContextPath();
		String usr_id = (String) session.getAttribute("usr_id");
		boolean flag = false;

        try {
        	if (usr_id == null || usr_id.equals("")) {
                if (isAjaxRequest(request)){
                    response.sendError(401);
                    return false;
                } else {
                    response.sendRedirect(webRoot + "/login.jsp");
                    flag = false;
                }
            } else {
                flag = true;
            }
        } catch (Exception e) {
            return false;
        }

		    log.info("> " + usr_id + ": " + request.getRequestURI());
        return flag;
	}

	@Override
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
		log.info("<\n");
	};

	private boolean isAjaxRequest(HttpServletRequest req) {
        String header = req.getHeader("AJAX");
        if ("true".equals(header)) {
        	 return true;
         } else {
        	 return false;
        }
    }

}
```

작성한 소스를 보면 HandlerInterceptorAdapter를 상속받아 preHandler와 postHandler가 override 구현된 것을 알 수 있다.
preHandler는 요청이 서버에 가기 전에, postHandler는 서버에서 응답을 받기 직전에 수행된다.

preHandler에는 세션에서 유저 아이디를 체크해서 유효하면 true를 반환한다.

유효하지 않으면 false를 반환하게 된다. 세션의 유저 아이디가 없으면 `isAjaxRequest(request)` 에서 헤더를 검사한다. 만약 ajax 요청이라면 위에서 헤더에 `'AJAX': true` 를 삽입하도록 구현했으므로 응답에 401에러를 함께 보내고, ajax 요청이 아니라면 login.jsp 페이지로 redirect하게 된다.

### 5. Dispatcher servlet 에 등록하기

위에서 만든 LoggerInterceptor.java를 서블릿에 등록해줄 차례다. 서블릿.xml에 다음을 추가해주면 된다.

```xml
<mvc:interceptors>
  <mvc:interceptor>
    <mvc:mapping path="/**"/>
    <mvc:exclude-mapping path="/logout.wips"/>
    <bean id="loggerInterceptor" class="test.cmm.logger.LoggerInterceptor"></bean>
  </mvc:interceptor>
</mvc:interceptors>
```
