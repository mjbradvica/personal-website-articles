# NPredicateBuilder Updated to 5.3

## NPB Gets a Glow-Up

[NPredicateBuilder](https://github.com/mjbradvica/NPredicateBuilder) now has its own identity. The construction sign was chosen to highlight NPB's ability to "construct" queries on the fly out of smaller pieces. Much like a building is created out of many blocks, your queries and orders are created from multiple single statements.

![logo](https://i.imgur.com/plmmAcK.png)

## All Language Versions Supported

NPB now supports the following dotnet versions:

Base Library: Standard 2.0 & 2.1, NET 5-8
EF Library: NET 480, 3.1, 5, 6, 7, 8

## It's time to ReadMe

The documentation has moved from the wiki to the ReadMe page when you land on the [repository main page](https://github.com/mjbradvica/NPredicateBuilder). (The wiki still exists if you like.)

### Did you know?

> NPredicateBuilder EF queries work for navigation properties as well.

As long as your navigation joins return a boolean value, they will integrate with NPB seamlessly.

## New Samples

An updated set of samples is available [here.](https://github.com/mjbradvica/NPredicateBuilder/tree/master/samples/NPredicateBuilder.Samples)

## Continued Purpose

The purpose of NPB continues to be the easiest and simplest way to test, construct, and maintain LINQ queries. You can break down your Where and Order statements into singular entities-creating reusable nuggets that may be tested independently, but used together.
