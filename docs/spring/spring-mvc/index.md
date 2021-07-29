---
layout: default
title: Spring MVC
parent: Spring Framework
nav_order: 2
has_children: false
---

# Spring MVC 

## Spring Web MVC framework

- Spring web MVC framework is request-driven and designed around DispatcherServlet.
- all incoming requests go through a single servlet - `DispatcherServlet`
(Front controller)

![](/img/spring/RequestLifecycle.png)

## Dispatcher Servlet

-  it's a servlet that takes the incoming request, and delegates processing of that request to one of the handlers (Controllers)

```xml
	<servlet>
		<servlet-name>dispatcher</servlet-name>
		<servlet-class>
			org.springframework.web.servlet.DispatcherServlet
		</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>/WEB-INF/spring/spring-mvc-config.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>

    <!-- mapping can also be provided for a context-->
	<servlet-mapping>
		<servlet-name>dispatcher</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
```

### DispatcherServlet initialization parameters

- `contextClass` 
    - default is `XmlWebApplicationContext`
- `contextConfigLocation` 
    - used by the `contextClass` to load configuration if the file `/WEB-INF/[servlet-name]-servlet.xml` is  absent
- `namespace` 
    - default is `[servlet-name]-servlet`

### Context Hierarchy

![Context Hierarchy](/img/spring/mvc-context-hierarchy.png)

- root should contain all infrastructure beans that should be shared between your other contexts and Servlet instances
- These inherited beans can be overridden in the servlet-specific scope

### Special bean types in the WebApplicationContext

- Beans
    - `HandlerMapping`
    - `HandlerAdapter`
    - `HandlerExceptionResolver`
    - `ViewResolver`
    - `LocalResolver` & `LocalContextResolver`
    - `ThemeResolver`
    - `MultipartResolver`
    - `FlashMapManager`
- default configuration for these beans are available in `DispatcherServlet.properties` in the package `org.springframework.web.servlet`

**Spring 4.3.9 RELEASE**

```properties
# Default implementation classes for DispatcherServlet's strategy interfaces.
# Used as fallback when no matching beans are found in the DispatcherServlet context.
# Not meant to be customized by application developers.

org.springframework.web.servlet.LocaleResolver=org.springframework.web.servlet.i18n.AcceptHeaderLocaleResolver

org.springframework.web.servlet.ThemeResolver=org.springframework.web.servlet.theme.FixedThemeResolver

org.springframework.web.servlet.HandlerMapping=org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping,\
	org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping

org.springframework.web.servlet.HandlerAdapter=org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter,\
	org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter,\
	org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter

org.springframework.web.servlet.HandlerExceptionResolver=org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerExceptionResolver,\
	org.springframework.web.servlet.mvc.annotation.ResponseStatusExceptionResolver,\
	org.springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver

org.springframework.web.servlet.RequestToViewNameTranslator=org.springframework.web.servlet.view.DefaultRequestToViewNameTranslator

org.springframework.web.servlet.ViewResolver=org.springframework.web.servlet.view.InternalResourceViewResolver

org.springframework.web.servlet.FlashMapManager=org.springframework.web.servlet.support.SessionFlashMapManager
```

**Spring 5.3.6 RELEASE**

```properties
# Default implementation classes for DispatcherServlet's strategy interfaces.
# Used as fallback when no matching beans are found in the DispatcherServlet context.
# Not meant to be customized by application developers.

org.springframework.web.servlet.LocaleResolver=org.springframework.web.servlet.i18n.AcceptHeaderLocaleResolver

org.springframework.web.servlet.ThemeResolver=org.springframework.web.servlet.theme.FixedThemeResolver

org.springframework.web.servlet.HandlerMapping=org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping,\
	org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping,\
	org.springframework.web.servlet.function.support.RouterFunctionMapping

org.springframework.web.servlet.HandlerAdapter=org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter,\
	org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter,\
	org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter,\
	org.springframework.web.servlet.function.support.HandlerFunctionAdapter


org.springframework.web.servlet.HandlerExceptionResolver=org.springframework.web.servlet.mvc.method.annotation.ExceptionHandlerExceptionResolver,\
	org.springframework.web.servlet.mvc.annotation.ResponseStatusExceptionResolver,\
	org.springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver

org.springframework.web.servlet.RequestToViewNameTranslator=org.springframework.web.servlet.view.DefaultRequestToViewNameTranslator

org.springframework.web.servlet.ViewResolver=org.springframework.web.servlet.view.InternalResourceViewResolver

org.springframework.web.servlet.FlashMapManager=org.springframework.web.servlet.support.SessionFlashMapManager
```

### DispatcherServlet Processing Sequence

- a request is received by `DispatcherServlet`, then `WebApplicationContext` is bound to it as an attribute, key = `DispatcherServlet.WEB_APPLICATION_CONTEXT_ATTRIBUTE`
- a locale resolver is bound to the request
- a theme resolver is bound to the request
- if a request is found as multipart, then the request is wrapped as `MultipartHttpServletRequest`
- a handler is searched for the request and executed (preprocessor, postprocessor and controller)
- corresponding view is returned for a model
- Handler exception resolver picks up any exception thrown

## Controllers

- `Handler` of the request
- interpret user input and transform it into a model that is represented to the user by the view.
    - `@Controller` 
    - `@RequestMapping`
        - `@GetMapping`
        - `@PostMapping`
        - `@PutMapping`
        - `@DeleteMapping`
        - `@PatchMapping`
    - `@RequestParam`
    - `@RequestBody`
    - `@ModelAttribute`
    - `@ResponseBody`

### URI Template Patterns

- URI-like string, containing one or more variable names e.g. `/owners/{ownerId}`
- use the `@PathVariable` annotation on a method argument to bind it to the value of a URI template variable
- supports the use of regular expressions in URI template variables `{varName:regex}`
- ant style path patterns are supported `/owners/*/pets/{petId}` (see `AntPathMatcher`)

###  Media Types

- `consumes` - matched only if the `Content-Type` request header matches
- `produces` - matched only if the `Accept` request header matches

## Handler Mapping

![MVC Request Flow](/img/spring/RequestLifecycle-2.png)

- maps requested URLs to the handler methods/objects 

```java
package org.springframework.web.servlet;

import javax.servlet.http.HttpServletRequest;

public interface HandlerMapping {
    HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception;
}
```

### Types

- `BeanNameUrlHandlerMapping`
    - handler sees bean name as URI to match 
- `SimpleUrlHandlerMapping`
    - allows for direct and declarative mapping between either bean instances and URLs or between bean names and URLs.
- `ControllerClassNameHandlerMapping`
    - maps URL to a registered controller bean that has, or starts with, the same name
    - removed in Spring 5
- `DefaultAnnotationHandlerMapping`
    - depricated 
- `RequestMappingHandlerMapping`
    - maps methods annotated with `@RequestMapping` (type-level and method-level mappings)

### Configuration

```xml
<!--BeanNameUrlHandlerMapping-->
<bean name="/beanNameUrl" class="org.example.webapp.mvc.controller.WelcomeController" />

<!--SimpleUrlHandlerMapping-->
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="mappings">
        <props>
            <prop key="/simpleUrlWelcome">welcome</prop>
            <prop key="/*/simpleUrlWelcome">welcome</prop>
        </props>
    </property>
</bean>
<bean id="welcome" class="org.example.webapp.mvc.controller.WelcomeController" />

<!--ControllerClassNameHandlerMapping-->
<bean class="org.springframework.web.servlet.mvc.support.ControllerClassNameHandlerMapping" />

<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>
```

## HandlerAdapter

- `HandlerAdapter` is responsible to invoke a handler method and to return the response as `ModelAndView` to the `DispatcherServlet`
- `HandlerMapping` maps a method to a specific URL
- `DispatcherServlet` then uses a `HandlerAdapter` to invoke this method

```java
package org.springframework.web.servlet;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public interface HandlerAdapter {
    boolean supports(Object handler); 
    ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception;
    long getLastModified(HttpServletRequest request, Object handler);
}
```
```java
package org.springframework.web.servlet.mvc;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.servlet.HandlerAdapter;
import org.springframework.web.servlet.ModelAndView;

public class SimpleControllerHandlerAdapter implements HandlerAdapter {

	@Override
	public boolean supports(Object handler) {
		return (handler instanceof Controller);
	}

	@Override
	public ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {

		return ((Controller) handler).handleRequest(request, response);
	}

	@Override
	public long getLastModified(HttpServletRequest request, Object handler) {
		if (handler instanceof LastModified) {
			return ((LastModified) handler).getLastModified(request);
		}
		return -1L;
	}

}
```
```java
package org.springframework.web.servlet.handler;

import javax.servlet.Servlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.servlet.HandlerAdapter;
import org.springframework.web.servlet.ModelAndView;

public class SimpleServletHandlerAdapter implements HandlerAdapter {

	@Override
	public boolean supports(Object handler) {
		return (handler instanceof Servlet);
	}

	@Override
	public ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {

		((Servlet) handler).service(request, response);
		return null;
	}

	@Override
	public long getLastModified(HttpServletRequest request, Object handler) {
		return -1;
	}

}
```
### Types of HandlerAdapter

- `HttpRequestHandlerAdapter`
    - map the requested URL to `handleRequest()`  of `HttpRequestHandler`
    - no view returned
- `SimpleControllerHandlerAdapter`
    - deals with classes implementing `Controller` interface
    - calls `handleRequest()` method in `Controller`
- `SimpleServletHandlerAdapter`
    - forwards the request from `DispatcherServlet` to the appropriate `Servlet` class 
    - calls `service()` method in `Servlet`
- `AnnotationMethodHandlerAdapter` 
    - deprecated from Spring 3.2
    - execute the methods that are annotated with `@RequestMapping` annotation
    - performs method level mapping
    - Handler mapping class is `DefaultAnnotationHandlerMapping` (performs type level mapping)
- `RequestMappingHandlerAdapter`
    - handler mapping class `RequestMappingHandlerMapping`

### Configuration

```xml
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>
    
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"/>
```
alternatively, these two can be done by 

```xml
<mvc:annotation-driven />
```
### Message converters for Handler Adapter

- `RequestMappingHandlerAdapter` convert the request body to the method argument by using an `HttpMessageConverter`
    - `ByteArrayHttpMessageConverter` converts byte arrays.
    - `StringHttpMessageConverter` converts strings.
    - `FormHttpMessageConverter` converts form data to/from a MultiValueMap<String, String>.
    - `SourceHttpMessageConverter` converts to/from a javax.xml.transform.Source

```xml
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
<property name="messageConverters">
    <util:list id="beanList">
        <ref bean="stringHttpMessageConverter"/>
        <ref bean="marshallingHttpMessageConverter"/>
    </util:list>
</property
</bean>

<bean id="stringHttpMessageConverter"
        class="org.springframework.http.converter.StringHttpMessageConverter"/>

<bean id="marshallingHttpMessageConverter"
        class="org.springframework.http.converter.xml.MarshallingHttpMessageConverter">
    <property name="marshaller" ref="castorMarshaller"/>
    <property name="unmarshaller" ref="castorMarshaller"/>
</bean>

<bean id="castorMarshaller" class="org.springframework.oxm.castor.CastorMarshaller"/>
```

alternatively, using `mvc` namespace

```xml
<mvc:annotation-driven>
    <mvc:message-converters>
        <bean class="org.springframework.http.converter.StringHttpMessageConverter"/>
        <bean class="org.springframework.http.converter.ResourceHttpMessageConverter"/>
        <bean class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter"/>
    </mvc:message-converters>
</mvc:annotation-driven>
```

## Handler Interceptor

![Handler Interceptor](/img/spring/RequestLifecycle-3.png)

- to perform actions before handling, after handling, or after completion (when the view is rendered), of a request.
- used to apply some type of processing to the requests 
- 3 methods
    - `prehandle()` – called before the actual handler is executed, but the view is not generated yet
    - `postHandle()` – called after the handler is executed
    - `afterCompletion()` – called after the complete request has finished and view was generated

```java
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.method.HandlerMethod;

public interface HandlerInterceptor {

    boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception;

    void postHandle( HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception;

    void afterCompletion( HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception;
}
```

### HandlerInterceptorAdapter 

- we may implement only required methods instead of all three methods
- deprecated in Spring 5, use `HandlerInterceptor` having default methods

```java
package org.springframework.web.servlet.handler;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.servlet.AsyncHandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

public abstract class HandlerInterceptorAdapter implements AsyncHandlerInterceptor {

	/**
	 * This implementation always returns {@code true}.
	 */
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

		return true;
	}

	/**
	 * This implementation is empty.
	 */
	@Override
	public void postHandle( HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
	}

	/**
	 * This implementation is empty.
	 */
	@Override
	public void afterCompletion( HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
	}

	/**
	 * This implementation is empty.
	 */
	@Override
	public void afterConcurrentHandlingStarted( HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
	}

}
```

### Registering Interceptor

- using `mvc` namespace

```xml
<mvc:interceptors>
    <bean id="loggerInterceptor" class="org.example.webapp.mvc.interceptor.LoggerInterceptor"/>
    <mvc:interceptor>
        <mvc:mapping path="/secure/*"/>
        <bean class="org.example.SecurityInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```
- using `HandlerMapping`
```xml
<bean id="handlerMapping" class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping">
    <property name="interceptors">
        <list>
            <ref bean="loggerInterceptor"/>
        </list>
    </property>
</bean>
<bean id="loggerInterceptor" class="org.example.webapp.mvc.interceptor.LoggerInterceptor"/>

<bean id="urlMapping"
        class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="interceptors">
        <list>
            <ref bean="localeChangeInterceptor"/>
        </list>
    </property>
    <property name="mappings">
        <value>/**/*.view=someController</value>
    </property>
</bean>
```

## View Resolvers

- Spring enables to render the model to browser via `ViewResolver`
- all handler methods must resolve to a logical view name
- explicitly by returning `String`, `View` or `ModelAndView` or implicitly by conventions
- Views are addressed by a logical view name and are resolved by a view resolver

```java
package org.springframework.web.servlet;

import java.util.Locale;

public interface ViewResolver
{
    View resolveViewName(final String p0, final Locale p1) throws Exception;
}
```

### Types

- `AbstractCachingViewResolver`
- `XmlViewResolver`
- `ResourceBundleViewResolver`
- `UrlBasedViewResolver`
- `InternalResourceViewResolver`
- `VelocityViewResolver` / `FreeMarkerViewResolver`
- `ContentNegotiatingViewResolver`

### Configuration

- InternalResourceViewResolver
```xml
<!--InternalResourceViewResolver-->
<bean class = "org.springframework.web.servlet.view.InternalResourceViewResolver">
   <property name = "prefix" value = "/WEB-INF/jsp/"/>
   <property name = "suffix" value = ".jsp"/>
</bean>

```

- XmlViewResolver

```xml
<!-- XmlViewResolver -->
<bean class = "org.springframework.web.servlet.view.XmlViewResolver">
   <property name = "location">
      <value>/WEB-INF/views.xml</value>
   </property>
</bean>
```

**views.xml**

```xml
<bean id = "hello"
   class = "org.springframework.web.servlet.view.JstlView">
   <property name = "url" value = "/WEB-INF/jsp/hello.jsp" />
</bean>
```

- ResourceBundleViewResolver

```xml
<!--ResourceBundleViewResolver-->
<bean class = "org.springframework.web.servlet.view.ResourceBundleViewResolver">
   <property name = "basename" value = "views" />
</bean>
```
**views.properties**

```properties
hello.(class) = org.springframework.web.servlet.view.JstlView
hello.url = /WEB-INF/jsp/hello.jsp
```

### Redirecting to Views

- For `InternalResourceView`, the servlet calls `RequestDispatcher.forward(..)` or `RequestDispatcher.include()`
- `redirect:` prefix tells the `UrlBasedViewResolver` that effectively the controller had returned a `RedirectView`
- For `RedirectView`, the servlet calls `HttpServletResponse.sendRedirect()`
- `forward:` prefix tells the `UrlBasedViewResolver` that effectively the controller had returned a `InternalResourceView`

### Flash Attributes

- a way for one request to store attributes intended for use in another in a `Post/Redirect/Get` pattern
- they are saved in `session` before redirect, and made available in the redirect request and it is removed immediately

## Exception handling 

![Exception Handling Flow](/img/spring/exception-handling-flow-annotation.png)

### HandlerExceptionResolver

- to resolve exceptions thrown during processing an HTTP request.

```java
package org.springframework.web.servlet;

import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpServletRequest;

public interface HandlerExceptionResolver {
	ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex);
}
```
### Types

- `DefaultHandlerExceptionResolver` resolves standard spring exceptions to thier error codes (HTTP 4xx or 5xx)
- `SimpleMappingExceptionResolver` allows to take the class name of any exception that might be thrown and map it to a view name.
- `AnnotationMethodHandlerExceptionResolver`
    - depricated in Spring 3.2
    - used to handle methods annotated with `@ExceptionHandler`
- `ExceptionHandlerExceptionResolver`
    - exception handler methods defined in Controller/ Controller Advice  with `@ExceptionHandler`
- `ResponseStatusExceptionResolver`
    - `@ResponseStatus` annotation available on custom exceptions and to map these exceptions to HTTP status codes.
- custom exception resolvers can extend `AbstractHandlerExceptionResolver`

### Configuration

- `SimpleMappingExceptionResolver`

```xml
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <property name="exceptionMappings">
        <props>
            <prop key="org.example.webapp.mvc.exception.AuthException">
                error/authExceptionView
            </prop>
        </props>
    </property>
    <property name="defaultErrorView" value="error/genericView"/>
</bean>
```

- `ExceptionHandlerExceptionResolver` 

```java

@Controller
public class FileUploadController {
     
    @RequestMapping(value = "/uploadFile", method = RequestMethod.GET)
    public String doFileUpload(@RequestParam int a) throws IOException, SQLException {
         
        // handles file upload stuff...
        if (a == 1) {
            throw new IOException("Could not read upload file.");
        } else if (a == 2) {
            throw new SQLException("Database exception!!!");
        }
         
        return "done";
    }
     
    @ExceptionHandler({IOException.class, java.sql.SQLException.class})
    public ModelAndView handleIOException(Exception ex) {
        ModelAndView model = new ModelAndView("IOError");
 
        model.addObject("exception", ex.getMessage());
         
        return model;
    }
}
```
- `ResponseStatusExceptionResolver`

```java
@ResponseStatus(value = HttpStatus.NOT_FOUND)
public class MyResourceNotFoundException extends RuntimeException {
    public MyResourceNotFoundException() {
        super();
    }
    public MyResourceNotFoundException(String message, Throwable cause) {
        super(message, cause);
    }
    public MyResourceNotFoundException(String message) {
        super(message);
    }
    public MyResourceNotFoundException(Throwable cause) {
        super(cause);
    }
}
```

### Controller Advice

-  implement common processes which are to be executed in multiple Controllers.
    - method with `@InitBinder`
    - method with `@ExceptionHandler`
    - method with `@ModelAttribute`
- based on `AOP` - as Exception Handling is a cross-cutting concern (hint 'Advice' in `@ControllerAdvice`)
- handled by `ExceptionHandlerExceptionResolver`

```java

import java.io.IOException;
import java.sql.SQLException;

import javax.servlet.http.HttpServletRequest;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseStatus;

@ControllerAdvice
public class GlobalExceptionHandler {

	private static final Logger logger = LoggerFactory.getLogger(GlobalExceptionHandler.class);
	
	@ExceptionHandler(SQLException.class)
	public String handleSQLException(HttpServletRequest request, Exception ex){
		logger.info("SQLException Occured:: URL="+request.getRequestURL());
		return "database_error";
	}
	
	@ResponseStatus(value=HttpStatus.NOT_FOUND, reason="IOException occured")
	@ExceptionHandler(IOException.class)
	public void handleIOException(){
		logger.error("IOException handler executed");
		//returning 404 error code
	}
}
```