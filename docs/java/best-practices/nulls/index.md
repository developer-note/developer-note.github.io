---
layout: default
title: Working with Nulls
parent: Best Practices
grand_parent: Java
nav_order: 60
has_children: false
---

{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# Working with Nulls

-  Null is a value that indicates that a reference doesn't refer to an object
- literal value of null is Null 
- `NullPointerException` is thrown 
- to avoid `NullPointerException`
  - assertions 
    - enabled by `java –ea ClassName`
    - `java.lang.AssertionError` thrown
  - If/else statements
  - Objects class 
  - try / catch
- do not overcomplicate things

```java
if (Optional.ofNullable(myVariable).isPresent()) // bad
if (Objects.nonNull(myVariable)) // better, but still bad
if (myVariable != null) // good

// Assertion

assert book != null;

// Objects

Objects.requireNonNull(book, "Book is null")

// or 

if( Objects.nonNull (book) )
// do something with book object
}
else{
// book object is null
}

// Try/Catch

try{
// use book object
}
catch (NullPointerException ex) {
// book object was null
}
```

## For data that we don't control

- javadoc comments (describe the contract)    
- always validate parameters at the begining of the method (Fail-Fast)
- check null in Presentation layer, avoid in service or persistance layer
- after detecting a invalid null value 
  - replace with some default value (not preferred always)
  - throw exceptions ( throw the `NullPointerException` or `IllegalArgumentException`) 

## For data that we control

- no need to check in every method
- never pass null as an argument
  - use primitive instead of wrapper classes
  - for optional parameters, overloaded the method with different parameters
- never return null
  - return empty collection
  - Null Object Pattern
  - Optional type

## Using Annotations

- java has no standard annotation to prevent null
- third party libraries - eclipse, Jetbrains, SpotBugs, Checker Framework
- Spring Annotations
  - `org.springframework.lang` package
    - `@NotNull`, `@Nullable`, `@NonNullApi`, `@NonNullFields`
  - provide warings and errors during compile time
- Bean Validation Annotations (Hibernate Validators is the reference)
  - a validator checks if the constraints are satisfied
    - e.g `@NotNull`, `@Size`, `@Min`/ `@Max`, `@PositiveOrZero`

## Null Object Pattern

!['Null Object Pattern'](/img/java/NullObject.png)

- instead of using null reference to represent, use object that implements the expected interface but does nothing
- Stub / Active Nothing
- doen't work like proxy
- State Pattern / Strategy Pattern 
- Disadvantage
  - incorrect implementation can make bugs harder to indentify
  - not easy to create a proper Null Object

```java
String str = "";

Collection collection = Collections.empty();

int n = 0
```

```java
public interface Router {
    void route(Message msg);
}

public class SmsRouter implements Router {
    @Override
    public void route(Message msg) {
        // implementation details
    }
}

public class JmsRouter implements Router {
    @Override
    public void route(Message msg) {
        // implementation details
    }
}

public class NullRouter implements Router {
    @Override
    public void route(Message msg) {
        // do nothing
    }
}

class RouterFactory{
  Router getRouter(String type){
    switch(type){
      case "SMS": return new SmsRouter();
      case "JMS": return new JmsRouter();
      default: return NullRouter():
    }

  }
}
```

## Optionals 

- use composition to use Optional
- use Optional as return value
- similar to stream has methods - `filter()`, `map()`, `flatMap()`
- dont use `Optional.get()` if Optional is empty
- should not use Optional in fields (not serializable)
- should not used in method argument 
- Disadvantage 
  - optional reference may be null
  - can make code harder to read
  - convert Null Pointer Exception into another exception

