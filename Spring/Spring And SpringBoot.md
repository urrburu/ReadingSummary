# Spring Boot vs. Spring MVC vs. Spring 의 비교

#### 1. 간편한 설정
#### 2. 편리한 의존성 관리 & 자동권장 버전관리
#### 3. 내장 서버로 인한 간단한 배포 서버 구축
#### 4. 스프링 Security, Data JPA등의 다른 스프링 프레임워크 요소를 쉽게 사용


## Spring Boot vs. Spring MVC vs. Spring: How Do They Compare? 

이 기사에서 여러분은 Spring, Spring MVC, Spring Boot 에 대해 간단히 알아보고 그것들이 해결하려는 문제들과 어디에 최적으로 적용되는지를 배울 것이다. 여러분이 배울 가장 중요한 것은 Spring, Spring MVC, Spring Boot 는 중복되는 부분을 다루지 않는다는 것이다. 각각의 모듈들은 서로 다른 문제들을 해결하면서도 그 문제들을 매우 잘 해결한다.
스프링 프레임워크가 해결하는 핵심 문제는 무엇일까?
오래 그리고 심각하게 생각해 보자. 스프링 프레임워크는 어떤 문제를 해결할까?
스프링 프레임워크의 가장 중요한 특징은 의존성 주입(Dependency Injection)이다. 모든 스프링 모듈들의 핵심에는 의존성 주입이나 IOC(Inversion of Control)가 있다.
왜 이것이 중요할까? 

*DI 나 IOC 를 적절히 사용하면, 우리는 느슨하게 결합된 애플리케이션들을 개발할 수 있기 때문이다. 또한 느슨하게 결합된 애플리케이션들은 단위테스트를 하기 쉽다.* 

간단한 예제를 살펴보자
 
의존성 주입이 없는 예제
아래의 예제를 보면, WelcomeController 는 welcome 메시지를 얻기위해 WelcomeService 에 의존적이다. 그러면 WelcomeService 인스턴스를 얻기 위해서는 어떤 작업을 진행할까?

```Java

WelcomeService service = new WelcomeService();
//그것은 WelcomeService 의 인스턴스를 생성하는 것이며, 그것은 그것들이 강하게 결합된다는 것을 의미한다. 
//예를들어 우리가 WelcomeController 의 단위테스트에서 WelcomeService 에 대한 목(mock)을 생성한다면, 
//우리는 어떻게 WelcomeController 가 목을 사용하도록 할 수 있을까? 결코 쉽지 않다. 
@RestController
public class WelcomeController {
    private WelcomeService service = new WelcomeService();
    @RequestMapping("/welcome")
    public String welcome() {
        return service.retrieveWelcomeMessage();
    }
}
```

의존성 주입을 이용한 동일한 예제
의존성 주입을 이용하면, 훨씬 더 단순해 보인다. 여러분은 스프링 프레임워크가 열심히 일하게 만들어야 한다. 우리는 단지 2개의 간단한 애노테이션인 @Component, @Autowired 를 사용한다. 

  -  @Component 를 사용해서 우리는 스프링 프레임워크에게 이렇게 말한다: "스프링 프레임워크야 이것은 너가 관리해야 하는 빈이야"
  -  @Autowired 를 사용해서 우리는 스프링 프레임워크에게 이렇게 말한다: "야 스프링 프레임워크야 이 타입에 맞는 것을 찾아서 그것을 이것과 연결 시켜줘"

 - 아래의 예제에서 스프링 프레임워크는 WelcomeService 인스턴스를 생성하고 WelcomeController 내에 @autowired 가 선언된 곳에 주입한다.

단위테스트시 우리는 스프링 프레임워크에게 WelcomeService 의 목을 WelcomeController 안으로 알아서 주입해 달라고 요청할 수 있다. (스프링 부트는 @MockBean 을 사용해서 이러한 작업을 쉽게 할 수 있도록 해줄 수 있다.)
```Java
@Component
public class WelcomeService {
    //Bla Bla Bla
}
@RestController
public class WelcomeController {
    @Autowired
    private WelcomeService service;
    @RequestMapping("/welcome")
    public String welcome() {
        return service.retrieveWelcomeMessage();
    }
}
```

### 문제1: Duplication/Plumbing Code

스프링 프레임워크는 의존성 주입만 할까? 그렇지 않다 다음과 같은 많은 스프링 모듈들을 이용해서 의존성 주입의 핵심을 구성한다.
   - Spring JDBC
    - Spring MVC
    - Spring AOP
    - Spring ORM
    - Spring JMS
    - Spring Test

Spring JMS와 Spring JDBC 를 생각해 보자. 이 모듈들이 어떤 새로운 기능을 제공하는 걸까? 그렇지 않다 우리는 J2EE 나 Java EE 를 이용해서 이 모듈들이 제공하는 모든 기능들을 할 수 있다. 그렇다면 이 모듈들이 제공하는 것은 무엇일까? 이 모듈들은 단순한 추상(abstraction) 을 제공하는데 이 추상들의 목적은 다음과 같다 
    • 반복 (Boilerplate code) 코드 나 중복코드를 줄임
    • 디커플링을 높이고 단위 테스트성을 증가시킨다.

예를들어 과거의 JDBC 나 JMS 에 비해서 JDBCTemplate 나 JMSTemplate 를 사용하면 훨씬 적은 코드를 작성해도 된다.


### 문제2: 다른 프레임워크들과의 통합

 스프링 프레임워크의 가장 훌륭한 점은 이미 해결된 문제를 해결하려고 시도하지 않는다는 것이다. 스프링 프레임워크가 하는 모든 것은 훌륭한 솔루션을 제공하는 프레임워크들을 훌륭하게 통합해 주는 일이다.
    • Hibernate for ORM
    • iBatis for Object Mapping
    • JUnit and Mockito for Unit Testing

Spring MVC 프레임워크가 해결하는 핵심 문제는 무엇일까? -> 관심사의 분리. 이것은 DI, IOC, AOP의 공통된 목적이자, Spring이 MVC패턴을 사용하는 이유이다. 이것이 분리된 웹 어플리케이션 개발 방법으로 드러난다. Dispatcher Servlet, ModelAndView, View Resolver 과 같은 단순개념을 이용해서 웹 애플리케이션 개발을 쉽게 할 수 있도록 해준다.

# Q. 우리는 왜 Spring Boot (스프링부트) 가 필요할까?
## A. ***스프링 기반의 많은 환경설정과 어플리케이션을 포함한다.***

우리가 Spring MVC 를 사용할 때, 우리는 컴포넌트 스캔, 디스패쳐 서블릿, 뷰 리졸버, 웹 jar등(정적 컨텐츠를 제공하기 위한)을 설정해야 한다.
```xml
  <bean
        class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix">
            <value>/WEB-INF/views/</value>
        </property>
        <property name="suffix">
            <value>.jsp</value>
        </property>
  </bean>
  <mvc:resources mapping="/webjars/**" location="/webjars/"/>
  ```
아래 코드는 웹 애플리케이션에서의 일반적인 디스패쳐 서블릿을 설정을 보여준다.
```xml
<servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>
            org.springframework.web.servlet.DispatcherServlet
        </servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/todo-servlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    ```
우리가 Hibernate/JPA 를 사용할 때는 datasource, entity manager factory, a transaction manager 를 구성해야 한다.
```xml
  <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource"
        destroy-method="close">
        <property name="driverClass" value="${db.driver}" />
        <property name="jdbcUrl" value="${db.url}" />
        <property name="user" value="${db.username}" />
        <property name="password" value="${db.password}" />
    </bean>
    <jdbc:initialize-database data-source="dataSource">
        <jdbc:script location="classpath:config/schema.sql" />
        <jdbc:script location="classpath:config/data.sql" />
    </jdbc:initialize-database>
    <bean
        class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean"
        id="entityManagerFactory">
        <property name="persistenceUnitName" value="hsql_pu" />
        <property name="dataSource" ref="dataSource" />
    </bean>
    <bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
        <property name="entityManagerFactory" ref="entityManagerFactory" />
        <property name="dataSource" ref="dataSource" />
    </bean>
    <tx:annotation-driven transaction-manager="transactionManager"/>
```

Spring Boot에 와서는 이러한 문제들이 자동환경설정을 해주는 방식으로 해결되었다.

### 문제 #1: Spring Boot 자동 환경 설정: 다르게 생각해 볼 수 있을까?

Spring Boot 는 이에 대한 새로운 사고방식을 제시한다.
여기에 더 많은 정보들(intelligence)을 넣을 수 있을까? spring mvc jar 가 애플리케이션에 추가될 때, 우리가 일부 빈들을 자동으로 설정할 수 있을까?
  • Hibernater jar 가 클래스 패스상에 있을 경우 Data Source를 자동으로  구성하는 것은 어떨까?
  
  • Spring MVC jar 가 클래스패스상에 있을 경우  Dispatcher Servlet 을 자동으로 구성하는 것은 어떨까?
	기본적인 자동 구성을 오버라이드 하기위한 대비도 제공될 것이다.
 - 스프링 부트는 클래스패스상에 사용가능한 프레임워크와 이미있는 환경설정을 바라본다. 이것들을 기반으로 스프링 부트는 애플리케이션을 이 프레임워크들과 함께 구성하는데 필요한 기본 환경설정을 제공한다. 이것을 “Auto Configuration” 이라고 부른다.


### 문제 #2: Spring Boot Starter Projects: Built Around Well-Known Patterns
 - 우리가 웹 애플리케이션을 개발하고 싶다고 해보자. 먼저 우리는 사용하고 싶어하는 프레임워크들과 그 프레임워크들의 버전을 선택하고 그것들을 함께 연결할 방법을 찾을 것이다.
모든 웹 애플리케이션들이 이와 유사항 요구사항들을 갖는다. 아래에 있는 dependency 들은 우리가 Spring MVC 코스에서 사용하는 것들이다. 이 종속성들은 Spring MVC, Jackson Databind (데이터 바인딩 용), Hibernate-Validator (Java Validation API 를 이용한 서버사이드 유효성 확인 용), Log4j (로깅용) 이다. 우리가 이 코스를 생성하려면 이 모든 프레임워크들이 호환되는 버전을 선택해야 했다.

```xml
<dependency>
   <groupId>org.springframework</groupId>
   <artifactId>spring-webmvc</artifactId>
   <version>4.2.2.RELEASE</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.5.3</version>
</dependency>
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>5.0.2.Final</version>
</dependency>
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```
​ 스프링 부트 문서에서는 스타터(starter) 들에 대해 아래와 같이 기술한다.
​ 스타터들은 편리한 종속성 기술자들(dependency descriptors)로서 여러분은 이 것을 애플리케이션에 포함시킬 수 있다. 여러분은 모든 스프링과 여러분이 필요로하는 관련 기술을 얻을 수 있는 올인원 쇼핑몰을 얻는 것으로 굳이 샘플코드를 찾아보거나 로드할 종속성 기술자들을 복사/붙여넣기 하지 않아도 된다. 예를들어 여러분이 스프링과 데이터베이스 접근을위한 JPA 를 사용하고 싶다면 여러분의 프로젝트에 spring-boot-starter-data-jpa 종속성을 포함시키고 진행하면 된다.
​ 스타터의 예를(Spring Boot Starter Web) 들어보자

여러분이 웹 애플리케이션이나 레스트풀 서비스들을 노출하기 위한 애플리케이션을 개발하거 싶어한다면 Spring Boot Start Web 을 선택할 수 있다. Spring Initializr 를 이용해서 Spring Boot Starter Web 을 이용하는 프로젝트를 생성하자.
Spring Boot Starter Web 용 종속성

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
아래의 스크린샷은 우리의 애플리케이션에 추가된 또다른 종속성들을 보여준다.

종속성들은 다음과 같이 분류 될 수 있다. 
   • Spring: core, beans, context, aop
   • Web MVC: (Spring MVC)
   • Jackson: for JSON Binding
   • Validation: Hibernate Validator, Validation API
   • Embedded Servlet Container: Tomcat
   • Logging: logback, slf4j
보통 웹 애플리케이션은 위 종속성들을 모두 사용할 것이다. Spring Boot Starter Web 은 미리 이것들을 패키지해 놓은 것을 함께 가져온다. 개발자인 우리는 이 종속성들이나 이것들의 호환버전들에대해 걱정할 필요가 없다.
Spring Boot Starter 프로젝트 옵션들
Spring Boot Starter Web 에서 살펴보았듯이 스타터 프로젝트들은 특정 애플리케이션 개발을 빠르게 시작할 수 있도록 도와 준다.
   • spring-boot-starter-web-services: SOAP Web Services
   • spring-boot-starter-web: Web and RESTful applications
   • spring-boot-starter-test: Unit testing and Integration Testing
   • spring-boot-starter-jdbc: Traditional JDBC
   • spring-boot-starter-hateoas: Add HATEOAS features to your services
   • spring-boot-starter-security: Authentication and Authorization using Spring Security
   • spring-boot-starter-data-jpa: Spring Data JPA with Hibernate
   • spring-boot-starter-cache: Enabling Spring Framework’s caching support
   • spring-boot-starter-data-rest: Expose Simple REST Services using Spring Data REST
Spring Boot 의 다른 목표
개발이나 유지보수에 도움을 주는 몇가지 스타터들이 있는데 다음과 같다.
   • spring-boot-starter-actuator: 여러분의 애플리케이션 추적하거나 모니터링하는 등의 기능을 사용할 수 있도록 한다. 
   • spring-boot-starter-undertow, spring-boot-starter-jetty, spring-boot-starter-tomcat: 내장된 서블릿 컨테이너를 선택할 수 있도록 해준다.
   • spring-boot-starter-logging: logback 을 사용해서 로깅할 수 있도록 해준다.
   • spring-boot-starter-log4j2: Log4j2 를 사용해서 로깅할 수 있도록 해준다.
스프링부트는 빠른시간에 애플리케이션이 제품이 될 수 있도록 하는 것을 목표로 한다.
   • Actuator: 애플리케이션을 고수준에서 모니터링하고 추적 할 수 있도록 해준다.
   • Embedded Server Integrations: 서버가 애플리케이션에 통합되기 때문에 우리는 서버에 설치되는 별도의 애플리케이션 서버를 가질 필요가 없다. (본문에는 별도의 애플리케이션 서버가 필요할 것이라고 되어 있는데 오타 인 것 같다. )
   • Default Error Handling

