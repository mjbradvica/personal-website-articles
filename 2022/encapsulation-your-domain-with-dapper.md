# Encapsulating Your Domain with Dapper

[Dapper](https://github.com/DapperLib/Dapper) is a wonderful micro-ORM that can accept any SQL and return a strongly typed C# object. The library requires very little setup for the developer and serves as a step-up from working with the lower level ADO.NET libraries.

Compared to raw ADO you just need:

```csharp
public async Task<IEnumerable<MyObject>> GetAll()
{
    return await _sqlConnection.QueryAsync<MyObject>("SELECT * from dbo.MyTable");
}
```

Dapper gives you a powerful way of performing queries with minimal overhead. The issue however, is the lack of encapsulation support for mapped objects.

Your objects that are returned from Dapper for the most part need to be POCO's. Dapper is able to map simple types to constructor. However complex types are outside of the libraries ability to construct and map to a custom constructor.

So while this is acceptable for Dapper:

```csharp
public class MyObject
{
    public MyObject(Guid id, DateTime timestamp)
    {
        Id = id;
        Timestamp = timestamp;
    }

    public Guid Id { get; }

    public DateTime Timestamp { get; }
}
```

This is not:

```csharp
public class MyObject
{
    public MyObject(Guid id, Name name, ZonedDateTime timestamp)
    {
        Id = id;
        Name = name;
        Timestamp = timestamp;
    }

    public Guid Id { get; }

    public Name Name { get; }

    public ZonedDateTime Timestamp { get; }
}
```

With Dapper you need to choose between POCO's and a rich domain. However we can use an alternative that allows us to utilize Dapper but keep the rich and encapsulated domain that we desire. It just requires us to have Dapper return a simple POCO which represents a one-to-one relationship with our database tables that will will map to a domain object.

We will now express our Dapper object as a regular POCO:

```csharp
public class CustomerRecord
{
    public Guid Id { get; set; }

    public string Name { get; set; } = string.Empty;

    public long Timestamp_Instant { get; set; }

    public string Timestamp_DateTimeZoneId { get; set; } = string.Empty;
}
```

We will then utilize a LINQ Select statement to map our POCO to the domain object we desire:

```csharp
public async Task<IReadOnlyList<Customer>> GetAllCustomers()
{
    return (await _sqlConnection.QueryAsync<CustomerRecord>("SELECT * FROM dbo.Customer"))
        .Select(record => new Customer(
            record.Id, 
            new Name(record.Name), 
            new ZonedDateTime(Instant.FromUnixTimeTicks(record.Timestamp_Instant), DateTimeZoneProviders.Bcl[record.Timestamp_DateTimeZoneId])))
        .ToList();
}
```

We can simply this by putting our mapper in the record itself:

```csharp
public class CustomerRecord
{
    public Guid Id { get; set; }

    public string Name { get; set; } = string.Empty;

    public long Timestamp_Instant { get; set; }

    public string Timestamp_DateTimeZoneId { get; set; } = string.Empty;

    public Customer FromRecord()
    {
        return new Customer(Id, new Name(Name), new ZonedDateTime(Instant.FromUnixTimeTicks(Timestamp_Instant), DateTimeZoneProviders.Bcl[Timestamp_DateTimeZoneId]));
    }
}
```

This breaks encapsulation in multiple ways:

1. Objects can be created in an infinite number of possible states
2. Object properties can be changed at any time for any reason
3. Object validation at creation is not internalized

We need our object to look like this:

```csharp
public class MyObject
{
    public MyObject(DateTime timestamp)
        :this(Guid.NewGuid(), timestamp)
    {
    }

    public MyObject(Guid id, DateTime timestamp)
    {
        Validator.ValidateId(id);
        Validator.ValidateDateTime(timestamp);

        Id = id;
        Timestamp = timestamp;
    }

    public Guid Id { get; }

    public DateTime Timestamp { get; }

    //additional methods and properties
}
```

Our object now as full encapsulation over its creation process and public API. Its' validation is also internalized and will reject non-valid parameters.

The issue is that Dapper is unable to map properly to private setters or call custom constructors.

## You May Just Not Need Dapper

Encapsulating our domain is more important than how we interact with our data.
