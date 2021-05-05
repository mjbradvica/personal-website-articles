# The Purpose of the Repository Pattern

The Repository Pattern is more than three decades old, While its purpose has shifted over the years, it still provides a simple solution to constrain your data access code.

The Repository Pattern is an interface:

```csharp
public interface IWidgetRepository
{
    Task AddWidget(Widget widget);

    Task<Widget> GetWidgetById(Guid id);

    Task DeleteWidgetById(Guid id);
}
```

Implement the interface, inject where needed, and go.

Notice, I said interface because this pattern does not imply any of the following: Dapper, ORM, EntityFramework, Unit of Work, SQL, DbSet, or LINQ.

It's merely a [Fascade](https://en.wikipedia.org/wiki/Facade_pattern) enforcing [Interface Segregation](https://en.wikipedia.org/wiki/Interface_segregation_principle).

Microservices, Service Layer, CQRS, DDD. None of these matter.

## It Constrains Options

What is a better engineered solution: three options or unlimited options? I would argue that fewer options are always better.

More options equals more bugs and a higher rate of entropy.

## It Makes Testing Easier

Check out this application service level code:

```csharp
public async Task<Widget> FindWidget(Guid id)
{
    Widget widget;

    // Retrieve widget from some data source.

    if (widget != null)
    {
        return widget;
    }

    return Widget.EmptyWidget();
}
```

How do you test this code? Repository. 1) Mock the method. 2) Assert the result.

Raw disk, file, or database access. Good luck. Each of those options requieres multiple paragraphs in its own right.

Good software reduces options. The Repository Pattern is no different in that it only provides what has been deemed necesary--interface segregation in the purest form.
