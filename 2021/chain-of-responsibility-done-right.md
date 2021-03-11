# Chain of Responsibility. Done Right.

## Pattern Overview

## My Issue With Current Examples

### Current Examples



## Better Suited

### Base Code

In the following I will present two interfaces and a base class for the chain of responsibility pattern.

**Listing X** IChainHandler interface

```csharp
public interface IChainHandler<T>
{
    Task<T> Handle(T obj);
}
```

**Listing X** ChainHandler base class

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

The base class and interface should be 99% familiar with what you have seen so far. However, in order to encapsulate how the chain is being created we need to introduce a factory interface that will allow us to derive a class responsible for constructing our chain.

**Listing X** IChainFactory interface

```csharp
public interface IChainFactory<T>
{
    IChainHandler<T> CreateChain();
}
```

### Integrate Into Application

### Testing

## Conclusion

