# Spring Core Concepts


## Spring framework

- a comprehensive programming and configuration model that reduces the complexity of developing modern Java-based enterprise applications
- key element of Spring is infrastructural support at the application level
- teams can focus on application-level business logic, without unnecessary ties to specific deployment environments

## Modules in Spring framework

![Spring Modules](/img/spring-overview.png)

## Benefits of Using Spring

- targets to make Jakarta EE development easier.
- `Lightweight`
- `Inversion of Control` (IoC) and `Dependency Injection` (DI)- Loosly coupled 
- `Aspect Oriented Programming` (AOP) - separation of business logic from system services
- `IoC container` - manage bean lifecycle
- `MVC framework` for web application
- `Transaction management` of DB, File operations
- `Exception handling` - technology-specific exceptions into consistent, unchecked exceptions

## Inversion of Control

- in traditional programming, where the program flow controls its execution, 
- in the IOC principle, the program delegates the control to something else like a Framework or Container which will drive the flow
- e.g. Strategy design pattern, Service Locator pattern, Factory pattern, and Dependency Injection (DI)
- The advantages of this architecture are:
    - decoupling the execution of a task from its implementation
    - making it easier to switch between different implementations
    - greater modularity of a program
    - greater ease in testing a program by isolating a component or mocking its dependencies, and allowing components to communicate through contracts

## Dependency Injection

- the class is no longer responsible for instantiating the objects it requires. Those responsibilities are passed to independent services.
- Dependency injection involves four roles:
    - the `service` object(s) to be used
    - the `client` object that is depending on the service(s) it uses
    - the `interfaces` that define how the client may use the services
    - the `injector`, which is responsible for constructing the services and injecting them into the client

![Dependency Injection](/img/dependency-injection.jpg)

## Bean Injection

- `Setter Injection` using `<property>` or `@Autowired` on setter method (Optional dependencies)
- `Constructor Injection` using `<constructor-arg>` or `@Autowired` on constructor (Mandatory dependencies)
- `Field Injection` uses `@Autowired` on field without setter using Reflection API, (not recommended) 
- for collection
    - java.util.List - `<list>`
    - java.util.Set - `<set>`
    - java.util.Map - `<map>`
    - java.util.Properties - `<prop>`

    ```xml
    <list>
        <value>1</value>
    </list>

    <set>
        <value>1</value>
    </set>
    
    <map>
        <entry key = "key" value = "Value"/>
    </map>

    <prop>
        <prop key = "key">value</prop>
    </prop>
    ```


## Spring IoC Container

- it implements IoC
- responsible for instantiating, configuring and assembling objects known as `Beans`, as well as managing their life cycles.
- it is represented by the interface `ApplicationContext`
- three implementations of `ApplicationContext`
    - For Standalone application
        - `ClassPathXmlApplicationContext` 
        - `FileSystemXmlApplicationContext`
    - For Web application 
        - `WebApplicationContext` 

## Spring Bean

- Java Objects that are initialized by the Spring IoC container
- Scopes
    - `singleton` (default) - one instance per IoC container
    - `prototype` - many instances per IoC container
    - `request` - one instance per HTTP request lifecycle 
    - `session` - one instance per HTTP session lifecycle
    - `global-session` - one instance per HTTP session lifecycle (in context of Portlet web application)
- beans are not thread safe by default, implementation should take care of thread safety

## Bean Definiton configuration (meta data)

- `XML-Based configuration`

    ```xml
    <bean id="interviewBitBean" class="org.intervuewBit.firstSpring.InterviewBitBean">
        <property name="name" value="InterviewBit"></property>
    </bean>
    ```
- `Annotation-Based configuration`

    ```xml
    <beans>
    <context:annotation-config/>
    </beans>
    ```
- `Java-based configuration`

    ```java
    @Configuration
    public class AppConfig {

        @Bean
        @Scope("prototype")
        public TransferService transferService() {
            return new TransferServiceImpl();
        }

    }
    ```
- in order to maintain the configuration, multiple configuration files is recommended which can be imported (`<import>` or `@Import`) in the main configuration

## Configuration Lifecycle

![Configuration Lifecycle](/img/spring-configuration-lifecycle.png)

## Bean Lifecycle

![Bean Life Cycle](/img/bean-lifecycle-2.jpg)

- Ioc Container instantiate beans from bean definition configuration file.
- populates properties
- `Aware` interfaces are called
    - if `BeanNameAware` interface is implemented, then calls `setBeanName()`
    - if `BeanFactoryAware` interface is implemented, then calls `setBeanFactory()`
    - if `AppliactionContextAware` interface is implemented, then calls `setApplicationContext()`
- If `BeanPostProcessor` is associated with the bean, then their `postProcessBeforeInitialization` is called in order
    - `@PostConstruct`
    - `afterPropertiesSet()` defined in `InitializingBean`
    - custom `init-method` definition
- If `BeanPostProcessor` is associated with the bean, then their `postProcessAfterInitialization` is called
- Bean is ready
- If the `ApplicationContext` is closed, then the destroy methods are called in order
    - `@PreDestroy`
    - `destroy()` defined in `DisposableBean`
    - custom `destroy-method` definiton

![Bean Life Cycle](/img/bean-lifecycle-1.png)

**All Types of Aware** 

![Bean Life Cycle](/img/aware-interfaces.png)

**AppConfig.java**

```java
package com.example;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import com.example.service.Service;

@Configuration
public class AppConfig {

	@Bean(initMethod = "initMethod", destroyMethod = "destroyMethod")
	public Service service() {
		return new Service();
	}
	
	@Bean
	public String description() {
		return "sometext";
	}
}
```

**Service.java**

```java
package com.example.service;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.BeanFactoryAware;
import org.springframework.beans.factory.BeanNameAware;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;

public class Service implements BeanNameAware, BeanFactoryAware, ApplicationContextAware, InitializingBean, DisposableBean{

	public String description;
	
	public String getDescription() {
		return description;
	}
	
	@Autowired
	public void setDescription(String description) {
		System.out.println("setDescription");
		this.description = description;
	}
	
	public Service() {
		System.out.println("Constructor");
	}

	public void setBeanName(String name) {
		System.out.println("setBeanName = " + name);
	}
	
	public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
		System.out.println("setBeanFactory = " + beanFactory.getClass());
	}
	
	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
		System.out.println("setApplicationContext = " + applicationContext.getClass());
	}

	@PostConstruct
	public void postConstruct() {
		System.out.println("postConstruct");
	}

	public void afterPropertiesSet() throws Exception {
		System.out.println("afterPropertiesSet");
	}

	public void initMethod() {
		System.out.println("initMethod");
	}
	
	public void doSomething() {
		System.out.println("do Something " + description);
	}

	@PreDestroy
	public void preDestroy() throws Exception {
		System.out.println("preDestroy");
	}
	
	public void destroy() throws Exception {
		System.out.println("destroy");
	}
	
	public void destroyMethod() {
		System.out.println("destroyMethod");
	}

}
```

**Application.java**

```java
package com.example;

import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import com.example.service.Service;

public class Application {

	public static void main(String[] args) {
		final ConfigurableApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

		context.getBean(Service.class).doSomething();

		context.registerShutdownHook();
	}
}

```
**Console output**
```
Constructor
setDescription
setBeanName = service
setBeanFactory = class org.springframework.beans.factory.support.DefaultListableBeanFactory
setApplicationContext = class org.springframework.context.annotation.AnnotationConfigApplicationContext
postConstruct
afterPropertiesSet
initMethod
do Something sometext
preDestroy
destroy
destroyMethod
```

## Bean wiring

- IoC combines all bean together i.e. tie them together using Dependency Injection
- modes (`autowire=` attribute in `<bean>`)
    - no - default, specify by `ref`
    - `byName` - wires by name of the bean
    - `byType` - wires by class type
    - `constructor` - same as byType but applies to constructor arguments
    - `autodetect` - constructor or byType
-  limitations
    - Overriding possibility - by `property` and `constructor-arg`
    - Data types restriction - for Primitives, Strings and Classes
    - Confusing Nature - hard to autowire when lot of dependencies 
- inner beans which are declared in `property` cannot be reused

## Bean Definition Template

- a child bean definiton inherits configuration from parent bean definition
- child may override or add additional values
- parent bean definition is maked as `abstract="true"` and class attribute is not neccessary, hence it cannot be instantiated

## Annotations 

- rely on the bytecode metadata for wiring up components instead of angle-bracket declarations

## Wiring using Annotations

- `@Resource` - `javax.annotation`
    - ByName
    - ByType
    - ByQualifier (`@Qualifier` in `org.springframework.beans.factory.annotation`)
- `@Autowired` - `org.springframework.beans.factory.annotation`
    - ByType
    - ByQualifier
    - ByName
- `@Inject` - `javax.inject`
    - same as `@Autowired`

## Lifecycle Hooks

- `@PostConstruct`
- `@PreDestroy`

## Sterotype Annotations

- `@Component` - generic sterotype for Spring managed component
- `@Repository` for Data Access Object (DAO)
- `@Service`  
- `@Controller` 

## Autodetect class for bean registration

- done through component scanning 
- can add custom filter for scanning
    - `includeFilters` and `excludeFilters`
    - `include-filter` and `exclude-filter`

**AppConfig.java**

```java
@Configuration
@ComponentScan(basePackages = "org.example",
        includeFilters = @Filter(type = FilterType.REGEX, pattern = ".*Stub.*Repository"),
        excludeFilters = @Filter(Repository.class))
public class AppConfig  {
    ...
}
```

**spring-config.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- No need for <context:annotation-config> explicitly-->
    <context:component-scan base-package="org.example2">
        <context:include-filter type="regex"
                expression=".*Stub.*Repository"/>
        <context:exclude-filter type="annotation"
                expression="org.springframework.stereotype.Repository"/>
    </context:component-scan>
</beans>
```
## AnnotationConfigApplicationContext

- instantiated by `@Configuration` class or `@Component` class having bean definition

```java
ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);

or 

ApplicationContext ctx = new AnnotationConfigApplicationContext(MyServiceImpl.class, Dependency1.class, Dependency2.class);

```

## Injecting inter-bean dependencies

```java
@Configuration
public class AppConfig {

    @Bean
    public Foo foo() {
        return new Foo(bar());
    }

    @Bean
    public Bar bar() {
        return new Bar();
    }
}
```

## @Import annotation

```java
@Configuration
public class ServiceConfig {

    @Bean
    public TransferService transferService(AccountRepository accountRepository) {
        return new TransferServiceImpl(accountRepository);
    }
}

@Configuration
public class RepositoryConfig {

    @Bean
    public AccountRepository accountRepository(DataSource dataSource) {
        return new JdbcAccountRepository(dataSource);
    }
}

@Configuration
@Import({ServiceConfig.class, RepositoryConfig.class})
public class SystemTestConfig {

    @Bean
    public DataSource dataSource() {
        // return new DataSource
    }
}

public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(SystemTestConfig.class);
    // everything wires up across configuration classes...
    TransferService transferService = ctx.getBean(TransferService.class);
    transferService.transfer(100.00, "A123", "C456");
}
```
## Combining Java and XML configuration

### XML-centric use of `@Configuration` classes
    
**AppConfig.java**

```java
@Configuration
public class AppConfig {

    @Autowired
    private DataSource dataSource;

    @Bean
    public AccountRepository accountRepository() {
        return new JdbcAccountRepository(dataSource);
    }

    @Bean
    public TransferService transferService() {
        return new TransferService(accountRepository());
    }
}
```

**system-test-config.xml**

```xml
<beans>
    <!-- enable processing of annotations such as @Autowired and @Configuration -->
    <context:annotation-config/>
    <context:property-placeholder location="classpath:/com/acme/jdbc.properties"/>

    <bean class="com.acme.AppConfig"/>

    <!--
    picks up and registers AppConfig as a bean definition
    <context:component-scan base-package="com.acme"/>
    -->

    <bean class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
</beans>
```

**jdbc.properties**

```
jdbc.url=jdbc:hsqldb:hsql://localhost/xdb
jdbc.username=sa
jdbc.password=

```

**Application.java**

```java
public static void main(String[] args) {
    ApplicationContext ctx = new ClassPathXmlApplicationContext("classpath:/com/acme/system-test-config.xml");
    TransferService transferService = ctx.getBean(TransferService.class);
    // ...
}

```

### `@Configuration` class-centric use of XML with `@ImportResource`

**AppConfig.java**

```java
@Configuration
@ImportResource("classpath:/com/acme/properties-config.xml")
public class AppConfig {

    @Value("${jdbc.url}")
    private String url;

    @Value("${jdbc.username}")
    private String username;

    @Value("${jdbc.password}")
    private String password;

    @Bean
    public DataSource dataSource() {
        return new DriverManagerDataSource(url, username, password);
    }
}
```

**properties-config.xml**

```
<beans>
    <context:property-placeholder location="classpath:/com/acme/jdbc.properties"/>
</beans>
```

**jdbc.properties**

```
jdbc.url=jdbc:hsqldb:hsql://localhost/xdb
jdbc.username=sa
jdbc.password=
```

```java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
    TransferService transferService = ctx.getBean(TransferService.class);
    // ...
}
```

## References

- [`BeanFactory`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/BeanFactory.html) 
- [`ApplicationContext`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationContext.html)
- [`FileSystemXmlApplicationContext`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/support/FileSystemXmlApplicationContext.html)
- [`ClassPathXmlApplicationContext`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/support/ClassPathXmlApplicationContext.html)
- [`GenericApplicationContext`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/support/GenericApplicationContext.html)
- [`GenericGroovyApplicationContext`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/support/GenericGroovyApplicationContext.html)
- [`WebApplicationContext`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/context/WebApplicationContext.html) 
- [`XmlWebApplicationContext`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/context/support/XmlWebApplicationContext.html)
- [`AnnotationConfigApplicationContext`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/AnnotationConfigApplicationContext.html)
- [`AnnotationConfigWebApplicationContext`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/context/support/AnnotationConfigWebApplicationContext.html)
- [`@Configuration`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html)
- [`@Bean`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Bean.html)
- [`@Required`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/annotation/Required.html)
- [`BeanFactoryPostProcessor`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/config/BeanFactoryPostProcessor.html)
- [`BeanPostProcessor`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/config/BeanPostProcessor.html)