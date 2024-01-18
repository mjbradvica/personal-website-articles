# Dependency Injection Magic Number

Dependency Injection is a lifesaving feature of modern software development. In this process, you define a classes' dependencies, wire up an IOC container, and the hard work is done for you.

However, issues arise when we as developers rely on dependency injection too much. Since the process of adding a new dependency is easy, sometimes our classes become bloated. Use the following points as a guideline for dependencies:

> Stay around four injected dependencies for each class.

A more nuanced version would be:

1. Keep dependencies to four or less
2. At five, look to refactor and reduce
3. Six dependencies is too many

The reason for this guideline comes from testing. If you have a service that has more than four dependencies, testing methods will be laborious.

Let's take a generic service for example:

```csharp
public class Service : IService
{
    private readonly IFirst _first;
    private readonly ISecond _second;
    private readonly IThird _third;
    private readonly IThird _fourth;
    private readonly IThird _fifth;
    private readonly IThird _sixth;

    public Service(IFirst first, ISecond second, IThird third, IThird fourth, IThird fifth, IThird sixth)
    {
        _first = first;
        _second = second;
        _third = third;
        _fourth = fourth;
        _fifth = fifth;
        _sixth = sixth;
    }

    public void HandleRequest()
    {
        // Handle
    }
}
```

Testing this service will require you to declare six mocks:

```csharp
public class ServiceTests
{
    public void HandleRequest_DoesTheCorrectThing()
    {
        var firstMock = new Mock<IFirst>();
        var secondMock = new Mock<ISecond>();
        var thirdMock = new Mock<IThird>();
        var fourthMock = new Mock<IFourth>();
        var fifthMock = new Mock<IFifth>();
        var sixthMock = new Mock<ISixth>();

        // Setup FirstMock
        // Setup SecondMock
        // Setup ThirdMock
        // Setup FourthMock
        // Setup FifthMock
        // Setup SixthMock

        var service = new Service(firstMock.object, secondMock.object, thirdMock.object, fourthMock.object, fifthMock.object, sixthMock.object);

        // More test code
    }
}
```

Just formulating this test is already tedious. Due to the requirement of the services' constructor, you must provide six different mocks. When you are writing production code, the container does the work for you. The annoyance of this is not apparent until you have to write the test code.

The four dependency limit will save you time. It places a hard cap that keeps your codebase simple. Less code in your classes means less code you have to test.

Even if a class _requires_ a certain level of complexity, you have plenty of options to choose from:

- Chain of Responsibility
- Events
- Strategy Pattern
- Rethinking a classes' responsibilities and boundaries

Everything in the list above helps to delegate responsibility of a class, service, or handler to a more specifically designed, easier to test component.

After all, easy-to-test components are the root of great software engineering!

By limiting the number of dependencies in each class, you are forcing yourself to break from the traditional mental model of simply injecting a new dependency. Furthermore, by breaking apart your code then applying a little object-oriented magic, you are investing in a better codebase.
