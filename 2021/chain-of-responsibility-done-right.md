# Chain of Responsibility. Done Right.

## Pattern Overview

The Chain of Responsibility is an original "Gang of Four" behavior pattern. It's main purpose is to allow for an operation to occur with out the individual steps having any knowledge of any other step involved.

## Problem Statement

The chain of responsibility solves the following issue. You may have an application that has a series of application or domain service calls chained one after another.

```csharp
public class FirstService : IFirstService
{
    private readonly ISecondService _secondService;

    public FirstService(ISecondService secondService)
    {
        _secondService = secondService;
    }

    public Widget DoSomething(Widget widget)
    {
        // Do something with the widget and pass to second service.
        return _secondService.DoSomething(widget);
    }
}
```

```csharp
public class SecondService : ISecondService
{
    private readonly IThirdService _thirdService;

    public SecondService(IThirdService thirdService)
    {
        _thirdService = thirdService;
    }

    public Widget DoSomething(Widget widget)
    {
        // Do something and pass to third service.
        return _thirdService.DoSomething(widget);
    }
}
```

```csharp
public class ThirdService : IThirdService
{
    public Widget DoSomething(Widget widget)
    {
        // Complete operation and return final widget.
        return widget;
    }
}
```

The interface definitions for these classes are inconsequential.

The problem with chaining these operations becomes apparent when with unit and integration testing. You will see that in order to unit test the first service, we end up having to inject mocks.

> **Note**: I am using the Moq testing library in these code examples.

```csharp
[TestClass]
public class FirstServiceUnitTests
{
    private readonly FirstService _firstService;

    public FirstServiceUnitTests()
    {
        var secondService = new Mock<ISecondService>();

        // Mock second service returns original widget passed to verify state
        secondService.Setup(service => service.DoSomething(It.IsAny<Widget>()))
            .Returns((Widget widget) => widget);

        _firstService = new FirstService(secondService.Object);
    }

    [TestMethod]
    public void MyMethod_Does_Something_Correctly()
    {
        var result = _firstService.DoSomething(new Widget());

        // Assert that my function did something correctly.
    }
}
```

In our unit test, we mocked the second service and returned the widget passed to it to verify that state was correct. The first issue the Chain of Responsibility would solve is the need for the mock in the first place.

> **Note**: This is without any other dependencies like factories or repositories you may need to inject. The amount of mocking with code like this can be very tedious.

```csharp
[TestClass]
public class FirstServiceIntegrationTests
{
    private readonly FirstService _firstService;

    public FirstServiceIntegrationTests()
    {
        _firstService = new FirstService(new SecondService(new ThirdService()));
    }

    [TestMethod]
    public void MyMethod_Does_Something_Correctly()
    {
        var result = _firstService.DoSomething(new Widget());

        // Assert our final result is correct
    }
}
```

In our integration test, you can see we need to manually create every dependency and inject them. There are ways you can remedy this. But the problem is clear that in order to do integration testing for the first service, we need to deal with the hassle of all its dependencies. Chain of Responsibility removes the need to bring along unnecessary dependencies when we are testing an individual service.

## My Issue With Current Examples

My issue with many examples for this pattern is that the creation of the chain is not encapsulated and the example are not shown with dependency injection in mind. You will see in the following examples how this plays out.

### Base Code

The following interface and base class should look familiar.

```csharp
public interface IChainHandler<T>
{
    Task<T> Handle(T obj);
}
```

```csharp
public abstract class ChainHandler<T> : IChainHandler<T>
{
    private readonly IChainHandler<T> _successor;

    protected ChainHandler(IChainHandler<T> successor)
    {
        _successor = successor;
    }

    public async Task<T> Handle(T request)
    {
        var result = await DoWork(request);

        return _successor == null ? result : await _successor.Handle(result);
    }

    protected abstract Task<T> DoWork(T request);
}
```

The base class and interface should be 99% the same from any other example. And we can now update our three services into chain handlers.

```csharp
public class FirstChainHandler : ChainHandler<Widget>
{
    public FirstChainHandler(IChainHandler<Widget> successor)
        : base(successor)
    {
    }

    protected override Task<Widget> DoWork(Widget request)
    {
        // Do something with the widget and pass.
        return Task.FromResult(request);
    }
}
```

```csharp
public class SecondChainHandler : ChainHandler<Widget>
{
    public SecondChainHandler(IChainHandler<Widget> successor)
        : base(successor)
    {
    }

    protected override Task<Widget> DoWork(Widget request)
    {
        // Do something with the widget and return.
        return Task.FromResult(request);
    }
}
```

```csharp
public class ThirdChainHandler : ChainHandler<Widget>
{
    public ThirdChainHandler(IChainHandler<Widget> successor)
        : base(successor)
    {
    }

    protected override Task<Widget> DoWork(Widget request)
    {
        // Do something with the widget and return.
        return Task.FromResult(request);
    }
}
```

With our new updated handlers, our testing becomes much easier since we no longer need mocks or other dependency chains.

```csharp
public class FirstHandlerUnitTests
{
    private readonly IChainHandler<Widget> _handler;

    public FirstHandlerUnitTests()
    {
        // Mock any dependencies needed by handler.
        _handler = new FirstChainHandler(null);
    }

    [TestMethod]
    public async Task Handle_Does_Something_Correctly()
    {
        var result = await _handler.Handle(new Widget());

        // Assert the result is correct.
    }
}
```

```csharp
[TestClass]
public class FirstHandlerIntegrationTests
{
    private readonly IChainHandler<Widget> _handler;

    public FirstHandlerIntegrationTests()
    {
        // Inject real dependencies for integration test.
        _handler = new FirstChainHandler(null);
    }

    [TestMethod]
    public async Task Handle_Does_Something_Correctly()
    {
        var result = await _handler.Handle(new Widget());

        // Assert the result is correct.
    }
}
```

We can see that the two examples above have made our lives considerably easier. We no longer need to worry about other dependencies that have no bearing on what our test code is trying to accomplish.

### Current Examples

The main issue occurs of when we try to create and initiate our chain.

```csharp
public class BadChainCreationHandler
{
    // IFirstChainDependency;
    // ISecondChainDependency;
    // IThirdChainDependency;
    public async Task<Widget> Handle(Widget widget)
    {
        var handlerChain = new FirstChainHandler(new SecondChainHandler(new ThirdChainHandler(null)));

        return await handlerChain.Handle(widget);
    }
}
```

The root of my gripe with most examples becomes apparent. Our code breaks SRP by being concerned with how the chain is created. We don't want to know the details of our chain. We just want to call a method and be done with it.

What we need is a factory that is responsible for the sole creation of our chain.

## Better Suited

```csharp
public interface IChainFactory<T>
{
    IChainHandler<T> CreateChain();
}
```

```csharp
public class WidgetChainFactory : IChainFactory<Widget>
{
    // IFirstChainDependency;
    // ISecondChainDependency;
    // IThirdChainDependency;
    public IChainHandler<Widget> CreateChain()
    {
        return new FirstChainHandler(new SecondChainHandler(new ThirdChainHandler(null)));
    }
}
```

> **Note**: You can't use dependency injection for each handler because they all have the same signature of IChainHandler of type T. This is why they need to be instantiated manually.

With our new widget chain factory, we have shifted the responsibility from the handler to create the chain to a factory. Our new handler now looks like this.

```csharp
public class GoodChainCreationHandler
{
    private readonly IChainFactory<Widget> _chainFactory;

    public GoodChainCreationHandler(IChainFactory<Widget> chainFactory)
    {
        _chainFactory = chainFactory;
    }

    public async Task<Widget> Handle(Widget widget)
    {
        var chain = _chainFactory.CreateChain();

        return await chain.Handle(widget);
    }
}
```

Our new handler now has zero knowledge of how our chain is created or how long it is. It simply calls the CreateChain method from our factory and kicks off the process.

### Other Things To Remember

Considering using the command pattern as the type parameter for your chains. So instead of "Widget", it may be "CreateWidget" or "ShipWidget". You would then have an IChainFactory of type CreateWidget or IChainFactory of type ShipWidget. This will allow for you to create similar chains for the same object.

You can reuse the IChainHandler interface to create a base handler that has logging or exception handling built in. This would further reduce the amount of testing required.

The examples I showed today only scratch the surface of what is possible. Remember that patterns play nicely with each other. A chain handler can kick off another chain or strategy inside of its handle method.

## Conclusion

The Chain of Responsibility pattern is one of the more underutilized behavior patterns. However it can become a wonderful tool in your engineering arsenal once one is aware of its potential.

The patterns main use it to decouple individual steps of long operations so that each step is only concerned with its scope of work. This allows for easier unit and integration testings with each step; and reusability among similar operations.
