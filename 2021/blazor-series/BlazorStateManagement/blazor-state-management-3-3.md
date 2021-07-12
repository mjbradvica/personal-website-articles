# Blazor In-Memory State Management Part 3 of 3

In the second entry of our Blazor in-memory series, we refactored our applications state into a single object located in our state store. This allowed us to reduce the number of events needed to publish from the store. Our subscribing components were then able to reduce the amount of boiler plate code required to subscribe and respond to events.

Our current implementation for subscribing events revolves around the common interface via the "ApplicationStateChanged" event. Each subscriber needs only to hold a reference to this event and re-render its content when the event is fired. This means that every subscribing component can now inherit from a base class that will implement the necessary logic.

Our base class for all subscribers:

```csharp
public class BaseSubscriber : ComponentBase, IDisposable
{
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

Our BaseSubscriber will contain all the logic necessary for subscribing, un-subscribing, and re-rendering our component when the application state has changed. This will allow us to simplify our counter subscriber to as follows:

```csharp
@using StateManagement.State
@inherits BaseSubscriber

<p>Current count: @Store.State.Count</p>

@code {
}
```

Our counter subscriber inherits from BaseSubscriber and now contains minimal code. Our final product is vastly smaller and easier to understand than our original implementation.

In-memory state management is just one way of tackling the issue of state in Blazor applications. When implemented correctly, your development team can have a easily understandable solution for passing around events and states in your application.
