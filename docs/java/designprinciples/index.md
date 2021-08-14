---
layout: default
title: Design Prinicples
parent: Java
nav_order: 10
has_children: false
---

# Design Prinicples

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

# Design Prinicples

> It is not enough for the code to work

- a set of guidelines that helps us to avoid having a bad design.
-  3 important characteristics of a bad design that should be avoided
    - Rigidity (one change affects too many parts of the system)
    - Fragility (one change unexpected parts of the system break)
    - Immobility (hard to use in another application)

# Technical debt

- cost of prioritizing fast delivery over code quality for longer period of time
- it is ok to chose Fast delivery over Code quality but always chosing leads to high technical debt
- always pay techinical debt
    - `Cost of Change` increases over the years
    - `Customer Responsiveness` decreases over the years

# SOLID

- first five object-oriented design (OOD) principles
    - S - Single-responsiblity Principle
    - O - Open-closed Principle
    - L - Liskov Substitution Principle
    - I - Interface Segregation Principle
    - D - Dependency Inversion Principle
- mostly effective when they applied together
- Other ways to maintain the clean clode
    - Continous Code refactoring
    - Unit testing (TDD)
    - Design pattern

## Single Responsibility Principle (SRP)

> Every function, class or Module should have one, and only one, reason to change.

- ask the question "What is the responsibility of your class/component/microservice?"

### Benefits 
- code easier to understand, fix and maintain
- class becomes less coupled and more resilient to change
- more testable design

### Symptoms / Identifying multiple reasons to change (few examples)
- if / Switch statements
- big methods/ different class and object references
- Helper classes 
- same features that are used by multiple people (Actors)

### Refactoring
- break the big methods based on the responsiblity (logging, exception handling at the caller, persistance, orchestration, user interface, business logic, users)
- create necessary specific classes and methods in appropriate places

## Open Closed Principle (OCP)

> Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.

- modifying the existing code affects the dependent parts
- better approach is to extend the class which does not affect

### Benefits

- new feature can be added with minimal bugs
- minimizes the risk of regression bugs
- enforces decoupling by isolating changes

### Refactoring

- we can use inheritance to apply this principle but introduces tight coupling (Parent child relation)
- better approach would be to use Strategy Pattern, where we create an interface and implement in both classes (same methods are present but less coupling), the use a Factory to create the instances for different types  

### Public APIs

- do not change existing public contracts: data classes and signature
- expose abstraction and let them add new features on top of your framework
- if beaking change is inevitable, give enough time to adapt (use `@Depricate`)

## Liskov Substitution Principle (LSP)

> If S is a subtype of T, then Objects of type T in a program may be replaced with Objects of type S without modifying the functionality of the program. 

- defines that objects of a superclass shall be replaceable with objects of its subclasses without breaking the application.
- Relationship `is-a` may have incorrect hierarchies, so ask the question "Is substitutable by?" 

### Symptoms / Violations during inheritance

- Empty Methods (obsolete methods)
- Hardening Preconditions (child class setting up more preconditions)
- Partial implemented methods (throwing Runtime exceptions)
- Type Checking (doing something for only a subtype)

### Refactoring 

- Eliminate incorrect relationship
- `Tell, don't ask principle` to eliminate type checking and casting 

```java
// Issue
for (Task t : tasks){
    if(t instanceof BugFix){
        BugFix b = (BugFix) t;
        b.init()
    }
    t.setInProgress()
}

// Fix 
class BugFix{
    void setInProgress(){
        this.init();
        super.setInProgress();
    }
}

for (Task t : tasks){
    t.setInProgress();
}
```

### Apply LSP proactively

- make sure the derived type can substitute the base type completely
- keep base class small and focused
- keep interfaces lean

## Interface Segragation Principle (ISP)

> Clients should not be forced to depend upon methods/ interfaces that they do not use.

- applies to abstract methods, public methods too
- goal is to reduce the side effects and frequency of required changes by splitting the software into multiple, independent parts.

### Benefits

- lean interfaces minimize dependencies on unused members and reduce code coupling
- code becomes more cohesive
- it reinforces the LSP and SRP

### Symptoms

- interfaces with lots of methods 
- interfaces with low cohesion (methods that are not related to the interface)
- client throws exception instead of implementing
- client provides empty method
- client forces implementation and becomes highly coupled (more dependencies to services)

### Refactor

- own code can be broken into multiple interfaces
- legacy code can use Adapter pattern 

## Dependency Inversion Principle (DIP)

> High-level modules should not depend on low-level modules. Both should depend on abstractions. Abstractions should not depend on details. Details should depend on abstractions.

![Dependency Inversion Principle](/img/java/dependency-inversion-principle.png)

- high level modules (e.g Payment, User Management etc.,)
    - modules that solve the real problems and use cases - more abstract and mapped to business domain
    - what software should do
- low level modules (e.g. Logging, Data access, IO, network communication etc)
    - implementation details that are required to execute the business policies
    - they are considered the plumbing or internals of the system
    - how the software should do various tasks
- high level and low level are relative to one another (Payment > Notification System > Email Services)
- Abstraction 
    - something that is not concrete
    - cannot create using `new`
    - done using interfaces and abstract classes

### Dependency Injection 

- used in conjuction to Dependency inversion principle
- creation of dependent objects outside of the class and provides those objects to class
- handling dependecies becomes harder doing it manually

### Inversion of Control

- it is a design principle in which the control of the object creation, configuration, and lifecycle is passed to a container / framework
- IOC container creates and controls the object (programmer does not control)
- Benefits
    - makes it easy to switch between different implementation
    - increase the program modularity
    - manages the lifecycle of objects and their configuration


