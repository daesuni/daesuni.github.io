---
layout: post
title: Postgres 데이터베이스 만들기, 권한부여
---

-	192.168.x.xxx ssh 접속
-	su – postgres
-	psql –U postgres로 쿼리 콘솔 진입
-	1. 유저 생성 : CREATE USER 아이디 WITH PASSWORD '비밀번호';
-	2. 데이터베이스 생성 : CREATE DATABASE 데이터베이스명;
-	3. 권한 부여 : GRANT ALL PRIVILEGES ON DATABASE "데이터베이스명" to 아이디;
