---
title: Design Pattern
draft: false
tags:
  - se
---

![banner](https://images.unsplash.com/photo-1559644705-15d30e582900?w=500&auto=format&fit=crop&q=60&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxzZWFyY2h8MTF8fHBhdHRlcm58ZW58MHx8MHx8fDA%3D)

**Design patterns** [^1] are **proven or reusable solutions** to common software design problems. They provide **structured approaches** to organizing code, improving **reusability, flexibility, and maintainability**. Think of them as a **blueprint or template** that guides developers in solving recurring issues in software development.

> [!hint] Three main types of design pattern
>
> - **Creational Patterns** – Focus on how objects are created, making it easier to manage object creation and avoid unnecessary complexity.
> - **Structural Patterns** – Help organize and connect different parts of a system, ensuring they work together efficiently.
> - **Behavioral Patterns** – Define how objects interact and communicate, making the system more flexible and easier to extend.

## In Depth

### **Creational Patterns**

> *"How objects are created"*

1. **[[Singleton]]**
    - Ensures only one instance of a class exists and provides a way to access it globally.
    - Useful for managing configurations or logging.

2. **[[Factory Method]]**
    - Provides a flexible way to create objects without specifying their exact class.
    - Useful when dealing with multiple related object types.

3. **[[Builder]]**
    - Helps create complex objects step by step.
    - Useful when an object has many optional parts.

### **Structural Patterns**

> *"How objects are organized"*

1. **[[Adapter]]**
    - Allows two incompatible interfaces to work together.
    - e.g., *Like a power plug adapter that makes a foreign device work in a local socket.*
2. **[[Composite]]**
    - Lets you treat a group of objects the same way as a single object.
    - e.g., *Useful for representing tree structures like UI components.*
3. **[[Decorator]]**
    - Adds new functionality to an object without modifying its original structure.
    - e.g., *Like adding extra features to a car without redesigning it.*

### **Behavioral Patterns** 

> *How objects communicate*

1. **[[Observer]]**
    - Allows multiple objects to react when one object changes.
    - e.g., *Like a notification system where many users receive updates.*
2. **[[Strategy]]**
    - Lets you switch between different algorithms without changing the main code.
    - e.g., *Like choosing different payment methods in an online store.*
3. **[[Command]]**
    - Encapsulates a request as an object, making it easy to queue, log, or undo actions.
    - e.g., *Like storing a list of actions in a video game to allow undo/redo.*

---

## 🔗 Sources

[^1]: [Difference Between Design Patterns and Design Principles](https://www.codepattern.net/Blog/post/difference-between-design-patterns-and-design-principles)