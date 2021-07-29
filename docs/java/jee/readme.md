---
layout: default
title: Java/Jakarta Enterprise Edition
nav_order: 1
has_children: false
---

# Java/Jakarta Enterprise Edition

- developing and deploying multi-tier web-based enterprise applications
- 4 modules
    - Application Client (Applets, Application client, etc)
    - Web (Java Servlet + Java Server Pages)
    - Business Component (Enterprise JavaBeans)
    - Resource Adapter Module

## Web Container / Servler Container

- manager the lifecycle of Servlets, security, deployment, and threading
- provides 
    - `Communication support` (like ServerSocket, listening on port, creating streams) 
    - `Lifecycle management` of Servlets (loading, instantiating, initializing of Servlets, invoking servlet method)
    - `Multithreading` 
    - `Declarative Security` using Deployment descriptor to configure security
    - `JSP support`  
- e.g. Apache Tomcat GlassFish, JBoss, WebLogic Application Server, IBM Websphere Application Server

## `Servlet`

![Servlet Invocation](/img/java/servlet-invocation.png)

>  *In short, Servlet = Java + HTTP* 

- a web component that is deployed on the server to create Dynamic web page
- receives and responds to requests from Web client
- lifecycle
    - loading
    - instantiation
    - init
    - service
    - destroy

```java
package javax.servlet;

import java.io.IOException;

public interface Servlet {

    public void init(ServletConfig config) throws ServletException;

    public ServletConfig getServletConfig();

    public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException;

    public String getServletInfo();

    public void destroy();
}
```

## `ServletConfig`

- an object created by the web container for each Servlet that has configuration information from Deployment Descriptor (`web.xml`)

```java
package javax.servlet;

import java.util.Enumeration;

public interface ServletConfig {
    
    public String getServletName();

    public ServletContext getServletContext();

    public String getInitParameter(String name);

    public Enumeration<String> getInitParameterNames();
}
```

**Usage**

```xml
<web-app>
    <servlet>
        ...
        <init-param>  
            <param-name>parametername</param-name>  
            <param-value>parametervalue</param-value>  
        </init-param>
    </servlet>
</web-app>  
```

```java
ServletConfig config = getServletConfig();  
String parametervalue = config.getInitParameter("parametername");  
```

## `ServletContext`

- an object that is used to interact with the Servlet container
- one context per "web application" per Java Virtual Machine

```java
//We can get the ServletContext object from ServletConfig object  
ServletContext application = getServletConfig().getServletContext();  
  
//Another convenient way to get the ServletContext object  
ServletContext application = getServletContext();  
```

```xml
<web-app>  
    <context-param>  
        <param-name>parametername</param-name>  
        <param-value>parametervalue</param-value>  
    </context-param>
</web-app>  
```

## ServletConvig Vs ServletContext

|`ServletConfig`|`ServletContext`|
|---------------|----------------|
|Scope is for `Servlet` specific|Scope is for whole application |
|each servlet has its own|only one for all Servlets|
|created during `Servlet` initialization|created at web application deployment|
|destroyed when the servlet is removed| destroyed when application is removed|
|`<init-param>` inside `<servlet>`|`<context-param>` inside `<web-app>`|
|cannot set attributes|can set attributes|

## `HttpServlet`

- an abstract class which provides HTTP specific methods
- extends `GenericServlet`
- override one of the `doX()` method

# Creating a Servlet and mapping url

- three ways to create servlet
    - By implementing the `Servlet` interface
    - By inheriting the `GenericServlet` class
    - By inheriting the `HttpServlet` class

```java
package org.example.webapp.servlet;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class SimpleServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {
		res.setContentType("text/html");
		PrintWriter pw = res.getWriter();// get the stream to write the data

		// writing html in the stream
		pw.println("<html><body>");
		pw.println("Welcome to servlet");
		pw.println("</body></html>");

		pw.close();// closing the stream
	}
}
```
- update the Deployment descriptor `web.xml`
```xml	
<web-app>
	<display-name>Spring Web Application</display-name>

	<servlet>
		<servlet-name>Simple Servlet</servlet-name>
		<servlet-class>org.example.webapp.servlet.SimpleServlet</servlet-class>
	</servlet>

	<servlet-mapping>
		<servlet-name>Simple Servlet</servlet-name>
		<url-pattern>/welcome-servlet</url-pattern>
	</servlet-mapping>
</web-app>
```

## Load on startup

- servlets are loaded when the first request
- `load-on-startup` will load the `web.xml`
- number represents the order, which is ascending from 0 to n

```xml
<servlet>  
    <servlet-name>servlet1</servlet-name>  
    <servlet-class>com.example.FirstServlet</servlet-class>  
    <load-on-startup>0</load-on-startup>  
</servlet>
 <servlet>  
   <servlet-name>servlet2</servlet-name>  
   <servlet-class>com.example.SecondServlet</servlet-class>  
   <load-on-startup>1</load-on-startup>  
</servlet>  
```

## RequestDispatcher

- provides the facility of dispatching the request to another resource it may be html, servlet or jsp. 
- also be used to include the content of another resource

```java

public interface RequestDispatcher {

    public void forward(ServletRequest request, ServletResponse response)
        throws ServletException, IOException;
    public void include(ServletRequest request, ServletResponse response)
        throws ServletException, IOException;
}
```

```java
RequestDispatcher rd=request.getRequestDispatcher("servlet2");  
rd.forward(request, response);  

RequestDispatcher rd=request.getRequestDispatcher("/index.html");  
rd.include(request, response);  
```
## SendRedirect

- used to redirect response to another resource, it may be servlet, jsp or html file 
- relative or absolute url

```java
response.sendRedirect("http://www.google.com"); 
```

## forward() vs sendRedirect()

|`forward()`|`sendRedirect()`|
|-----------|----------------|
|method of `RequestDispatcher`|method of object `HttpServlerResponse`|
|internally forward the request to another servlet or jsp page|redirected to client and it will process the new URL|
|sends same request and response objects to another servlet|sends a new request|
|can work within the server only|can be used within and outside the server|
|End User can see on which page, url is redirected|End User can see on which page, url is redirected|

## sendError()

- sends a status code (usually 404) along with a short message that is automatically formatted inside an HTML document and sent to the client

```java
response.sendError(407, "Need authentication!!!" );
```

## setIntHeader()

- you can make a webpage in such a way that it would refresh automatically after a given interval

```java
response.setIntHeader("Refresh", 5);
```

## Session Tracking

- session means particular interval of time
- sometimes we need to maintain state, but HTTP is stateless (every request is new)
- so we need to manage session 
- 4 techniques
    - `Cookies`
    - `Hidden Form Field`
    - `URL rewriting`
    - `HttpSession`

## Cookie

- a small piece of information that is persisted between the multiple client requests.
- two types
    - Persistant Cookie - cleared when browser closed
    - Non-persistant Cookie - removed only by logout
- `javax.servlet.http.Cookie`

## Cookie management

**Create Cookie**

```java
Cookie ck=new Cookie("user","Sivakumar");//creating cookie object  
response.addCookie(ck);//adding cookie in the response  
```

**Delete Cookie**

```java
Cookie ck=new Cookie("user","");//deleting value of cookie  
ck.setMaxAge(0);//changing the maximum age to 0 seconds  
response.addCookie(ck);//adding cookie in the response 
```

**Access Cookie**
```java
Cookie ck[]=request.getCookies();  
for(int i=0;i<ck.length;i++){  
 out.print("<br>"+ck[i].getName()+" "+ck[i].getValue());//printing name and value of cookie  
}  
```

## HttpSession 

- container creates a session id for each user
- used to 
    - bind objects
    - view and manipulate information about a session
- can be obtained by `HttpServletRequest.getSession()` or `HttpServletRequest.getSession(Boolean createNew)` 

## Deleting Session Data

- `removeAttribute(String name)`
- `invalidate()`
- `setMaxInactiveInterval(int interval)` - return session-timeout property
- using logout Servlet to invalidate session for that user
- session timeout in `web.xml`
    ```xml
    <session-config>
        <session-timeout>15</session-timeout> <!-- default = 30 min-->
    </session-config>
    ```

## Event and Listeners

- two levels of servlet events:

    - Servlet context-level (application-level) event

        This event involves resources or state held at the level of the application servlet context object.

    - Session-level event

        This event involves resources or state associated with the series of requests from a single user session; that is, associated with the HTTP session object.

- Each of these two levels has two event categories:

    - Lifecycle changes
    - Attribute changes

- `ServletContextEvent` and `ServletContextListener`
- `HttpSessionEvent` and `HttpSessionListener`
- `ServletRequestEvent` and `ServletRequestListener`
- `ServletContextAttributeEvent` and `ServletContextAttributeListener`
- `HttpSessionBindingEvent` and `HttpSessionAttributeListener`
- `ServletRequestAttributeEvent` and `ServletRequestAttributeEvent`

```xml
<listener>
        <listener-class>fully.qaulified.path.ContextListener</listener-class>
</listener>
```

## Servlet Filter

- is an object that is invoked at the preprocessing and postprocessing of a request
- Filter API has the following
    - `Filter` - implement this which provides lifecycle#
    - `FilterChain` - responsible to invoke the next filter or resource in the chain
    - `FilterConfig` - configuration object injected by Container
- order of filter-mapping elements in web.xml determines the order in which the web container applies the filter to the servlet.

```xml
<web-app>  
  
    <filter>  
        <filter-name>...</filter-name>  
        <filter-class>...</filter-class>  
    </filter>  
    
    <filter-mapping>  
        <filter-name>...</filter-name>  
        <url-pattern>...</url-pattern>  
    </filter-mapping>  
  
</web-app>  
```    
```java
public interface Filter {
    public void init(FilterConfig filterConfig) throws ServletException;
    public void doFilter(ServletRequest request, ServletResponse response,
                         FilterChain chain) throws IOException, ServletException;
    public void destroy();
 }
```
```java
public interface FilterChain {
    public void doFilter ( ServletRequest request, ServletResponse response ) throws IOException, ServletException;

}
```
```java
public interface FilterConfig {

    public String getFilterName();

    public ServletContext getServletContext();
    
    public String getInitParameter(String name);

    public Enumeration<String> getInitParameterNames();

}
```

## ServletInputStream and ServletOutputStream 

- `ServletInputStream`
    -  provides stream to read binary data such as image etc. from the request object. It is an abstract class.
    - use `getInputStream()` method of `ServletRequest` interface
- `ServletOutputStream`
    - provides a stream to write binary data into the response. It is an abstract class.
    - `getOutputStream()` method of `ServletResponse` interface

## Error Handling

- when error is thrown in Servlet, the web container checks the web.xml to find a match 
- match can be on error-code or exception-type

```xml
<servlet>
   <servlet-name>ErrorHandler</servlet-name>
   <servlet-class>ErrorHandler</servlet-class>
</servlet>

<!-- servlet mappings -->
<servlet-mapping>
   <servlet-name>ErrorHandler</servlet-name>
   <url-pattern>/ErrorHandler</url-pattern>
</servlet-mapping>

<error-page>
   <error-code>404</error-code>
   <location>/ErrorHandler</location>
</error-page>

<error-page>
   <exception-type>java.lang.Throwable</exception-type >
   <location>/ErrorHandler</location>
</error-page>
```

## Servlet with Annotation 

- With the adoption of the version 3.0 of Servlet APIs, the `web.xml` file has become optional
    - `@WebServlet`
    - `@WebInitParam`
    - `@WebFilter`
    - `@WebListener`
    - `@HandlesTypes`
    - `@HttpConstraint`
    - `@HttpMethodConstraint`
    - `@MultipartConfig`
    - `@ServletSecurity`