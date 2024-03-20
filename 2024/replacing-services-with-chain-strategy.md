# Replacing Services with ChainStrategy

## The Problem Space

In many long-lived operations in software, a complex process needs to be broken up into multiple steps. The most common solution is to use a myriad of services to take care of each step individually. These services are then aggregated together at the root of a dependency.

A rudimentary example such as below may feel familiar:

```csharp
public class MyHandler : IHandle<MyRequest, MyResponse>
{
    private readonly IServiceA _a;
    private readonly IServiceB _b;
    private readonly IServiceC _c;

    public MyHandler(IService A, IService B, IService C)
    {
        _a = A;
        _b = B;
        _c = C;
    }

    public MyResponse Handle(MyRequest request)
    {
        // lots of code.
    }
}
```

Such a solution may suffice for certain operations, however there are better options available to us.

A few issues tend to arise from using a collection of services like the example above:

1) Code tends to leak into the Handler. The dependency root should act as an orchestrator, not a location for random logic.
2) Services are not declarative. The naming of services skews to generic and confusing titles. This is because random logic is placed into services, resulting in catch-all names.
3) Dependency roots such as the handler are a nightmare to test. The handler above requires at least three mocks, if not more.
4) Lengthy parameter lists. The more logic and responsibilities pushed into services, the more their parameter lists grow. Adding to the complexity of already difficult unit testing.

## Chains Designate Responsibility

Chains have a clear entry and exit point. Calling the "Execute" method is a hard boundary between where you want an operation or start and finish.

```csharp
public class MyHandler : IHandle<MyRequest, MyResponse>
{
    private readonly IChainFactory _factory;

    public MyHandler(IChainFactory factory)
    {
        _factory = factory;
    }

    public async Task<MyResponse> Handle(MyRequest request)
    {
        var result = await _factory.Execute(new MyPayload(request));
    }
}
```

Using a chain signals a single entry and egress for the operation. All logic for the operation is completed inside the "Execute" method.

## Chain Handlers Are Fully Declarative

With Services, logic finds its way to large methods that are unable to describe themselves fully. You would never name a method, "FindAllWidgetsCheckForValidOneFilterOutInvalidRunCalculationAndFinalizeOrder". But it is very easy to see a single service method trying to accomplish that entire process.

```csharp
public class CheckForInvalidWidgetsHandler : ChainHandler<MyPayload>
{
    public CheckForInvalidWidgetsHandler(IChainHandler? handler)
        : base(handler)
    {
    }

    public Task<MyChainRequest> DoWork(MyPayload payload)
    {
        foreach (var widget in payload.PotentialWidgets)
        {
            if (widget.IsValid)
            {
                payload.AddValidWidget(widget);
            }
        }

        return Task.FromResult(payload);
    }
}
```

 With chains, handlers can be as small as you want, and you can be as descriptive as you like with your naming.

## Chain Handlers Embrace Unit Testing

Testing services or service aggregates have difficulties due to the number of dependencies required to mock.

```csharp
[TestClass]
public class MyHandlerTests
{
    private readonly Mock<IServiceA> _a;
    private readonly Mock<IServiceB> _b;
    private readonly Mock<IServiceC> _c;
    private readonly MyHandler _handler;

    public MyHandlerTests()
    {
        _a = new Mock<IServiceA>();
        _b = new Mock<IServiceB>();
        _c = new Mock<IServiceC>();
        _handler = new MyHandler(_a.Object, _b.Object, _c.Object);
    }

    [TestMethod]
    public void MyHandler_IsCorrect()
    {
        // test code here
    }
}
```

The handler tests above require a minimum of three mocks which may or may not be used depending on the logical branch being tested at a time. Compare this to moving the handler to accept a chain instead, only one dependency is needed. Unit testing the handler may be forgone completely in the end if no branching logic occurs.

```csharp
[TestClass]
public class MyHandlerTests
{
    private readonly Mock<IChainFactory> _factory;
    private readonly MyHandler _handler;

    public MyHandlerTests()
    {
        _factory = new Mock<IChainFactory>();
        _handler = new MyHandler(_factory.Object);
    }

    [TestMethod]
    public void MyHandler_IsCorrect()
    {
        // test code here
    }
}
```

## Command as a Parameter

Services typically have parameter lists of N parameters. This parameter lists wanes and waxes when the service is updated or refactored. Inevitably leading to [shotgun surgery](https://en.wikipedia.org/wiki/Shotgun_surgery).

```csharp
public class MyPayload : ChainPayload
{
    // all properties for the chain
}
```

Chains require you to put all data-initial, final, and temporary into a single payload object-one example of a [command](https://en.wikipedia.org/wiki/Command_pattern). This ensures that all updates to the payload do not require downstream changes to any handler.

## A Clear Alternative

[ChainStrategy](https://github.com/mjbradvica/ChainStrategy) gives you a much more practical approach using the Chain of Responsibility pattern to solve the issue of breaking apart a large problem.

In addition to everything discussed in this article chain handlers give you the added advantage of:

- Autonomy, each handler does not know where it exists in the process.
- Reusability, via a [base handler](https://github.com/mjbradvica/ChainStrategy?tab=readme-ov-file#using-a-base-handler) or [constraint](https://github.com/mjbradvica/ChainStrategy?tab=readme-ov-file#handler-constraints).

Moving from a service-based approach to a chain of responsibility provides numerous advantages, the most notable being the increased ease of testing. The focus on testing makes it easier for you and other developers on your team to modify any software going forward. When you focus on putting testing first, the planets will align for your software. Using a chain of responsibility is one way to focus on testing over pure code.
