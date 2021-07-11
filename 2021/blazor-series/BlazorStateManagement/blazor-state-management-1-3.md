<!-- Individual Events -->

# Blazor In-Memory State Management Part 1 of 3

In traditional web applications state is stored in the database, cookies, or URL. While all those options are still viable in Blazor, we have new technique available in the form of in-memory state. Our state can be updated via publishing an event from a component that any other component in our application may subscribe to. The barrier to entry for creating our in-memory state container is very small.

Note: I am using the standard Blazor template that is generated when creating any new Blazor project.

A quick overview of what we will be implementing:

- Events that we will publish
- A composition root for our events
- A way for components to subscribe and un-subscribe from events

First, an interface to describe an event:

```csharp
public interface IAction
{
}
```

We are using a blank interface as a way to constrain what objects are events.

We now need to define a few events:

```csharp
public class DecreaseCounter : IAction
{
}
```

and...

```csharp
public class IncreaseCounter : IAction
{
}
```

These two classes will represent a type of event that we wish to propagate through out the entire application. We now need to add a composition root that will be the source for publishing our events.

```csharp
public class StateStore
{
    private readonly Dictionary<Type, Action<IAction>> _actions;

    public StateStore()
    {
        _actions = new Dictionary<Type, Action<IAction>>
        {
            { typeof(IncreaseCounter), action => IncreaseCounterHandler?.Invoke(this, action as IncreaseCounter) },
            { typeof(DecreaseCounter), action => DecreaseCounterHandler?.Invoke(this, action as DecreaseCounter) },
        };
    }

    public void Publish(IAction action)
    {
        _actions[action.GetType()].Invoke(action);
    }

    public event EventHandler<IncreaseCounter> IncreaseCounterHandler;
    public event EventHandler<DecreaseCounter> DecreaseCounterHandler;
}
```

Our StateStore has the following properties and methods:

- A Dictionary that acts as a switch-case that will trigger an event handler depending on what event was published
- A "Publish" method that our components will use to publish an event
- Numerous events that components may choose to subscribe to

We need to ensure that any component in our application can have access to our StateStore. To enable this we will pass the StateStore down through out the entire application as a cascading parameter.

In our App.razor file:

```csharp
<Router AppAssembly="@typeof(Program).Assembly" PreferExactMatches="@true">
    <Found Context="routeData">
        <CascadingValue Value="Store">
            <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
        </CascadingValue>
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code
{
    public StateStore Store { get; set; } = new StateStore();
}

```

We added a "CascadingValue" that wraps our MainLayout component. In the code section, we initialized our object. Any component may now  publish or subscribe to events.

The default counter component contains a button that when clicked, keeps track of the click counts internally. We are going to modify the component to publish events to our StateStore instead. We will then create a new component that listens and reacts accordingly.

```csharp
@page "/counter"
@using StateManagement.State
@using StateManagement.State.Events

<h1>Counter</h1>

<button class="btn btn-primary" @onclick="() => Store.Publish(new IncreaseCounter())">Increase Counter</button>
<button class="btn btn-primary" @onclick="() => Store.Publish(new DecreaseCounter())">Decrease Counter</button>

@code {
    [CascadingParameter]
    public StateStore Store { get; set; }
}

```

Our component now accepts the StateStore as a cascading parameter which we will use to publish counter events. We have two button that will publish an event to either increase or decrease the counter.

We now need a component to react to the events that are being published:

```csharp
@using StateManagement.State
@using StateManagement.State.Events
@implements IDisposable

<p>Current count: @CurrentCount</p>

@code {
    [CascadingParameter]
    public StateStore Store { get; set; }

    public int CurrentCount { get; set; }

    protected override void OnInitialized()
    {
        Store.IncreaseCounterHandler += IncreaseCount;
        Store.DecreaseCounterHandler += DecreaseCount;
        base.OnInitialized();
    }

    private void IncreaseCount(object? sender, IncreaseCounter e)
    {
        ++CurrentCount;
        StateHasChanged();
    }

    private void DecreaseCount(object? sender, DecreaseCounter e)
    {
        --CurrentCount;
        StateHasChanged();
    }

    public void Dispose()
    {
        Store.IncreaseCounterHandler -= IncreaseCount;
        Store.DecreaseCounterHandler -= DecreaseCount;
    }
}

```

There is a lot happening in this component, it can be summarized by the following:

- Our counter subscriber component accepts the StateStore as a cascading parameter just like the counter component
- We declare a variable for the "CurrentCount" that will be displayed in the UI
- We override the "OnInitialized" method to subscribe to the events
- We implement a method for each event that updates the "CurrentCount" property and calls the "StateHasChanged" method to notify the component to re-render
- We implement the "IDisposable" interface to un-subscribe from the events

I updated the "index.razor" component to display both components for simplicity.

```csharp
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Counter/>
<CounterSubscriber/>
```

When we click the buttons in our Counter component, the CounterSubscriber should respond:

![Result](https://media.giphy.com/media/ryVEBNJJP4wEplqRVZ/giphy.gif)

This part one was concerned with getting our StateStore up and running. One of the drawbacks with the current implementation is that we have to subscribe to each event, leading to tedious boiler plate code. In part two, we will refactor our StateStore to contain the state in a central location to reduce the code in our subscribers.
