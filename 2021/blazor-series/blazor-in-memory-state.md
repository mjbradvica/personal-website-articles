# Blazor In Memory State Store

If you come from React, you may be aware of [Redux](https://redux.js.org/). A library for managing state in your application. The most common application of a state container is to allow for components to be notified of events in other components. This is commonly accomplished by passing a callback function from a parent component to a child.

How do you know I may need a state store?

Do you have a component that needs to be notified about an event from a different component that may be dozens of level away in the render tree? That's where a state container may be applicable.

Our state container will need a couple of pieces:

A place to store the state of our application - Our Store

Events that get dispatched to the store - Our Actions

A way to update the state of our application - The Reducer

**Our situation**: We have a component that needs to know the current value that resides in a component that resides very far away in the component tree.

```csharp
@page "/iNeedState"

<p>The current value is @Value.</p>

@code{
    public int Value { get; set; }
}
```

The component that will be publishing its state:

```csharp
@page "iSendState"

<button @onclick="">Increase Count!</button>
<button @onclick="">Decrease Count!</button>

@code{
}
```

**Creating our State Store**

We need an interface to use as a dummy parameter for our store. All of our actions will implement this interface.

```csharp
public interface IAction
{
}
```

Lets define some actions that will represent us increasing our decreasing the counter:

```csharp
public class IncreaseCounter : IAction
{
    public IncreaseCounter(int amount)
    {
        Amount = amount;
    }

    public int Amount { get; }
}
```

```csharp
public class DecreaseCounter : IAction
{
    public DecreaseCounter(int amount)
    {
        Amount = amount;
    }

    public int Amount { get; }
}
```

A class that contains the current state of our application:

```csharp
public class InitialState
{
    public int CurrentCount { get; set; }
}
```

A store that holds our actions, initial state, and reducer.

```csharp
public class StateStore
{
    public InitialState State { get; }
    private readonly Dictionary<Type, Action<IAction>> _reducers;

    public StateStore()
    {
        State = new InitialState();

        _reducers = new Dictionary<Type, Action<IAction>>
        {
            { typeof(IncreaseCounter), action => State.Value += (IncreaseCounter) action).Amount},
            { typeof(DecreaseCounter), action => State.Value += (DecreaseCounter) action).Amount},
        };    
    }

    public void Dispatch(IAction action)
    {
        _reducers[action.GetType()].Invoke(action);
    }
}
```

Our StateStore holds our InitialState, a dictionary of reducers, and a dispatch method.

**Adding our state container to the application**

In our App.cs, we wrap the layout of our application with a cascading parameter and pass in our state store. This will allow any component to access the state store.

```csharp
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <CascadingValue Value="StateStore">
            <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
        </CascadingValue>
    </Found>
</Router>

@code
{
    public StateStore StateStore { get; set; } = new StateStore();
}
```

Our component that relies on the state can now become:

```csharp
@page "/iNeedState"

<p>The current value is @Store.State.CurrentCount</p>

@code{
    [CacasdingParameter]
    public StateStore Store { get; set; }
}
```

And our component that publishes its' state:

```csharp
@page "iSendState"

<button @onclick="() => Store.Dispatch(new IncreaseCounter(1))">Increase Count!</button>
<button @onclick="() => Store.Dispatch(new DecreaseCounter(1))">Decrease Count!</button>

@code{
    [CascadingParameter]
    public StateStore Store { get; set; }
}
```