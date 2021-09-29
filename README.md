# 전자정부프임워크 Common All-in-one 패키지 JBoss EAP 7 

# archive 배포시 globals.properties 로딩 문제
Common All-in-one 패키지에서 “globals.properies” 로딩 부분은 파일의 절대 경로를 참조한다. 
JBoss EAP 에서 압축된 형태의 애플리케이션을 배포할 경우 VFS 로 관리되며 압축된 패키지의 경우 properties 파일 경로를 VFS의 경로로 설정되어 아래와 같이 파일을 찾지 못하는 현상이 발생한다. 

애플리케이션은 압축 해제된 상태의 애플리케이션을 배포한다.

JBoss EAP 에서 압축된 상태로 배포할 경우 “globals.properties” 를 찾지 못하는 아래와 같은 에러가 한다.

> 전자정부프레임워크 게시판 Q&A <br>
> https://www.egovframe.go.kr/home/qainfo/qainfoRead.do?menuNo=69&qaId=QA_00000000000016651

# Unix 환경에서 Bean 초기화 문제

Unix 환경에서 애플리케이션 배포시 인증 부분에서 에러가 발생한다.
초기 context 로딩시 빈 설정 초기화 순서의 문제로 인증부분에서 NullPointerException 이 발생한다.

전자정부프레임워크 소스중 EgovWebApplicationInitializer.java의 xml 로딩 부분의 수정이 필요하다.

- egovframework.com.cmm.config.EgovWebApplicationInitializer.java
```
//-------------------------------------------------------------
// Spring ServletContextListener 설정
//-------------------------------------------------------------
XmlWebApplicationContext rootContext = new XmlWebApplicationContext();

// rootContext.setConfigLocations(new String[] { "classpath*:egovframework/spring/com/**/context-*.xml" }); // 주석 처리

rootContext.setConfigLocations(new String[] { "classpath*:egovframework/spring/com/context-*.xml","classpath*:egovframework/spring/com/*/context-*.xml" });

rootContext.refresh();
rootContext.start();

```

> 전자정부프레임워크 게시판 Q&A <br>
> https://www.egovframe.go.kr/home/qainfo/qainfoRead.do?menuNo=69&qaId=QA_00000000000017326 <br>
> https://www.egovframe.go.kr/home/qainfo/qainfoRead.do?menuNo=69&qaId=QA_00000000000017320 <br>

# 국제화 환경 설정

애플리케이션의 배포는 정상적으로 되지만 페이지 접속시 국제화 환경 properties 를 찾지 못하는 문제가 발생한다.

EgovWildcardReloadableResourceBundleMessageSource 를 사용할 경우 정상적으로 파일을 로딩하지 못해서 발생하는 문제가 발생한다.
SpringFramework 에서 제공하는 ReloadableResourceBundleMessageSource 변경하고  “WEB-INF/classes/egovframework/message/” 아래 조건에 맞는 리스트는 모두 기록하면 국제화 설정을 사용할 수 있다.

```
<bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
<property name="basenames">
<list>
	<value>classpath:/egovframework/message/com/cmm/err/message-common</value>
	<value>classpath:/egovframework/message/com/cmm/message-common</value> 
	<value>classpath:/egovframework/message/com/message-common</value> 
  ...
  <value>classpath:/egovframework/rte/fdl/idgnr/messages/idgnr</value>
	<value>classpath:/egovframework/rte/fdl/property/messages/properties</value>
	<value>classpath:/egovframework/egovProps/globals</value>
</list>
</property>

<property name="cacheSeconds">
<value>5</value>
</property>
</bean>
```

> 전자정부프레임워크 게시판 Q&A <br>
> https://www.egovframe.go.kr/home/qainfo/qainfoRead.do?menuNo=69&qaId=QA_00000000000018392 <br>
> https://www.egovframe.go.kr/home/qainfo/qainfoRead.do?menuNo=69&qaId=QA_00000000000018383 <br>
> https://www.egovframe.go.kr/home/qainfo/qainfoRead.do?menuNo=69&qaId=QA_00000000000017886
