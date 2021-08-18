---
layout: default
title: Writing Readable Code
parent: Best Practices
grand_parent: Java
nav_order: 2
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

# Reference

- [Twitter Java style guides](https://github.com/twitter-archive/commons/blob/master/src/java/com/twitter/common/styleguide.md)
- [Google Java Style Guides](https://google.github.io/styleguide/javaguide.html)
- [Oracle Code Conventions](https://www.oracle.com/java/technologies/javase/codeconventions-contents.html)

# Writing Readable Code

## Class

- Convention
  - Should be Noun (Concrete or Abstract) e.g. Calculator, EmailSender
  - Specific
  - obey SRP 
  - Not too long name
- For Refactoring Poorly chosen name
  - Split into different class having Specific names or 
  - Rename if name is not too specific, to a Concrete name
  - E.g. `*Coordinator`/`*Manager` does lost of task so use 
    - Builder
    - Writer
    - Reader
    - Handler
    - Container

## Variable

- Convention
  - Not a single character
  - Always specific
  - 1 or 2 words
  - If Boolean, then should start with 'is'
  - Use camel-case
  - If constant, the ALL CAPS with underscore

## Methods

- When you see the name it should reveal intent
- Name should explain full functionality
- Pattern
  - Verb (Do What?) + Noun (To What? Be More Specific) e.g loadPage(), loadCustomerDetails()
- Anti-pattern
  - If method does more than it says
  - Name containing and, or, if
- Split those methods doing more task into small methods 
  - Exceptions apply to some patterns
    - Static Factory methods
    - Builder 
    - Fluent Interface pattern
- No Abbreviations in name
  - Some global abbreviations are acceptable but better avoid
- Careful with typos & Spelling

## Constructor

- Consider Static factory methods than Constructors. Static Factory class encapsulate the constructor logic
  - Making the constructor private and hide the instantiation logic so that we can focus on our task
  - E.g. Calendar.getInstance()
- Do Chaining constructor for setting multiple fields with default values 
  - One constructor calling another constructor and setting private variables
  - Only one last constructor does the setting
- Anti Pattern
  - Constructor Telescoping
    - Having multiple constructor a Class having lot of fields 
    - Use Builder patter to solve this 

## Methods

- Follow DRY and and dont follow WET
- Aim for Lower Cyclomatic Complexity (CYC)
- Signal : Clean useful code.
- Noise : Poor names, High CYC, duplication, bad comments etc.,
- Don't return Null
  - Leads to NPE or more CYC in caller method
- Don't use special codes like -1, 0, 1 or other codes for exceptions/ negative cases
  - as their meaning cannot be known by other developers
  - Use Proper exceptions
- Not too many arguments 
  - 0 - 2 accepted
  - 3 avoid 
  - 4+ Refactor
    - because More Arguments More Complexity
    - Does too many things
    - Too many primitive types
    - Introduces Boolean flag
      - Split methods like switch(room,flag) - > switchOn(room) and switchOff(room)
- Magic numbers
  - Avoid magic numbers as this affects readability
    - The purpose is not obvious
  - Put it in a variable or constant
- Fail Fast and Fail Safe
  - Fail Fast
    - Immediately report and let the program fail 
    - Check these conditions at the start of the methods
    - Easier trobuleshoot
    - E.g.
      - someInt ==0
      - someString.isEmpty()
      - someList.isEmpty()
      - Objects.isNull()
      - Preconditions.checkArugument() -> from Guava lib
      - ObjectUtils.isNotEmpty() 
  - Fail Safe
    - Try to keep the program running
- After first Fail fast then it should follow DRY principle in methods

## Conditions

- Boolean check
  - Don't be Anti negative 
    - E.g. Don't use !isDoorClosed
    - Use isDoorOpened
- Multiple conditions into one methods for readability
  - E.g. If( day > 6 && day < 12) -> if(isDay())
- Ternary Operator
  - Keep it simple, no nested
  - If nested the use if.. Else.. As it improves readability

## Exceptions

- Don't catch general exceptions
  - Throwable (No)
    - Error(No)
    - Exception(No)
      - Checked Exceptions (Yes)
      - Runtime Exception (No)
        - NPE (No, Avoid in code)
        - Other Runtime Exceptions (Yes)
- Prevent NPE
- Use catch with separator (|) for better readability
  - If we handle differently based on exception then use multiple catch
- No `if` inside catch
- Anti-pattern
  - Catch block should not be 
    - Empty
    - Only comments
    - Unhelpful code e.g. printStacktrace()
    - Not to return null
  - Useful things
    - Logging
    - Rethrow the exception with more detail & meaningful message
- Finally block
  - No exception should be thrown here
    - It will mask the original exception
  - Use try with resources pattern
    - It will close the resources automatically

## Class

- Should be SRP. (one responsibility or one reason to change)
- Should have higher cohesion
  - Cohesion 
    - tendency to unite or degree to which the elements inside the class are related 
    - Fields and methods are co-dependent
    - higher cohesive when methods use other 
      - Methods in the same class
      - Field variables in the same class
    - Cohesion levels
      - Class
      - Package
      - Module
      - System
    - Cohesion != SRP (a class can still be cohesive and does many things)
- Coupling
  - Degree of interdependence or measure of interconnted
  - Two types
    - Tight coupling 
      - More maintenance
    - Loose coupling
      - Adhere SRP
      - Increase Cohesion
      - Program to Interface
      - Strong Encapsulation
      - Uses dependency injection
  - Don't make the methods or fields public for "just because"

## Style conventions

- Code quality benefits
  - Better maintainability and readability
- Refer Google, Twitter & Oracle code style conventions
- Principle of Proximity (it is desirable to keep things that are related together)
  - Top to bottom
- SOLID
  - S : SRP
  - O : Open closed Principle
  - L : Liskov Substitution Principle
  - I : Interface segregation
  - D : Dependency Injection


## Comments

- why we write
  - Helps to understand code
  - Express personal use
  - Disable a functionality
- 80% of the comm
- Bad comments
  - Redundant comments
  - Commpensating comment 
  - Marker comment
  - Commented Code
  - VCS and Wiki comment
  - Lying comment

```java

// Fixed bug ABC-100  ---> VCS and Wiki comment
// TODO 
public BooK getBook{

  int in; // insurance number ---> commpensating comment 

  // looping over books ---> redundant comment 
  for(Book book : books){

  } // end of for loop ---> Marker comment

  // doThat() ---> commented out code

  // returning book name as String ---> Lying comment
  return new Book(); 
}

```

## Tests

- Test Code != Application Code
- Application Code : DRY > DAMP > WET
- Test code : DAMP > DRY
- Don’t Repeat Yourself (DRY) (`how-to`)
  - Application code - (how to implement those specific steps)
- Descriptive and Meaningful Phrases (DAMP)  (`what-to`)
  - Test code (they describe a test scenario)
  - Application code (they describe the use case)
- Write Everything Twice (WET) 
- Each test follow SRP 
  - Verify one thing
  - One assertion per test (some exception are acceptable)
- No if branching
- Benefits of focused tests
  - Fewer points of failure
  - Higher stability
  - Faster failure investigation
  - Overall higher maintainability
- Test pattern
  - AAA 
    - Arrange - setup and initialize the objects and environment
    - Act - exercise the functionality under test
    - Assert - verify
  - BDD
    - `Given` some condition
    - `When` we do some action
    - `Then` expect the result

## Maintaining clean code

- Define rules -> write code -> use static code checker -> submit for code review -> merge

## Static code checker 

- FindBug - outdated
- SonarLint

## Boy scout principle

- Leave the code cleaner than you found it.
