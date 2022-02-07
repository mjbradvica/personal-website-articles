# Dependency Inversion vs Dependency Injection vs Inversion of Control



## Dependency Inversion

Dependency Inversion is a *principle* that comes from the set of SOLID principles of software engineering. It can be summarized by the following:

1) Use abstractions such an interface or base class.
2) Abstractions should not depend on details.

This can be realized in a number of ways.

* Using layering in your application to separate presentation, data, and business logic.
* Classes that inherit only from abstract classes or interfaces.
* Returning abstractions from uses cases instead of raw domain objects.

Dependency Inversion is concerned with how your application as a whole communicates between boundaries.

## Inversion of Control

Inversion of Control is another principle is concerned with separating abstractions from implementations. 

## Dependency Injection

Dependency Injection is what most people think of when you talk about inversion of dependencies. Dependency Injection states that there are three main ways to give an object a dependency:

* Constructor
* Method
* Property

A vast majority of dependencies are and should be initialized via the constructor. This ensures that the object has everything it may required upon initialization.

## Summary

Dependency Inversion - Principle concerned with architecture. Communication between layers should use abstractions.

Inversion of Control - Principle concerned with separating the what from the how in modules.

Dependency Injection - Technique that states dependencies for objects should use aggregation.
