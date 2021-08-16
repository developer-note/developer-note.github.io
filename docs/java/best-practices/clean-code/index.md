---
layout: default
title: Clean Coding Principles
parent: Best Practices
grand_parent: Java
nav_order: 10
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

# Clean Coding Prinicples

- General
    - Do not Repeat Yourself (DRY)
    - Keep It Simple, Stupid (KISS)
    - You Aren't Gonna Need It (YAGNI)
    - The Boy Scouts rule: Leave the campground cleaner than you found it.
    - Be consistent. 
- Names
    - Names of variables, methods and Classes should be descriptive, intention-revealing and meaningful. 
    - Names should be domain and context related.
    - Replace magic numbers with named constants.
- Function
    - Methods should be small and do one thing. (SRP)
    - Avoid long list of parameters
    - It should either change the state or have side effect, not both. (CQP)
    - Should not return Null.
    - Should not throw Generic exceptions like Exception, Error, RuntimeException. It should be Specific. 
    - Avoid duplicating. (DRY)
    - Use Guard clauses to reduce nested conditions.
    - Encapsulate boundary conditions to a variable/function for easy access and understanding.
    - Prefer polymorphism to if/else or switch/case.
    - Don't use flag arguments, instead split into independent methods that can be called directly.
- Comments
    - Code should be self explanatory.
    - Use comments as an explanation of intent, clarification of code, warn of consequence.
    - Public methods should be well documented.
    - Avoid Bad comments like commented code, redundant comments.
- Format
    - Maintaing consistent project structure
    - Follow conventional order in package declaration, import statements, fields, Constructors and methods inside class
    - Related code should appear close. (Vertically dense)
    - Group of functions that perfrom similar operations need to be close. (Conceptual Affinity)
    - Function that is called should be below the caller function (Vertical Ordering)
    - Dependant variables or Methods should be close to their usage. (Vertical Distance)
    - Make required whitespaces for less related (Horizontal Density and Openess)
    - Don't do Horizontal Alignment.
    - Use indentation for scoping heirarchy.
- Objects and Data Structures
    - Don't Expose a Class more than it should. Use private, protected, public modifiers to control the abstraction.
    - Composition is favored over inheritance
- Class
    - Class should be small and have one responsibility. (SRP)
    - Replace hard coded values with constants, enums or from Configurations
    - Provide enough logging with log levels and provide context data for logs
    - Fields and methods should have high Cohesion.
    - Use Dependency Injection.
    - Prefer dedicated value objects to primitive type.
    - Avoid Method chaining (Law of Demeter)
    - Avoid negative conditional
- Tests
    - Write one assert per unit tests
- Concurrency
    - Fields and Methods accessed should be thread-safe.