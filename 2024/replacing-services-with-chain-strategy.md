# Replacing Services with ChainStrategy

## Fixing Injection Nightmares

A common issue in application code is the proliferation of injected services into application-level services and handlers.

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

Lots of injected services will translate one-to-one to lots of mocks in unit tests.

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

Multiple services have lots of hidden costs outside of excessive mocking for unit testing.

1. Many of these services have a single method. You incur a lot of overhead for just one method.
2. They break encapsulation, the application service does not need to know the details of how a process is performed.
3. Change is hard, updating one parameter forces downstream updates, a clear example of [shotgun surgery](https://en.wikipedia.org/wiki/Shotgun_surgery).

## A Clear Alternative

[ChainStrategy](https://github.com/mjbradvica/ChainStrategy) gives you a much more practical approach using the Chain of Responsibility pattern to solve the issue of breaking apart a large problem.

### Clearly Defined Entry and Egress

ChainStrategy forces you to define a single object that encompasses the state for an entire operation.

```csharp
public class MyChainRequest : ChainRequest
{
    // properties as needed
}
```

You may now add or remove properties at will without having to update service parameters downstream.

### Chain Handlers over Individual Services

With small handlers, you can focus on each handler doing only what is required, enforcing SRP (single responsibility principle) for an operation.

```csharp
public class MyChainHandler : ChainHandler<MyChainRequest>
{
    public MyChainHandler(IChainHandler? handler)
        : base(handler)
    {
    }

    public Task<MyChainRequest> DoWork(MyChainRequest request)
    {
        // implement and return
    }
}
```

Chain handlers give you the added advantage of:

1) Can having very specific declarative naming
2) Are autonomous, each handler does not know where it exists in the process
3) Are re-usable with either a [base handler](https://github.com/mjbradvica/ChainStrategy?tab=readme-ov-file#using-a-base-handler) or a [constraint](https://github.com/mjbradvica/ChainStrategy?tab=readme-ov-file#handler-constraints)

## Updated Application

With only one dependency needed for a complex operation. Your code base is simplified.

```csharp
public class MyHandler : IHandle<MyRequest, MyResponse>
{
    private readonly IChainFactory _chainFactory;

    public MyHandler(IChainFactory chainFactory)
    {
        _chainFactory = chainFactory;
    }

    public async Task<MyResponse> Handle(MyRequest request)
    {
        var result = await _chainFactory.Execute(new MyChainRequest());

        // rest of code
    }
}
```

And testing is much easier...

```csharp
[TestClass]
public class MyHandlerTests
{
    private readonly Mock<IChainFactory> _chainFactory;
    private readonly MyHandler _handler;

    public MyHandlerTests()
    {
        _chainFactory = new Mock<IChainFactory>();
        _handler = new MyHandler(_chainFactory.Object);
    }

    [TestMethod]
    public void MyHandler_IsCorrect()
    {
        // test code here
    }
}
```
