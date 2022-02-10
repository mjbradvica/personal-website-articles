# Dependency Inversion vs Dependency Injection vs Inversion of Control

## Dependency Inversion

Dependency Inversion is a *principle* that comes from the set of SOLID principles of software engineering. It can be summarized by the following:

* Use abstractions such as an interface or base class
* These abstractions should not depend on details

This can be realized in a number of ways:

* Layering your application to separate presentation, data, and business logic
* Having layers interact with each other via interfaces
* Returning abstractions from use cases instead of raw domain objects

Dependency Inversion is concerned with how your application as a whole communicates among boundaries.

## Inversion of Control

Inversion of Control is a *principle* concerned with separating abstractions from implementations. Inversion of Control is analogous, yet distinct, from Dependency Inversion. It is implemented at the module level of your application through a number of commonly known patterns and techniques.

* Event-Driven Design
* Service Locator
* Factory Method and Factory Pattern
* Strategy Pattern
* Dependency Injection

Each of these patterns is built around separating what a module wants to do and how it is performed.

**Events** - Something happened, but I don't care what happens next.
**Service Locator** - I need this service, but I don't care about the implementation.
**Factories** - I need an object, but I don't care how it is created.
**Strategy** - I have an object for a business process, but I don't care about details.
**Dependency Injection** - I need these interfaces, but I don't care about their implementations.

Most of these techniques are used at the module level such as inside of an application service or an individual object.

## Dependency Injection

A type of *technique*, Dependency Injection is an implementation of Inversion of Control. It is concerned with how objects should define relationships with other objects. Dependency Injection states there are three ways to give an object a dependency:

* Constructor
* Method
* Property

Dependency Injection is one example of aggregation. Given an object *n*, all dependencies of *n* should have independent life cycles.

## Summary

Dependency Inversion - a principle realized at the application level; use interfaces, abstractions, and layers to separate major aspects of your application

Inversion of Control - a principle realized at the module level; separate the what and the how via abstractions

Dependency Injection - a technique implemented at the object level; implement relationships between objects via aggregation
