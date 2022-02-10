# Dependency Inversion vs Dependency Injection vs Inversion of Control

## Dependency Inversion

Dependency Inversion is a *principle* that comes from the set of SOLID principles of software engineering. It can be summarized by the following:

1) Use abstractions such an interface or base class.
2) Abstractions should not depend on details.

This can be realized in a number of ways.

* Layering in your application to separate presentation, data, and business logic.
* Have layers interact with each other via interfaces.
* Return abstractions from uses cases instead of raw domain objects.

Dependency Inversion is concerned with how your application as a whole communicates between boundaries.

## Inversion of Control

Inversion of Control is a *principle* concerned with separating abstractions from implementations. Inversion of Control is analogous yet distinct from Dependency Inversion. It is implemented at the module level of your application via a number of commonly known patterns and techniques.

* Event Driven Design
* Service Locator
* Factory Method and Factory Pattern
* Strategy Pattern
* Dependency Injection

Each of these patterns is built around the separating what a module wants to do from how it is performed.

Events - Something happened, but I don't care what happens next.
Service Locator - I need this service, but I don't care about the implementation.
Factories - I need an object, but I don't care how it is created.
Strategy - I have an object for a business process, but I don't care what happens.
Dependency Injection - I need these interfaces, but I don't care about their implementations.

Most of these techniques are used at the module level such as inside of an application service or internally inside an individual object.

## Dependency Injection

Dependency Injection, a *technique*, is an implementation of Inversion of Control. It is concerned with how objects should define relations with other objects. Dependency Injection states that there are three main ways to give an object a dependency:

* Constructor
* Method
* Property

Dependency Injection is an example of objects implementing an aggregate relationship. Given an object *n*, all dependencies of *n* should have independent life cycles.

## Summary

Dependency Inversion - Principle realized at the application level. Use interfaces, abstractions, and layers to separate major aspects of your application.

Inversion of Control - Principle realized at the module level. Separate what a module does from how it does it via abstractions.

Dependency Injection - Technique implemented at the object level. Implement relationships between objects via aggregation.
