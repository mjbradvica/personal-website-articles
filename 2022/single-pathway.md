# Single Pathway

## The Problem with Options

Giving customers or developers too many options will cost you money. The phenomenon [Analysis Paralysis](https://en.wikipedia.org/wiki/Analysis_paralysis) states that people think themselves out of decisions.

Costco has figured out how to defeat this conundrum--they only give you one option.

Compared to a grocery store with dozens of options, Costco may only present one. Often, there is only one size to choose. Giving people fewer options means that people are in and out of the warehouses faster, which in turn means that Costco can keep costs down by serving more people with less space. Similarly, well-designed software gives minimal pathways. You need to develop your own yellow brick road that is easy for anyone to follow.

## The Yellow Brick Road for Customers

In the same way Costco reduces transactional friction by removing options, so should you. Two additional examples are Delta Air Lines and American Express. As a customer of both, they have my information required to make a transaction. I can purchase a flight on Delta.com in three clicks, select the outbound and return flights, then confirm my credit card code. AmericanExpress allows me to apply for a new credit card in two clicks and confirms my income. I am not required to enter any redundant information regarding my name, address, or zip code. Both processes are well designed-because they make the process of completing a transaction as easy as possible. Both companies get my business, and I save time.

## Engineering Roads for Developers

The idea that we should provide a simple pathway for developers is just as important. This translates to limiting their options in the development space. If you give ten different developers ten options each, then you will end up with one-hundred solutions to the problem. The issue is not unique to software development but is certainly very prevalent. You, as a lead developer or architect, need to ensure that your developers are choosing the pathway you have defined. The goal is to spend less time arguing and more time developing.

You can enforce a single pathway by attacking the problem at multiple angles:

1. Force Test Coverage - Mandatory test coverage gives you a minimum bar for certainty. You will be assured that code meets a minimum threshold for quality. Forcing developers to write tests is the best way to have them write better code.

2. Forced Coding Standards via the Compiler - ESLint plus Prettier on the front-end and either .NET Analyzers or Checkstyle on the back-end do the dirty work to enforce team standards. Static analysis tools keep developers on the pathway you have defined, leading to shorter feedback loops, fewer bugs, and faster-to-market features.

3. Choosing Simplicity - As the architect of an application, choosing to delay decisions until the last possible moment can pay dividends. You probably do not require Docker or that fancy ORM on day one of the project. Piling on layers of libraries and frameworks early only serves to create multiple paths that slow down development. There will be some decisions you must make early, such as your front-end framework. I must stress the importance of choosing boring and simple solutions and not being swept away by the latest carnival lights and sounds that a new framework may promise. Customers don't care about the internal guts of your application. They just want it to work.

4. Reducing Options - Never let 

## Conclusion

A dirty secret not only in software engineering is that many times you need to make decision