# 2022 ideas

* Two NuGet packages that should be in every .NET project

* Two npm packages that should be in every Javascript project

* Four things all well engineered software contains

    * Some kind of DevOps pipeline
    * Strongly enforced coding standards
    * A clearly defined architecture, whatever it is
    * Easily understandable onboarding documentation

* Choosing between EF, Dapper, Nhibernate, ADO.NET
    * Advantages-disadvantages of all four
    * Adv/Dis between EF code first, db first, db from model

* The beauty that are common interfaces (make it abstract)

* If you are keep repeating yourself, something needs to change

* NBaseRepository repo and its future

* Refactoring your Data Access layer (series)
    * The purpose of the Repository
    * Extracting predicates from queries
    * Using query objects
    * Moving to a common base repository
    * Using NPredicateBuilder for queries

* Understanding conditional AND/OR in LINQ statements

* AutoMapper does not belong in any back-end code

* Remember that MediatR is a service locator, use it appropriately

* Ignoring technical debt will cost you dearly later (make investing costs comparison, compounding fees)

* Software is your business. Not the other way around.

* Consistently average software is better than occasionally good software

* C# equality cheat sheet

* When to use an interface (mock or different implementations)

* An interface doesn't mean just an interface

* Finding a common interface is best thing you can do

* From delegates to LINQ, everything is a function

* Common configurations for js frameworks in VS Code

* Applying prettier with the new husky

* Where should you validate objects

## Q4 Articles

* Alternatives to Blazor
    * ASP.NET Core Razor Pages
    * ASP.NET Core MVC
    * Angular
    * React

* Reasons to go Blazor Adjacent
    * You don't need a fully interface UI
    * Talent doesn't have the right skills
    * Need more framework maturity
    * Technical stack is already heave Javascript

* Adding Typescript to an ASP.NET Core Projects
    * Typescript is a more type-safe and safer coding method
    * Expand in ESLint, Prettier, ect

* Using ASP.NET Core Tag Helpers to abstract the UI
    * Easier code base for developers to work in

* Task-Based UI's as Business value
    * Task-based UI provides details for changes
    * person changes address/card means changes in behavior
    * Three part of refactoring to task-based UI

* Custom colors in Bulma
    * adding secondary and tertiary colors in bulma

* Leverage front-end code generation from Swagger
    * Less time spent creating boilerplate front ends

* A base page pattern for ASP.NET Core Razor Pages
    * Reduce your code base

1) Blazor Prop value
2) Alternative to Blazor
3) Task based UI
4) Reason to go Blazor adjacent
5) Reasons to make the investment in Web Assembly

## Ryan Azure Topics (distributed systems)

Business possibilities with DDD -> moving from solo solution to DDD

Aligning your business domain in code -> modeling in DDD

heavily favored business transactions -> implementing cqrs in applications

rarely do you "know" you need microservices -> your architecture is learned over time

architecture for distributed systems -> azure services for microservices

utilization with distributed systems -> containers, container orchestration

dealing with distributed communication -> nservicebus with azure

storage in azure for distributed systems -> SQL server, redis cache, cosmos db

business possibilities of event sourcing -> using eventstore in azure
