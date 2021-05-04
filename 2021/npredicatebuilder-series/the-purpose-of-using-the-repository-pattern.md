# The Purpose of the Repository Pattern

The Repository pattern has been around a long time. And while its purpose has shifted over the years. It still provides an easy way to constrain your data access code.

It's just an interface after all:

```csharp
public interface IWidgetRepository
{
    Task AddWidget(Widget widget);

    Task<Widget> GetWidgetById(Guid id);

    Task DeleteWidgetById(Guid id);
}
```

Just implement the interface, inject when needed, and go.

Notice I said interface. That's because this pattern does not imply any of the following: Dapper, ORM, EntityFramework, Unit of Work, SQL, DbSet, or LINQ.

It's just a [Fascade](https://en.wikipedia.org/wiki/Facade_pattern) enforcing [Interface Segregation](https://en.wikipedia.org/wiki/Interface_segregation_principle).

Microservices, Service Layer, CQRS, DDD. None of that matters.

## It constrains options

What is a better engineered solution, three options or unlimited options?

What do you believe will cause more bugs?

What do you believe will introduce more entropy into your solution?

## It makes testing easier

Look at this application service level code:

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

How do you test this code?

Repository, its rather easy. Mock the method. Assert the result.

Raw disk, file, or database access. Good luck. Each of those options would require multiple paragraphs in their own right.

Good software limits options. The Repository Pattern is no different in that it only provides what has been deemed necesary. Interface segregation in the purest form.
