# Query Objects with NPredicateBuilder

## Introduction

This article assumes you have some basic knowledge or usage history of NPredicateBuilder. If not, check out [the documentation here](https://github.com/mjbradvica/NPredicateBuilder), it's a quick read.

## Quick Overview

A query object is a type of [Command](https://en.wikipedia.org/wiki/Command_pattern). It condenses most, if not all of a query into a single parameter.

NPredicateBuilder uses a simple interface that looks like the following:

```csharp
Expression<Func<T, bool>> SearchExpression { get; }
```

A search expression may contain N number of Where clauses that comprise a full query.

## Implementation

If you are using the standard pattern of a Repository for data access, you may add the following method below.

```csharp
public Task<List<Widget>> SearchEntities(IBaseQuery<Widget> query);
```

> Widget is just a placeholder for the entity type you are using.

Use the built-in extension method to apply the query. The following shows an example of EntityFramework.

```csharp
public class MyDataClass
{
    // Other code hidden for simplicity.

    public async Task<List<Widget>> SearchEntities(IBaseQuery<Widget> query)
    {
        return await _context.Widgets
        .NPredicateBuilderEFWhere(query)
        .ToListAsync();
    }
}
```

> Returning a List of T is ideal for queries in EF to insure against deferred execution issues.

### Adding Optional Ordering

You may add the option to order by proving a nullable order command as well.

```csharp
public Task<List<T>> SearchEntities(IBaseQuery<T> query, IBaseOrder<T>? order);
```

Implement like so:

```csharp
public async Task<List<Widget>> SearchEntities(IBaseQuery<Widget> query, IBaseOrder<Widget>? order = null)
{
    var result = _context.Widgets.NPredicateBuilderEFWhere(query);

    if (order != null)
    {
        result = result.NPredicateBuilderEFOrder(order);
    }

    return await result.ToListAsync();
}
```

Because NPredicateBuilder has extensions for EF, the query isn't executed until the last line, preserving performance.

### Before and After

You can see the potential to squash numerous methods down into a single interface. The effect is even more dramatic if you have multiple queries that share Where and Order clauses.

```csharp
public interface IWidgetData
{
    Widget? GetById(Guid id);
    List<Widget> ActiveWidgets();
    List<Widget> ClientWidgets(int clientId);
    // Potentially more methods here.
}
```

After...

```csharp
public interface IWidgetData
{
    List<Widget> SearchWidgets(IBaseQuery<Widget> query);
}
```

### Understanding the Tradeoff

There is a tradeoff with query objects. You are moving methods from your data access layer to the process of creating a unique query and/or order object.

In this respect, you are opening up your data layer to more possibilities with what developers may pass as an argument.

**Pros:**
Fewer methods in the data access interface
A single query or order object for logic
Singular, testable queries, and orders
Can move tests from integration to unit

**Cons:**
A more open data access API
Library dependency on NPredicateBuilder

You will need to weigh the benefits and drawbacks of your situation. If you choose to use query objects, NPredicateBuilder can be a helpful asset.
