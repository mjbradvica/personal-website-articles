# Introducing ChainStrategy

![logo](https://i.imgur.com/LMX4jJf.png)

## Overview

[ChainStrategy](https://github.com/mjbradvica/ChainStrategy) is a fully-featured implementation of the [Chain of Responsibility](https://en.wikipedia.org/wiki/Chain-of-responsibility_pattern) and [Strategy](https://en.wikipedia.org/wiki/Strategy_pattern) patterns for dotnet. ChainStrategy allows you to focus on solving problems instead of fussing over boilerplate.

Also included is:

- Dependency Injection built-in
- Cancellation Token support
- Easily testable handlers

## Installation

Install where you need with:

```bash
Install-Package ChainStrategy
```

Integration with the Microsoft DI container is done in one line.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var builder = WebApplication.CreateBuilder(args);

        builder.Services.AddChainStrategy(Assembly.GetExecutingAssembly());

        // Continue setup below
    }
}
```

You may also pass params of Assemblies if you have multiple projects to register.

## Chain of Responsibility

A Chain of Responsibility is best used when you have a complex process that can be broken up into smaller chunks. The biggest advantage of doing so is greater ease of testing.

Create a payload object:

```csharp
public class MyPayload : ChainPayload
{
    // all properties and data
}
```

Create any number of handlers required to complete the operation:

Implement the "DoWork" method for each handler and return the payload when finished.

```csharp
public class MyChainHandler : ChainHandler<MyPayload>
{
    public MyChainHandler(IChainHandler<MyChainPayload>? handler)
        : base(handler)
    {
    }

    public override Task<MyChainPayload> DoWork(MyChainPayload payload, CancellationToken cancellationToken)
    {
        // implement steps and update payload
        
        return payload;
    }
}
```

Create a profile to define what steps each chain should use.

```csharp
public class MyProfile : ChainProfile<MyChainPayload>
{
    public MyProfile()
    {
        AddStep<MyFirstHandler>()
        .AddStep<MySecondHandler>()
        .AddStep<MyThirdHandler>();
    }
}
```

Inject the factory interface where you need it and execute.

```csharp
public class MyService
{
    private readonly IChainFactory _chainFactory;

    public IMyService(IChainFactory chainFactory)
    {
        _chainFactory = chainFactory;
    }

    public async Task Handle()
    {
        var result = await _chainFactory.Execute(new MyPayload());
    }
}
```

This is only a very small example of what chains can accomplish. The official documentation will show you the full power they offer. Samples for chains are available [here](https://github.com/mjbradvica/ChainStrategy/tree/master/samples/ChainStrategy.Samples).

## Strategy

Strategies are best utilized when you have multiple implementations of the same algorithm or process. You can think of processing credit cards as one such example.

Strategies use a request and response to push data up and down.

```csharp
public class MyResponse
{
    // response properties
}
```

```csharp
public class MyRequest : IStrategyRequest<MyResponse>
{
    // request properties
}
```

Similar to chain handlers, implement the appropriate interface and required method.

```csharp
public class MyStrategyHandler : IStrategyHandler<MyRequest, MyResponse>
{
    public async Task<MyResponse> Handle(MyRequest request, CancellationToken cancellationToken)
    {
        // implement and return response
    }
}
```

Creating a profile for a strategy is done in the same fashion with one caveat; adding a strategy handler requires a predicate to decide which handler will be chosen.

```csharp
public class MyStrategyProfile : StrategyProfile<MyRequest, MyResponse>
{
    public MyStrategyProfile()
    {
        AddStrategy<MySecondHandler>(request => request.Value == 0);
        AddStrategy<MyFirstHandler>(request => request.Value > 10);
    }
}
```

Starting a strategy follows a similar process as well.

```csharp
public class MyService
{
    private readonly IStrategyFactory _strategyFactory;

    public MyService(IStrategyFactory strategyFactory)
    {
        _strategyFactory = strategyFactory;
    }

    public async Task Handle()
    {
        var result = await _strategyFactory.Execute(new MyRequest());
    }
}
```

## Conclusion

This article has only briefly covered what ChainStrategy is capable of. A glance at the documentation will show you how you may also implement testing, base handlers, handler reuse, and more.

Proper use of both patterns can drastically improve a codebase by allowing you to remove and replace hard-to-test code while improving code quality and test coverage.
