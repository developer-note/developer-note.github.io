---
layout: default
title: Refactoring Code
parent: Best Practices
grand_parent: Java
nav_order: 6
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

# Refactoring Code

- a change made to the internal structure of Software to make it easier to understand and cheaper to modify without changing its observable behaviour
- Refactor first then implement something new
- without refactoring technical debt increases
  - duplicate code increases
  - logic becomes complex
  - code is difficult to understand
- when can we skip technical debt
  - current code doesn't work, fix it than refactoring
  - deadlines must be met, come back later to refactor
  - code is already in good shape, flexible enough and works fine, don't spend over optimizing it

## Code Smell

- a surface indication that usually (not always) corresponds to a deeper problem in the system 

# Standard Refactoring Process

- Verifying the existing behavoiur
- Ensure the test are valid and passing
- Refactor
- Verify the behaviour again

# Bloaters

- Very large methods or Classes that are hard to work with
- accumalates over time as Software evolves 
  - Method Bloaters
    - count the lines 
  - Class Bloaters
    - count the responsiblities  
- different Bloaters
  - Long Parameter List 
  - Long Method
  - Contrived Complexity
  - Primitive Obsession
  - Data Clumps
  - Large Class

## Long Parameters 

- 4 or more needs refactoring
- usually means other bloaters are present, eliminated by fixing them 

## Long Methods

- more than 10 lines needs refactor
- fix by
  - `Extract Methods` (spliting methods by responsibility)
  - `Decompose Conditional` (Spliting methods by on conditions of if or switch statements) 

## Contrieved Complexity

- code that acheives an object but it is too complex even when a shorter, elagant way exists
- fix by `Substitute Algorithms`

## Primitive Obsessions

- over use of primitives instead of Objects
- fix by
  - `Preserve Whole Object` (pass in as whole object than different attributes) 
  - `Introduce Parameter Object` (create an object, move parameters there and then pass)
- difference between them is Class created or not

## Data clumps

- group of variables that are passed around together to various parts of the program
- fix by
  - `Introduce Parameter Object`
  - Combining Entities

## Large Class

- is a class that does many things (many responsiblities)
- fix by 
  - `Extract Class` (slit into smaller class)

# Object-oriented Abusers

- code that doesn't follow the OOPS principles
- different 
  - Conditional complexity
  - Refused Request
  - Temporary Field
  - Alternative classes with different interfaces

## Conditional complexity

- complex switch operator or a sequence of if statements
- mainly occurs due to 
  - missing domain objects
  - not using polymorphism
  - not using inheritance
- fix by
  - Replace by Polymorphism (move the methods where it belongs )
  - Replace by delegation (Strategy design pattern)
  - other methods also there

## Refused Request

- subclass inherits fields and methods that it doesn't need
- bequest (act of giving/leaving personal property by will)
- fix by
  - Rename (rename the field or method so that it makes sense for subclass)
  - Push down (move a method or field to a subclass or new abstract class)

> Note : Inheritance level should not be more than 7

> Favor composition over inheritance

## Temporary Field

- variables that have null most of the time and only has values in certain cases
- indicates low cohesion of a class 
- fix by
  - Extract class 
  - Replace method with method object (Extract class + Move Method ) (a dedicated class for that code)
  - other methods available

> Cohesion : it refers to the degree to which the elements inside a class or a module belong together 

## Alternative classes with different interfaces

- two or more methods exist in multiple classes that does the same thing
- violates DRY principle
- can be considered as a certain type of inheritance misuse (lack of common interface)
- fix by
  - create abstract class or inheritance
  - combine into one (change until both looks same and remove any one, update references to existing one)

# Change Preventers

- change in one place forces change in many other places (makes change difficult)

## Divergent change

- changing several unrelated things within the same class 
- fix by
  - Extract Method
  - Extract Class

## Solution Sprawl and Shotgun surgery

- Solution Sprawl : a solution is broken into multiple classes or places
- Shotgun surgery : an update requires additional changes in multiple classes or modules 
- fix by 
  - combine to one (change until you have a single class with SRP that encapsulate related change)

## Parallel inheritance and hierarchy

- creating one subclass forces you to create another subclass in another tree
- fix by
  - move method (if it doesn't create large class)
  - Visitor or Bridge pattern

# Couplers

- tightly coupled classes
- Tightly coupled : cannot change one with change in another class
- Loosly couples : no or minimum change
- Preventing
  - improve encapsulation
  - apply SRP 
- different 
  - Feature Envy
  - Inappropriate Intimacy
  - Excessive exposure
  - Message chain
  - Middle man

## Feature Envy

- one class uses or access too much data of another class more than its own
- fix by 
  - Move method (to caller method class)
  - Provide a single encapsulated call

## Inappropriate Intimacy

- a class knows too much of internal details of another class
- usage of 
  - public fields instead of getters and setters
  - too many links (method calls)
- fix by 
  - Gradually relax the members visibility when it makes sense

## Excessive exposure

- a class exposes too many details about itself
- it is different to Inappropriate intimacy as this class cares too much about itseld e.g. Calendar, other Factory methods (where we hide the implementations)
- fix by
  - making the client call the interface/abstract parent public method and use factory methods to hide implementation

## Message chain

- code that calls multiple methods to get the required data 
- e.g. customer.getAddress().getCountry().getIsoCode()
- fix by 
  - Encapsulation (move it to the class and keep it as a single point of change, e.g customer.getCountryIso()) 

## Middle man

- a class that performs only one action, which is delegating work to another class
- fix by
  - deleting middleman class and call direclty
- delegating class with a purpose existing in different patterns (delegation is not wrong) 
  - Facade 
  - Proxy
  - Adapter  

# Dispensables

- code that is not needed and can be removed 
- mostly the fix is to remove the code

## Bad comments

- misused or misplaced comments
- it should not compensate for a bad code, refactor to remove the comment
- different 
  - redundant comments
  - version comments
  - misleading, misplace comments
  - commented out code
- fix by
  - Removing
  - if required then add a javadoc to the public method 

## Dead code

- unused code
- don't have the code thinging it may require in future, we can revert using version control later 
- fix by 
  - Removing

## Duplicate Code

- same code written multiple times in several places
- violates DRY
- fix by
  - duplicate method call in a method can be replaced by a local variable
  - duplicate method call in a Class can be replaced by a extract method

## Speculative Generality

- Code created 'just in case' to support anticipated future features that never get implemented
- fix by
  - removing the todo comment sections 
  - Collapse Hierarchy (removing the abstract class created in anticipation of future class may require)

> YAGNI - You Ain't Gonna Need It

## Lazy class and Data Class

- Lazy Class : a class that doesn't have a reason to exist
- Fix by
  - Remove (most cases we can remove)
  - Add Functionality (applies to class)
- Data Class : a class with getter and setter, also may have an explicit constructor. No other methods.
- used to contain data (POJO)
- there are design pattern using Data classes
  - Data Transfer Object pattern (DTO - an object carrying data between two process/subsystem to reduce data calls)
- Fix by
  - remove (not always)

# Tips

- understand before refactoring
- refactor in small steps to avoid defects
- create / use unit tests
- manage the scope of refactoring, don't start a refactoring crusade
- use code reviews
- use IDE and Static Analysis tools

# Refactoring Patterns

- Most commonly used pattern
  - Factory
  - Strategy
  - Adapter
  - Facade
- also widely used
  - Singleton
  - Builder
  - State
  - Decorator

## Important Points

### Object Creation (Creational)

- Fixing No argument Constructor with Dependency injection
  - Flexible +
  - Testable +
  - Complex Client Code x
- Encapsulating with Static Factory methods (Encapsulation)
  - simple client code +
  - Easy to change +
- move creation to a dedicated factory class (SRP )
  - one responsibility
- refactor to Factory Method pattern (OCP)
  - can add new concreate products and factories 

```
Static Factory Method -> Simple Factory -> Factory Method Pattern

```

### Conditional Complexity (Structural)

- Fix low level branching
  - use Guard clauses
  - Extract Conditional
  - use Streams 
- Replace conditional using Polymorphism before introducing Behavioural patterns
  - create a class for each branch with a common method and then create a parent class with that common method, use a facatory to get the correct class
- Refactor to Strategy (Factory method + Context with setter method)
  - no branching + 
  - improved encapsulation + 
  - swap behaviour at runtime +
  - OCP is respected + 
  - complicated design -
  - class combinatorial explosion -
- Replace Strategy with Decorator (when mix and match behaviours)
  - combinatorial explosion +
  - clunky code -
- Replace Strategy with Functional interface (leverage using lambdas)
  - clunky code +
  - some cases only (having less code, short simple classes) -
  
```
Reduce Nestedness -> Replace with Polymorphism -> Strategy Pattern
                                                      |
                                                      -> Functional Approach

```

```
// All uses polymorphism just the intention differs

    Factory                       <>          Strategy              <>        State 
(encapsulate object creation)           (encapsulate behaviour)           (encapsulate state)
```

### Improving Interfaces with Wrappers

- Adapter pattern : wraps an existing class and acts as a connector between two incompatible interfaces
- Decorator pattern : adds additional responsiblities to an object 
  - can be replaced by Functional Composition e.g. `encryt.andThen(compress).apply(new File('f.txt'))`
- Facade pattern : simplify and combines one or more modules 

```
    Adapter                       <>          Decorator                   <>        Facade 
(wraps and changes an interface)        (wraps and adds functionality)           (wraps, unites and simplifies modules)

```
