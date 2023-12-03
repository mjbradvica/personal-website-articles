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

## Chains Force Coding to a Contract

A chain of responsibility has a single in-out request style.

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