# Dependency Injection Do's and Don'ts

Thanks for participating!

![thanks](https://media.giphy.com/media/RuP38JSfj14je/giphy.gif)

Can anyone here tell me the difference between Dependency Inversion, Inversion of Control, and Dependency Injection?

Or are they the same thing?

![think](https://media.giphy.com/media/2H67VmB5UEBmU/giphy.gif)

Dependency Inversion - a principle realized at the application level; use interfaces, abstractions, and layers to separate major aspects of your application

Inversion of Control - a principle realized at the module level; separate the what and the how via abstractions

Dependency Injection - a technique implemented at the object level; implement relationships between objects via aggregation

## Overview

- Most of this talk is what **NOT** to do
- Going to talk about some other topics by proxy, but they're still related
- I am assuming that most you have some basic knowledge about DI

## TL:DR (If you have to leave early)

- Please use dependency injection
- Because testing
- Use constructor injection 99.99999% of the time

## 