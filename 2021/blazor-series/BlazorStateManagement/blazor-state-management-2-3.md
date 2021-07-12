# Blazor In-Memory State Management Part 2 of 3

In the first part of our series, we created a state store which allowed us to publish and subscribe to events in our application. This allowed us to have a separate component that listened to events published from our counter component. The biggest drawback to our current implementation is the amount of boiler plate in our subscription components. They must implement at least three methods and an additional method per event to which they are subscribed. Another issue is that every subscription component that responds to events needs to keep its own version of what the current state may be. An easier and simpler implementation would be for the state store to hold a single copy of the applications' state the entire time. Each subscribing component would listen to a single event indicating all subscribers to re-render.

We need to introduce an object to centralize our application’s state:

```csharp
public class InitialState
{
    public int Count { get; set; }
}
```

Our count property is now located in a single location, enabling us to centralize our application's state to only one location and also to reduce the number of events handlers.

We will use a single event to notify subscribers that the application state has been updated:

```csharp
public class ApplicationStateChanged
{
}
```

We can now update our StateStore to handle our count property internally and publish our new event.

```csharp
public class StateStore
{
    private readonly Dictionary<Type, Action<IAction>> _actions;

    public InitialState State { get; } 

    public StateStore()
    {
        State = new InitialState();

        _actions = new Dictionary<Type, Action<IAction>>
        {
            { typeof(IncreaseCounter), action => UpdateState(state => ++state.Count) },
            { typeof(DecreaseCounter), action => UpdateState(state => --state.Count) },
        };
    }

    public void Publish(IAction action)
    {
        _actions[action.GetType()].Invoke(action);
    }

    private void UpdateState(Action<InitialState> stateAction)
    {
        stateAction.Invoke(State);
        ApplicationStateChangedHandler?.Invoke(this, new ApplicationStateChanged());
    }

    public event EventHandler<ApplicationStateChanged> ApplicationStateChangedHandler;
}
```

Our new StateStore has been simplified with only one event. In other words, the entire state of our application is contained in a single object. When we publish an event to our state store, the state will be updated, and an "ApplicationStateChanged" event will be published.

Our component that subscribes to our events can now be a lot simpler:

```csharp
@using StateManagement.State
@implements IDisposable

<p>Current count: @Store.State.Count</p>

@code {
    [CascadingParameter]
    public StateStore Store { get; set; }

    protected override void OnInitialized()
    {
        Store.ApplicationStateChangedHandler += ReRender;
        base.OnInitialized();
    }

    private void ReRender(object sender, ApplicationStateChanged e) => StateHasChanged();

    public void Dispose() => Store.ApplicationStateChangedHandler -= ReRender;
}
```

We have removed a majority of boiler plate code from our subscribing component. We have also removed our local reference to the current count.

While this implementation is a major upgrade over the previous one, we can improve it yet further. Since our subscribing components need only to listen to a single event, we consequently have a common interface. Whenever we have a common interface in our code, we can extract it into an interface or base class. In part three of this series, we will extract the remainder of the subscriber code into a base class that will allow us to continue the simplification process.
