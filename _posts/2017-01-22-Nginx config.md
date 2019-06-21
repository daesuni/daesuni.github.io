---
layout: post
title: Nginx config 설정
category: Nginx
keywords:
  - nginx
  - config
  - 설정
---

### Nginx란?

Nginx는 웹 서버 소프트웨어로, 가벼움과 높은 성능을 목표로 한다. 웹 서버, 리버스 프록시 및 메일 프록시 기능을 가진다. Nginx는 트래픽이 많은 웹사이트를 위해 개발된 **비동기 이벤트 기반 웹서버**다.

### Nginx.conf

nginx.conf 파일은 Nginx가 어떻게 동작하는지 설정을 적어놓은 파일이다. 기본적으로 /usr/local/nginx/conf 에 위치한다. 다음 명령으로 conf 파일의 위치를 확인할 수 있다.

```
sudo find / -name nginx.conf
```

### Nginx를 사용할때 자주 발생하는 에러와 설정

1\. 용량이 큰 파일을 업로드할때 http response code가 413을 리턴하거나, 에러메시지로 client intended to send too large body 라는 문구가 발생할때 client_max_body_size 를 설정해주면 해결된다. http블록에 넣어도 무관하다.

```
client_max_body_size 100M;
```

2\. CORS(Cross Origin)문제로 다운로드가 되지 않을때 Access-Control-Allow-Origin 설정을 헤더에 넣어주면 해결된다.

```
add_header 'Access-Control-Allow-Origin' '*';
```

3\. WAS에서 Client IP를 얻기위해 request.getRemoteAddr()를 실행했지만 Client IP가 아닌 Nginx의 IP 혹은 127.0.0.1이 나올때는 헤더에 다음과 같은 설정을 해준다.

```
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header Host $http_host;
```

4\. nginx 504 Gateway Time-out 이라는 에러문구가 나올때는 timeout 설정으로 해결된다.

```
proxy_connect_timeout 300;
proxy_send_timeout 300;
proxy_read_timeout 300;
send_timeout 300;
```

5\. 최종 완성된 샘플은 아래 코드와 같다.

```nginx
server {
	listen 80;
	server_name test.co.kr;
	// 업로드 용량 설정
	client_max_body_size 100M;

	location / {
		allow 111.111.111.0/24;
		deny all;

		// CROSS ORIGIN 설정
		add_header 'Access-Control-Allow-Origin' '*';
		add_header 'Access-Control-Allow-Credentials' 'true';
		add_header 'Access-Control-Allow-Methods' 'GET';
		add_header 'Access-Control-Allow-Methods' 'POST';

		// IP 설정
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header Host $http_host;

		// TIMEOUT 설정
		proxy_connect_timeout 300;
		proxy_send_timeout 300;
		proxy_read_timeout 300;
		send_timeout 300;
		proxy_pass http://111.111.111.111:8080/cluster;

		location /cluster {
			allow 111.111.111.0/24;
			deny all;

			proxy_connect_timeout 300;
			proxy_send_timeout 300;
			proxy_read_timeout 300;
			send_timeout 300;
			proxy_pass http://111.111.111.111:8080;
		}

		error_page  403  /403.html;
		location = /403.html {
		root   html;
		}
	}
}
```
