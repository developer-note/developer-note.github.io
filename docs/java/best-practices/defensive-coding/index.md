---
layout: default
title: Defensive Coding
parent: Best Practices
grand_parent: Java
nav_order: 50
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

# Defensive Coding

- guarding against errors you don’t expect
- cost of fixing the bug increases from stage to stage  ( Analysis and Design > Dev and Unit test > QA > Prod) 
- the sooner we find the bug, the better
- two ways
  - Reacting early
  - Preventing

# Validating Method Input

## Guard Clause

- Errors should be identified as early as possible
- guard clauses in methods and constructors
- three options 
  - Return early (return false or nothing)
  - Fail fast (throw exception)
  - alternative execution (showing a user friendly message)
- Fail Early
  - place the Guard clauses at the very begining of the method
  - avoid unneccessary computations

## Validating null

- bad examples
  - nested null check
  - null check and then throw exception at the end of the method
- good examples
  - check all bad inputs first in the method then proceed with the safe execution

## Validating Number 

- careful about the range of number (Zero, Postitive or negative, etc)
- careful about the operator ( `&&` and `||` )
- avoid dividing by zero
- avoid zero numerator as well 

## Validating String

- invalid strings (null checking, empty string, string with spaces ) all checks needed, which increase boiler plate code
- refactoring techinque - `Decompose conditional` (extracting into a method)
- REGEX conditions for complex string validations(like email)
- use `Enum` appropriately (provide type safe)
- null safe version of `equals()` e.g. `"String".equals(inputString)`

## Validating Dates

- Don't
  - `java.util.Date` (muttable and not thread safe)
  - don't use it as String
  - don't use Regex to validate
- Do's
  - `java.time` packages - `LocalDate`, `LocalTime`, `Instant` (immutable and thread safe)
  - store date as date and time objects 
  - use `LocalDate.parse()` method to validate (throws `DateTimeParseException`)
- `java.util.Date` requires Defensive copying

## Combinatorial Testing

- watchout for invalid combination of inputs and validate them 

## Class Invariants

- A property that always remains true for all instances of a class no matter what happens.
- the invalid data is handled in the presentation layer and not cascaded into service layer or presentation layer 
- this provides valid and safe data to rest of the system

## Throwing Exceptions

- Dont's
  - thowing `Error`, `Exception`, `RuntimeException` or `Throwable`
- Do's
  - throw Specific exceptions - `IllegalArgumentException`, `IllegalStateException`,  `UnsupportedOperationException`
  - it is ok to throw `NullPointerException` when we verify null parameters and then throw (not the same as catching exception and rethrow)


# Using Frameworks for Validation

## Native Java

- provide `java.util.Objects`
  - `requireNonNull(input)`
  - `requireNonNull(input, message)`
  - `equals()`
  - `deepEquals()`

```java

// throws NPE if null
Objects.requireNonNull(input)

Objects.requireNonNull(input, message)

// it also returns the same value if not null
String name = Objects.requireNonNull(input, message)

```

## Guava and Apache

- Google's Guava
  - `Preconditions` class
    - `checkNotNull()`
    - `checkArgument()` throws `IllegalArgumentException`
    - `checkState()` throw `IllegalStateException`
- Apache Commons
  - `Validate` class
    - `notNull()`
    - `isTrue()`
    - `validState()`
    - `notEmpty()`
    - `noNullElements()`
    - `exclusiveBetween()`
    - `inclusiveBetween()`

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>30.1.1-jre</version>
</dependency>

<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.12.0</version>
</dependency>

```

## Hamcrest & AssertJ

- for Test frameworks
- more readable
- Hamcrest 
  - `MatcherAssert` class
  - `is()`, `anyOf()`, `greaterThan()`, `startsWith()` methods 
- AssertJ (uses fluent interfaces)
  - `Assertions` class


```java
// junit way
assertEquals(2, flightsList.size());

// Hamcrest way
MatcherAssert.assertThat(flightsList.size(), equalTo(2));
MatcherAssert.assertThat(flightsList, hasSize(2)); // empty() for no items

// junit way
assertEquals("Boston", destinations.get(0).getFromDist());
assertEquals("Boston", destinations.get(1).getFromDist());

// Hamcrest way
assertThat(destinations, everyItem(equalTo("Boston")));

// AssertJ way
Assertions.assertThat(flights)
          .isNotEmpty()
          .hasSize(2)
          .doesNotContainNull()
          .flatExtracting(Flight::getFromDest)
          .doesNotContain("Chicago")
          .allMatch(d -> d.equals("Boston"));

```

```xml
<dependency>
    <groupId>org.hamcrest</groupId>
    <artifactId>hamcrest-all</artifactId>
    <version>1.3</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.assertj</groupId>
    <artifactId>assertj-core</artifactId>
    <version>3.20.2</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.8.0-M1</version>
    <scope>test</scope>
</dependency>

```

# Improving Method return values

- do not return magic numbers for success or failure (0 or -1), it forces to learn their meaning
- for do-this-but-may-fail methods, we have some valid return types 
  - Boolean (true/false)
  - void/throw exception (ensure nothing means success)
- don't mix (like method returing - true/false/throw exception)
- avoid returning null, this result in passing null within the application and may break somewhere

## Alternatives to null

- usage of null
  - value for uninitialized fields (neccessary)
  - missing value or result (unneccessary)
- alternatives
  - throwing exceptions
  - Sensible Default (providing a default value)
  - Empty Collections and Arrays
  - Java Optionals 
    - `ofNullable()`, `or`,`orElseGet()`, `orElse()`, `ifPresent()`, `orElseThrow()`, `ifPresentOrElse()`

```java

return new ArrayList<>(flights); // potential empty collection

Collections.emptyList()
Collections.emptySet()
Collections.emptyMap()

```

# Other Defensive Coding practices

- Encapsulation (information and implementaion hiding)
  - only provide getter and setter if required
- Command Query Separation Principle
  - Method does two things
    - returns value
    - change state of something (side effect)
      - e.g. updating variable, writing to file
  - every method should either be a command that performs an action, or a query that returns data to the caller, but not both
  - some exceptions to this principle 
    - Logging
    - intentional design
      - `String e = stack.pop();`
      - `Response r = httpClient.sendPost();`
- Exception Handling
  - use try-with-resources
  - pass useful informations to exceptions (arguments & parent exceptions)
  - don't catch Throwable and Exceptions (it will catch exceptions that are not meant to be catched)
  - don't catch `NullPointerException`, instead fix the code
  - don't catch and swallow exceptions
- Use Static Analysis Tools
