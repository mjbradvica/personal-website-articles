# Chain of Responsibility adopted for Dependency Injection

The [Chain of Responsibility](https://en.wikipedia.org/wiki/Chain-of-responsibility_pattern) is an original "Gang of Four" pattern that solves chained function calls. It's main purpose is to allow for an operation to occur without the individual steps having any knowledge of any other step involved.

Unfortunately, many examples fail to show how to properly integrate the pattern into your application properly. You end up with bloated classes that look something like this.

```csharp
public class BadChainCreationHandler : IRequestHandler<Widget>
{
    private readonly IFirstDependency _firstDependency;
    private readonly ISecondDependency _secondDependency;
    private readonly IThirdDependency _thirdDependency;

    public BadChainCreationHandler(IFirstDependency firstDependency, ISecondDependency secondDependency, IThirdDependency thirdDependency)
    {
         _firstDependency = firstDependency;
         _secondDependency = secondDependency;
         _thirdDependency = thirdDependency;
    }

    public async Task<Widget> Handle(Widget widget)
    {
        var thirdHandler = new ThirdChainHandler(_thirdDependency, null);
        var secondHandler = new SecondChainHandler(_secondDependency, thirdHandler);
        var firstHandler = new FirstChainHandler(_firstDependency, secondHandler);

        return await firstHandler.Handle(widget);
    }
}
```

We have an application service level handler that does something with a fictitious widget object. The issue that that the handler is responsible for both the creation of the chain and its execution. In the spirit of SRP, the handler should not be aware of how the chain is created.

Our solution is quite simple, a factory.

```csharp
public interface IChainFactory<T>
{
    IChainHandler<T> CreateChain();
}
```

Our handler can now become...

```csharp
public class GoodChainCreationHandler : IRequestHandler<Widget>
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

And our factory is now responsible for the creation of our chain.

```csharp
public class WidgetChainFactory : IChainFactory<Widget>
{
    private readonly IFirstDependency _firstDependency;
    private readonly ISecondDependency _secondDependency;
    private readonly IThirdDependency _thirdDependency;

    public WidgetChainFactory(IFirstDependency firstDependency, ISecondDependency secondDependency, IThirdDependency thirdDependency)
    {
        _firstDependency = firstDependency;
        _secondDependency = secondDependency;
        _thirdDependency = thirdDependency;
    }

    public IChainHandler<Widget> CreateChain()
    {
        var thirdHandler = new ThirdChainHandler(_thirdDependency, null);
        var secondHandler = new SecondChainHandler(_secondDependency, thirdHandler);
        var firstHandler = new FirstChainHandler(_firstDependency, secondHandler);

        return firstHandler;
    }
}
```

Our factory is now only responsible for the creation aspect. And the handler is only responsible for starting the chain. Testing becomes easier since you have reduced the number of dependencies in your handler. And lastly, we have enforced consistency in the application. Keeping the creation of objects inside factories.
