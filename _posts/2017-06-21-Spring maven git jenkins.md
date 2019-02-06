---
layout: post
title: Spring + Maven + Git + Jenkins 연동하기
category: Spring
---

Spring으로 개발하고, Github을 사용하면서 Jenkins로 자동 배포를 하는 환경세팅 이다. 그 전에 주의할 점은 DEPLOY 할 대상에 톰캣 webapps 경로에 manager 폴더를 삭제하면 불가능하므로, 만약 삭제했다면 톰캣을 다운받아 manager 폴더만 복사하면 된다.

1\. POM.xml 에 다음 코드를 추가한다.

```xml
<plugin>
    <groupId>org.apache.tomcat.maven</groupId>
    <artifactId>tomcat7-maven-plugin</artifactId>
    <version>2.1</version>
    <configuration>
        <url>http://192.168.x.xxx:8080/manager/text</url> // DEPLOY 할 아이피 주소를 적어주면 됨
        <server>remember</server> // 이건 기억해두어야 한다~
        <path>/context</path> // 컨텍스트 적어주고
    </configuration>
</plugin>
```

2\. DEPLOY 하는 위치의 (POM.xml에 설정한 url) tomcat-users.xml에 추가한다.

```xml
<role rolename="admin"/>
<role rolename="admin-gui"/>
<role rolename="admin-script"/>

<role rolename="manager"/>
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>

<user username="admin" password="admin" roles="admin,manager,admin-gui,admin-script,manager-gui,manager-script,manager-jmx,manager-status"/>
```

3\. 젠킨스가 있는 위치에 메이븐 setting.xml 수정 (/usr/local/maven3/conf/settings.xml)

```xml
<server>
    <id>remember</id> // 위 1번에서 기억해 두라고 한 서버 명을 적는다
    <username>admin</username> // 2번에서 설정한 아이디
    <password>admin</password> // 2번에서 설정한 비밀번호
</server>
```
