# Encapsulating Your Domain with Dapper

[Dapper](https://github.com/DapperLib/Dapper) is a wonderful micro-ORM that can accept any SQL and return a strongly typed C# object. The library requires very little setup for the developer and serves as a step-up from working with the lower level ADO.NET libraries.

Compared to raw ADO, you just need:

```csharp
public async Task<IEnumerable<MyObject>> GetAll()
{
    return await _sqlConnection.QueryAsync<MyObject>("SELECT * from dbo.MyTable");
}
```

Dapper gives you a powerful way of performing queries with minimal overhead. The issue, however, is the lack of encapsulation support for mapped objects.

Your objects that are returned from Dapper (for the most part) need to be POCOs. Dapper is able to map simple types to constructors. Yet, complex types are outside of the libraries' ability to construct and map to a custom constructor.

So, while this is acceptable for Dapper:

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

This is not acceptable:

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

> Note: The ZonedDateTime type comes from the NodaTime library.

Dapper requires us to choose between POCOs and a rich domain. However, we can use an alternative that allows us to utilize Dapper while keeping the rich and encapsulated domain that we desire. It just requires us to have Dapper return a simple POCO, which represents a one-to-one relationship with our database tables that will map to a domain object.

We now express our Dapper object as a regular POCO:

```csharp
public class CustomerRecord
{
    public Guid Id { get; set; }

    public string Name { get; set; } = string.Empty;

    public long Timestamp_Instant { get; set; }

    public string Timestamp_DateTimeZoneId { get; set; } = string.Empty;
}
```

We then utilize a LINQ Select statement to map our POCO to the domain object we desire:

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

We can do this simply by putting our mapper in the record itself:

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

Then, we call our method as so:

```csharp
public async Task<IReadOnlyList<Customer>> GetAllCustomers()
{
    return (await _sqlConnection.QueryAsync<CustomerRecord>("SELECT * FROM dbo.Customer"))
        .Select(record => record.FromRecord())
        .ToList();
}
```

> It is important to keep our POCOs in the same project as our data access code. The domain or application layers should not be aware they exist.

We can now use both Dapper and keep our rich encapsulated domain. This gives us the best of both worlds--the simplicity of Dapper and the correctness of encapsulation.

## You May Not Need Dapper

The main reason people decide to move away from ADO.NET is because Dapper removes much of the required boilerplate. A developer can just write some SQL and get an object back with zero hassle. The irony of wanting to keep our application encapsulated with Dapper is that we are still required to perform the most frustrating part about ADO: the manual mapping process.

The correct decision may be a simple base class that removes the boilerplate for us. If we must remove the biggest advantage about Dapper, the prudent decision may be to not use it in the first place.

Encapsulating our domain is far more important than how we interact with our data.
