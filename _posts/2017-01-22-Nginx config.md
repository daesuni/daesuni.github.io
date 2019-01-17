---
layout: post
title: Nginx config 설정
---

```nginx
server {
	listen 80;
	server_name cluster.dev.wipsmin.com;
	client_max_body_size 100M; // 파일 업로드 용량 제한

	location / {
		allow 111.111.111.0/24;
		deny all;

		// CROSS ORIGIN 설정
		add_header 'Access-Control-Allow-Origin' '*';
		add_header 'Access-Control-Allow-Credentials' 'true';
		add_header 'Access-Control-Allow-Methods' 'GET';
		add_header 'Access-Control-Allow-Methods' 'POST';

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
