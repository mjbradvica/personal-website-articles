# Single Pathway

## The Issue with Giving People Options

Giving customers or developers too many options will lose you money. It comes down to [Analysis Paralysis](https://en.wikipedia.org/wiki/Analysis_paralysis) where people tend to think themselves out of a decision.

Costco has figured out how to defeat this conundrum-they only give you one option. If you need to buy Ketchup they won't present you with a dozen options that would be available in a grocery store. There is one brand only, and it probably comes in a very large two-pack. Giving people only one options means that people are in and out of the warehouses faster which in-turn means that Costco can keep costs down by serving more people with less space needed. Well designed software, like Costco, only giving a few if not only one pathway for people to walk down. You need to develop your own yellow brick road for Dorthy's to happily skip down.

## Single Pathway for Customers



## Single Pathway for Developers

The idea that we should provide a pathway for developers is just as important as providing one for customers. This means we need to severely limit their options in the development space. Give ten different developer ten options each, and you will end up with one-hundred solutions to the problem. The issue is not unique to software development but is certainly very prevalent.

You can enforce a single pathway by attacking the problem at multiple angles:

1. Force Test Coverage - Mandatory test coverage gives you a minimum bar for certainty. You will be assured that code meets a minimum threshold for quality-and forcing developers to write tests is the best way to have them write better code overall.

2. Force Coding Standards via the compiler - Tools such as ESLint/Prettier for the front-end and either the .NET Analyzers or Checkstyle for Java will do the dirty work for you to enforce team standards. Static analysis tools keep developers on the pathway you have defined which leads to a shorter feedback loops, fewer bugs, and faster to market features.

3. Choosing Simplicity - One of the reasons (as of 2022) that I recommend React over Vue or Angular is that React is just concerned with the view and does not try to give the developer dozens of features they may or may not use correctly. For instance Vue gives you multiple way to propagate events between components where React just provides callbacks. Choosing Vue means you now have to enforce the correct way of implementing features where React provides a single pathway.

4. Reducing Options - Never let 