# Prefer Chains to Multiple Service Calls

## Multiple Service calls are Ambiguous

Multiple services calls occur when you have a service calls other services to complete an operation.

```csharp
public class ServiceA
{
    private readonly ServiceB _serviceB;

    public ServiceA(ServiceB serviceB)
    {
        _serviceB = serviceB;
    }

    public Data Foo()
    {
        var goo = _serviceB.DoSomething();

        // logic here
    }
}
```

There is a chance that this kind of behavior continues with "ServiceB" then calling a "ServiceC" until the desired outcome is reached.

This is unnecessary and somewhat confusing. The term "Service" is already ambiguous at best and is one of the most overloaded terms in software. Anything and everything can be a service or manager. It behooves us as engineers to remove any ambiguity in our software because we are coding for others, not just ourself.

## Chain of Responsibility to the Rescue

Compared to multiple service calls. A Chain of responsibility gives you:

- A single contract that constrains the scope of the operation
- Explicitly named handlers that help guid SRP (Single Responsibility Principle)
- Loose coupling of chain handlers, each handler has no knowledge of what comes before or after it

## Chains Force Coding to a Contract

Services parameters have a tendency to get bloated with long parameter lists.

```csharp
var result = _myService.DoSomething(first, second, third, fourth, fifth);
```

Testing this requires a lot of mocks.

```csharp
_mockService.Setup(x => x.DoSomething(
    It.IsAny<int>(),
    It.IsAny<int>(),
    It.IsAny<int>(),
    It.IsAny<int>(),
    It.IsAny<int>()))
    .Returns(new MyResult());
```

Having an excessive amount of parameters becomes a problem when testing or refactoring. Changing the parameters means you need to update any test or reference to that method call.

A chain of responsibility has a single in-out request style.

```csharp
public class MyChainRequest
{
    // properties and methods
}
```

You can add or remove methods and properties at will to a chain request object-which unlike a normal method parameter list, will not required cascading code changes.

## Chains Still Allow for Reuse

Chains are not just for single use operations. You can allow for re-use through constraints.

A handler may define its request object to be of a certain interface allowing for it to be used multiple times.

Provided the following interface:

```csharp
interface IFoo {
    Guid Id { get; }
    void UpdateRequest(Data data);
}
```

A handler may do the following:

```csharp
public class MyHandler : ChainHandler<MyRequest>
    where MyRequest : IFoo
{
    public MyHandler(IChainHandler<MyRequest>? handler)
        : base(handler)
    {
    }


    public override async Task<MyRequest> DoWork(MyRequest request, CancellationToken cancellationToken)
    {
        var newData = _dataSource.DoSomething(request.Id, cancellationToken);

        request.UpdateRequest(newData);
    }
}
```

## Chains are better than internal Mediator calls

The Mediator pattern is a great behavior pattern that sits near the root of your application for kicking off business use cases.

Using a mediator in your application layer compared to a chain of responsibility has the following drawbacks:

1. More boilerplate, you need to define a request and response object per handler.
2. Doesn't solve the core issue of having to call multiple services/handlers to finish an operation.
3. Mediator calls in the application layer are akin to a service locator.
