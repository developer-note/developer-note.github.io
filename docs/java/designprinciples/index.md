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

- a set of guidelines that helps us to avoid having a bad design.
-  3 important characteristics of a bad design that should be avoided
    - Rigidity (one change affects too many parts of the system)
    - Fragility (one change unexpected parts of the system break)
    - Immobility (hard to use in another application)
- first five object-oriented design (OOD) principles
    - S - Single-responsiblity Principle
    - O - Open-closed Principle
    - L - Liskov Substitution Principle
    - I - Interface Segregation Principle
    - D - Dependency Inversion Principle


# Single Responsibility Principle (SRP)

> A class should have one, and only one, reason to change.

- What is the responsibility of your class/component/microservice?

# Open Closed Principle (OCP)

> Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.

- It uses interfaces instead of superclasses to allow different implementations which you can easily substitute without changing the code that uses them. 
- The interfaces are closed for modifications, and you can provide new implementations to extend the functionality of your software.

# Liskov Substitution Principle (LSP)

> Let Φ(x) be a property provable about objects x of type T. Then Φ(y) should be true for objects y of type S where S is a subtype of T.

- defines that objects of a superclass shall be replaceable with objects of its subclasses without breaking the application.
- That requires the objects of your subclasses to behave in the same way as the objects of your superclass.
- Don’t implement any stricter validation rules on input parameters than implemented by the parent class.
- Apply at the least the same rules to all output parameters as applied by the parent class.

# Interface Segragation Principle (ISP)

> Clients should not be forced to depend upon interfaces that they do not use.

- goal is to reduce the side effects and frequency of required changes by splitting the software into multiple, independent parts.

# Dependency Inversion Principle (DIP)

> High-level modules should not depend on low-level modules. Both should depend on abstractions. Abstractions should not depend on details. Details should depend on abstractions.

- an interface abstraction between higher-level and lower-level software components to remove the dependencies between them.