---
layout: default
title: Spring Security
parent: Spring Framework
nav_exclude: true
---

# Spring Security

## Security Aspects

- `Confidentiality`
    - privacy of the system
    - accessed by only authorized users (authenticity, identity, authorization)
- `Integrity`
    - consistency and accurate modification of our data, and this requires authorized modification of the data
- `Availability`
    - continuity of the authorized access to the system
    - opposite of availability is *denial of service*

## Spring Security Framework

- comprehensive security solutions for J2EE applications
- provides several configurable servlet filters to provide authentication and authorization
    - `Authentication`
        - process of establishing a principal is who they claim to be 
    - `Authorization`
        - process of deciding whether a principal is allowed to perform an action within your application
- supports a wide range of authentication models
    - HTTP BASIC
    - LDAP
    - Form-based authentication
    - OpenID authentication
    - Authentication based on pre-established request headers 
    - Automatic "remember-me" authentication
    - Anonymous authentication
    - Custom authentication systems

## Outline

![Spring Security Arcchitecture](/img/spring/security-filter-chain.png)

- Spring MVC provides, a special `Servlet` called `DispatcherServlet` which acts as a Front controller and forwards the HTTP request to `@Controllers`, `@RestControllers`, `@Endpoints`.
- Spring Securtity provides, a special `Filter` called `DelegatingFilterProxy`, which configures some security filters
- `DelegatingFilterProxy` is the central servlet filter
- then requests are sent to `FilterChainProxy` filter bean (a `GenericFilterBean` has implemented `Filter` and Bean factory interfaces)
- then requests are sent to `SecurityFilterChain` 
- the filter chain has `RequestMatcher` that apply on `HttpServletRequest` and choses the security filters which are applied sequentially 
- the request flows through the filter chain where these filters known as `Authentication Manager` (responsible for authentication) and then `Access Decision Manager` (responsible for authorization)
- 
![Spring Authentication and Authorization](/img/spring/security-authenticate-authorize.png)

## Modules

- Core (`spring-security-core`): Spring security's core classes and interfaces on authentication and access control reside here.
- Remoting (`spring-security-remoting`): In case you need Spring Remoting
- Aspect (`spring-security-aspects`): Aspect-Oriented Programming support within Spring Security.
- Config (`spring-security-config`): Provides XML and Java configuration support.
- Crypto (`spring-security-crypto`): Contains cryptography support.
- Data (`spring-security-data`): Integration with Spring Data.
- Messaging (`spring-security-messaging`)
- OAuth2: Support for OAuth 2.x support within Spring Security:
- Core (`spring-security-oauth2-core`)
- Client (`spring-security-oauth2-client`)
- JOSE (`spring-security-oauth2-jose`)
- OpenID (`spring-security-openid`): OpenID web-authentication support.
- CAS (`spring-security-cas`): CAS (`Central Authentication Service`) client integration.
- TagLib (`spring-security-taglibs`): Various tag libraries regarding Spring Security.
- Test (`spring-security-test`): Testing support.
- Web (`spring-security-web`): Contains web security infrastructure code, such as various filters and other Servlet API dependencies.
- `spring-ldap`: Simplifying Lightweight Directory Access Protocol (LDAP) programming in Java.
- `spring-security-oauth`: Easy programming with OAuth 1.x and OAuth 2.x protocols.
- `spring-security-saml`: Bringing the SAML 2.0 service provider capabilities to Spring applications.
- `spring-security-kerberos`: Bringing easy integration of Spring application with Kerberos protocol.

## `DelegatingFilterProxy`

- it is a Servlet Filter 
- any request that reaches the web application, this proxy makes sure to delegate the request to Spring Security
- delegates to Spring manages beans
- configured in `web.xml`
- looks for the bean configuration for `FilterChainProxy` with the same filter name (e.g. `springSecuirtyFilterChain` in below config)

```xml
<?xml version="1.0" encoding="UTF-8"?>
 <web-app>
    <filter>
        <filter-name>springSecurityFilterChain</filter-name>
        <filter-class>
            org.springframework.web.filter.DelegatingFilterProxy
        </filter-class>
    </filter>

    <filter-mapping>
        <filter-name>springSecurityFilterChain</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
 </web-app>
```

![DelegatingFilterProxy](/img/spring/delegatingfilterproxy.jpg)

## `FilterChainProxy` 

## SecurityFilterChain

### SecurityContextPersistenceFilter

### HeaderWriterFilter

### CsrfFilter

### LogoutFilter

### UsernamePasswordAuthenticationFilter

### BasicAuthenticationFilter

### RequestCacheAwareFilter

### SecurityContextHolderAwareRequestFilter

### AnonymousAuthenticationFilter

## WebSecurityConfigurerAdapter 

- `configure(AuthenticationManagerBuilder auth)`
    - This method can be overriden to configure application-specific user realms into Spring Security.
- `configure(WebSecurity web)`
    - This method can be overriden to ignore certain requests.
- `configure(HttpSecurity http)`
    - This method can be overriden to configure certain url matches and their filter bindings.
### HttpSecurity

### antMatcher

### requestMatchers

## Reference
- [Spring Security 4.2.x](https://docs.spring.io/spring-security/site/docs/4.2.x/reference/htmlsingle/)