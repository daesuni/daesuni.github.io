---
layout: post
title: mybatis-config.xml 설정
---

Spring 환경에서 mybatis를 사용할때, query 결과가 NULL이면 ""로 받게되는데 이것을 NULL로 받고싶을 때가 있다.  
이때는 mybatis-config.xml에 설정해주면 해결할 수 있다.

1. 우선 SqlSessionFactoryBean에 mybatis-config.xml파일을 불러올 수 있도록 맵핑해준다.

```xml
<bean id="SSOSqlSessionBean" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="dataSourceSpiedSSO" />
	<property name="configLocation" value="classpath:/config/mybatis/mybatis-config.xml"></property>
	<property name="mapperLocations" value="classpath:/mapper/sso/*_SQL.xml" />
</bean>
```

2. 위 맵핑 경로에 mybatis-config.xml를 만들고, callSettersOnNulls를 설정해준다.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
		<settings>
    		<setting name="callSettersOnNulls" value="true"/> // 값이 NULL일때, NULL로 받는다
		</settings>
	</configuration>
```

3. 추가로 mybatis config

|설정 | 설명 | 사용가능한 값들 | 디폴트 |
| cacheEnabled | 설정에서 각 매퍼에 설정된 캐시를 전역적으로 사용할지 말지에 대한 여부 | true | false | true |
| lazyLoadingEnabled | 지연로딩을 사용할지에 대한 여부. 사용하지 않는다면 모두 즉시 로딩할 것이다. 이 값은 fetchType 속성을 사용해서 대체할 수 있다. | true | false | false |
| aggressiveLazyLoading | 활성화되면 모든 메서드 호출은 객체의 모든 lazy properties 을 로드한다. 그렇지 않으면 각 property 가 필요에 따라 로드된다. (lazyLoadTriggerMethods 참조). | true | false | false (3.4.1 부터 true) |
| multipleResultSetsEnabled | 한개의 구문에서 여러개의 ResultSet을 허용할지의 여부(드라이버가 해당 기능을 지원해야 함) | true | false | true |
| useColumnLabel | 칼럼명 대신에 칼럼라벨을 사용. 드라이버마다 조금 다르게 작동한다. 문서와 간단한 테스트를 통해 실제 기대하는 것처럼 작동하는지 확인해야 한다. | true | false | true |
| useGeneratedKeys | 생성키에 대한 JDBC 지원을 허용. 지원하는 드라이버가 필요하다. true로 설정하면 생성키를 강제로 생성한다. 일부 드라이버(예를들면, Derby)에서는 이 설정을 무시한다. | true | false | False |
| autoMappingBehavior | 마이바티스가 칼럼을 필드/프로퍼티에 자동으로 매핑할지와 방법에 대해 명시. PARTIAL은 간단한 자동매핑만 할뿐 내포된 결과에 대해서는 처리하지 않는다. FULL은 처리가능한 모든 자동매핑을 처리한다. | NONE, PARTIAL, FULL | PARTIAL |
| autoMappingUnknownColumnBehavior | 자동매핑 대상중 알수 없는 칼럼(이나 알수없는 프로퍼티 타입)을 발견했을때 행위를 명시
NONE: 아무것도 하지 않음
WARNING: 경고 로그를 출력('org.apache.ibatis.session.AutoMappingUnknownColumnBehavior'의 로그레벨은 WARN이어야 한다.)
FAILING: 매핑이 실패한다. (SqlSessionException예외를 던진다.) |
| NONE, WARNING, FAILING | NONE |
| defaultExecutorType | 디폴트 실행자(executor) 설정. SIMPLE 실행자는 특별히 하는 것이 없다. REUSE 실행자는 PreparedStatement를 재사용한다. BATCH 실행자는 구문을 재사용하고 수정을 배치처리한다. | SIMPLE REUSE BATCH | SIMPLE |
| defaultStatementTimeout | 데이터베이스로의 응답을 얼마나 오래 기다릴지를 판단하는 타임아웃을 설정 | 양수 | 설정되지 않음(null) |
| defaultFetchSize | 조회결과를 가져올때 가져올 데이터 크기를 제어하는 용도로 드라이버에 힌트를 설정 이 파라미터값은 쿼리 설정으로 변경할 수 있다. | 양수 | 설정하지 않음(null) |
| defaultFetchSize | 결과를 가져오는 크기를 제어하는 힌트처럼 드라이버에 설정한다. 이 파라미터는 쿼리설정으로 변경할 수 있다. | 양수 | 셋팅되지 않음(null) |
| safeRowBoundsEnabled | 중첩구문내 RowBound사용을 허용 허용한다면 false로 설정 | true | false | False |
| safeResultHandlerEnabled | 중첩구문내 ResultHandler사용을 허용 허용한다면 false로 설정 | true | false | True |
| mapUnderscoreToCamelCase | 전통적인 데이터베이스 칼럼명 형태인 A_COLUMN을 CamelCase형태의 자바 프로퍼티명 형태인 aColumn으로 자동으로 매핑하도록 함 | true | false | False |
| localCacheScope | 마이바티스는 순환참조를 막거나 반복된 쿼리의 속도를 높히기 위해 로컬캐시를 사용한다. 디폴트 설정인 SESSION을 사용해서 동일 세션의 모든 쿼리를 캐시한다. localCacheScope=STATEMENT 로 설정하면 로컬 세션은 구문 실행할때만 사용하고 같은 SqlSession에서 두개의 다른 호출사이에는 데이터를 공유하지 않는다. | SESSION | STATEMENT | SESSION |
| jdbcTypeForNull | JDBC타입을 파라미터에 제공하지 않을때 null값을 처리한 JDBC타입을 명시한다. 일부 드라이버는 칼럼의 JDBC타입을 정의하도록 요구하지만 대부분은 NULL, VARCHAR 나 OTHER 처럼 일반적인 값을 사용해서 동작한다. | JdbcType 이늄. 대부분은 NULL, VARCHAR 나 OTHER 를 공통적으로 사용한다. | OTHER |
| lazyLoadTriggerMethods | 지연로딩을 야기하는 객체의 메소드를 명시 | 메소드 이름을 나열하고 여러개일 경우 콤마(,) 로 구분 | equals,clone,hashCode,toString |
| defaultScriptingLanguage | 동적으로 SQL을 만들기 위해 기본적으로 사용하는 언어를 명시 | 타입별칭이나 패키지 경로를 포함한 클래스명 | org.apache.ibatis.scripting.xmltags.XMLLanguageDriver |
| defaultEnumTypeHandler | Enum에 기본적으로 사용되는 TypeHandler 를 지정합니다. (3.4.5 부터) | 타입별칭이나 패키지 경로를 포함한 클래스명 | org.apache.ibatis.type.EnumTypeHandler |
| callSettersOnNulls | 가져온 값이 null일때 setter나 맵의 put 메소드를 호출할지를 명시 Map.keySet() 이나 null값을 초기화할때 유용하다. int, boolean 등과 같은 원시타입은 null을 설정할 수 없다는 점은 알아두면 좋다. | true | false | false |
| returnInstanceForEmptyRow | MyBatis 는 기본적으로 모든 열들의 행이 NULL 이 반환되었을 때 null을 반환한다. 이 설정을 사용하면 MyBatis가 대신 empty 인스턴스를 반환한다. nested results(collection 또는 association) 에도 적용된다. 3.4.2 부터 | true | false | false |
| logPrefix | 마이바티스가 로거(logger) 이름에 추가할 접두사 문자열을 명시 | 문자열 | 설정하지 않음 |
| logImpl | 마이바티스가 사용할 로깅 구현체를 명시 이 설정을 사용하지 않으면 마이바티스가 사용할 로깅 구현체를 자동으로 찾는다. | SLF4J | LOG4J | LOG4J2 | JDK_LOGGING | COMMONS_LOGGING | STDOUT_LOGGING | NO_LOGGING | 설정하지 않음 |
| proxyFactory | 마이바티스가 지연로딩을 처리할 객체를 생성할 때 사용할 프록시 툴을 명시 | CGLIB | JAVASSIST | JAVASSIST (마이바티스 3.3과 이상의 버전) |
| vfsImpl | VFS 구현체를 명시 | 콤마를 사용해서 VFS구현체의 패키지를 포함한 전체 클래스명 |
| useActualParamName | 메소드 시그니처에 명시된 실제 이름으로 구문파라미터를 참조하는 것을 허용 이 기능을 사용하려면 프로젝트를 자바 8의 -parameters옵션을 사용해서 컴파일해야만 한다.(마이바티스 3.4.1이상의 버전) | true | false | true |
| configurationFactory | Configuration 인스턴스를 제공하는 클래스를 지정한다. 반환된 Configuration 인스턴스는 역직렬화 된 객체의 지연로딩 속성들을 불러오는데 사용된다. 이 클래스는 static Configuration getConfiguration() 메서드를 가져야 한다. (3.2.3 부터) | 타입별칭이나 패키지 경로를 포함한 클래스명 | 설정하지 않음
