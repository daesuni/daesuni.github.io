---
layout: post
title: Java client IP 가져오기
category: Java
---

### XFF란?

&nbsp;XFF(X-Forwarded-For)는 HTTP 헤더 중 하나로, 원래의 IP 주소를 식별하기 위한 표준 헤더이다.
&nbsp;웹 서버나 WAS 앞에 L4 같은 Load Balancers나 Proxy server(HAProxy), Caching server(Varnish), HTTP 서버용 WAS Connector(웹로직 커넥터- mod_wl, 톰캣 커넥터 -mod_jk 등) 등이 있을 경우 이런 제품들은 웹서버/WAS에 HTTP나 전용 프로토콜(AJP)로 요청을 보낸 후에 받은 결과를 가공하여 클라이언트에 재전송하게 된다.
&nbsp;이로 인해 처리한 웹 서버나 WAS에서 request.getRemoteAddr(); 등으로 클라이언트 IP를 얻을 경우 L4나 Proxy의 IP를 얻게 되는데 이는 원하는 결과가 아니다.
&nbsp;X-Forwarded-For는 이 문제를 해결하기 위해 사용하는 http header 로 squid caching server 에서 처음 사용되었다.
&nbsp;다음과 같이 콤마를 구분자로 client와 proxy IP가 들어가게 되므로 첫번째 IP 를 가져오면 클라이언트를 식별할 수 있다.

```
X-Forwarded-For: client, proxy1, proxy2
```

### WAS에서 사용하기

&nbsp;여기까지 읽고 웹 어플리케이션을 개발할 경우 client ip 를 식별할 필요가 있다면 먼저 저 헤더가 있는지 확인한 후에 없으면 getRemoteAddr()로 IP를 얻으면 되겠지라고 생각할 수도 있겠지만 이게 끝은 아니다.<br/>
&nbsp;XFF 는 사실상의 표준이지 정식 RFC 에 포함된게 아니므로 대개는 착실하게 저 헤더를 사용하지만 엉뚱한 헤더를 사용하는 제품들이 있다.<br/>
&nbsp;그중에 하나인 WebLogic Connector(mod_wl) 는 저 헤더를 사용하지 않고 WL-Proxy-Client-IP 나 Proxy-Client-IP  같은 전혀 엉뚱한 헤더를 사용하므로 만약 만드는 웹 어플리케이션이 WebServer, WAS, L4, proxy 종류에 상관없이 client IP를 잘 가져오기를 바란다면 다음과 같은 순서로 IP를 얻어내야 한다.

```java
String ip = request.getHeader("X-Forwarded-For");
if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
    ip = request.getHeader("Proxy-Client-IP");
}
if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
    ip = request.getHeader("WL-Proxy-Client-IP");
}
if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
    ip = request.getHeader("HTTP_CLIENT_IP");
}
if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
    ip = request.getHeader("HTTP_X_FORWARDED_FOR");
}
if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
    ip = request.getRemoteAddr();
}
```

### NGINX 설정하기

&nbsp;nginx는 --with-http_realip_module 옵션을 주고 컴파일해야 실제 IP를 얻어올 수 있다. 배포판에 포함된 nginx 는 기본 컴파일 옵션일것이라고 생각되며 다음 명령어로 지원 여부를 확인할 수 있다.

```
nginx -V
```

 &nbsp;nginx가 지원한다면 다음 설정을 nginx.conf 에 넣어주면 실제 IP가 로그 파일에 기록된다.

```nginx
http {
set_real_ip_from   127.0.0.1; # proxy/L4 서버 IP
real_ip_header      X-Forwarded-For; #proxy/L4 가 실제 IP 를 설정하는 HTTP Header
```
