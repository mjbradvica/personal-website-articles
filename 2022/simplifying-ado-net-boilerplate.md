# Simplifying ADO.NET Boilerplate

When using [ADO.NET](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/ado-net-overview), every command you dispatch needs a certain amount of boilerplate.

Each command requires us to:

1. Open a connection
2. Create the command
3. Execute the command
4. Map the result set to an object for queries
5. Close the connection

Since none of these requirements needs to change from command to command, we can extract it as common logic. A base class can encapsulate the repeated logic while only accepting what is required via parameters.

## Our Base Class

Considering every command issued to our database is a query or non-query, we can express this through two methods.

Our base class:

```csharp
public abstract class BaseRepository
{
    private readonly SqlConnection _sqlConnection;

    protected BaseRepository(SqlConnection sqlConnection)
    {
        _sqlConnection = sqlConnection;
    }

    protected async Task<int> ExecuteCommand(string sql, CancellationToken cancellationToken = default)
    {
        await _sqlConnection.OpenAsync(cancellationToken);

        var transaction = _sqlConnection.BeginTransaction();

        var sqlCommand = new SqlCommand(sql, _sqlConnection, transaction);

        int result;

        try
        {
            result = await sqlCommand.ExecuteNonQueryAsync(cancellationToken);

            await transaction.CommitAsync(cancellationToken);
        }
        catch (Exception)
        {
            await transaction.RollbackAsync(cancellationToken);

            throw;
        }
        finally
        {
            await _sqlConnection.CloseAsync();
        }

        return result;
    }

    protected async Task<IReadOnlyList<TEntity>> ExecuteQuery<TEntity>(string sql, Func<SqlDataReader, TEntity> conversionFunc, CancellationToken cancellationToken = default)
    {
        await _sqlConnection.OpenAsync(cancellationToken);

        var sqlQuery = new SqlCommand(sql, _sqlConnection);

        var result = new List<TEntity>();

        try
        {
            var sqlDataReader = await sqlQuery.ExecuteReaderAsync(cancellationToken);


            while (await sqlDataReader.ReadAsync(cancellationToken))
            {
                result.Add(conversionFunc.Invoke(sqlDataReader));
            }
        }
        catch (Exception)
        {
            // Log Exception

            throw;
        }
        finally
        {
            await _sqlConnection.CloseAsync();
        }

        return result;
    }
}
```

Breaking down our base class:

- We accept an SqlConnection via the constructor.
- Our ExecuteCommand method will accept some SQL, wrap the command in a transaction, and--in the event of some undefined behavior--our transaction will rollback
- Our ExecuteQuery method will accept some SQL alongside a mapping function to convert a result set into the type we specify.

Now, we can now reuse this base class throughout our entire application.

## Implementation

For our example, we will use a simple Customer class:

```csharp
public class Customer
{
    public Customer(Guid id, string name)
    {
        Id = id;
        Name = name;
    }

    public Customer(string name)
        : this(Guid.NewGuid(), name)
    {
    }

    public Guid Id { get; }

    public string Name { get; }
}
```

Our CustomerRepository will implement our base class and pass the required parameters:

```csharp
public class CustomerRepository : BaseRepository
{
    public CustomerRepository(SqlConnection sqlConnection)
        : base(sqlConnection)
    {
    }

    public async Task<int> AddCustomer(Customer customer)
    {
        return await ExecuteCommand(
            $"INSERT INTO dbo.Customer VALUES ('{customer.Id}', '{customer.Name}')");
    }

    public async Task<IReadOnlyList<Customer>> GetAllCustomers()
    {
        return await ExecuteQuery(
            "SELECT * FROM dbo.Customer",
            reader => new Customer(
                    reader.GetGuid(0),
                    reader.GetString(1)));
    }
}
```

Our example above shows us passing the SqlConnection to our base class and utilizing two methods--one to add a customer and one to retrieve all available customers. We only need to pass the SQL and mapping function to our methods. Our code base is significantly smaller compared to writing every method using the necessary boilerplate.

## Query Reuse

We now have a wonderful situation, wherein our mapping function can be re-used for different queries. If we want to add a method in order to return a single customer by their identifier, the implementation would be quick and easy.

Our customer repository would now become:

```csharp
public class CustomerRepository : BaseRepository
{
    private readonly Func<DbDataReader, Customer> _customerFunc = reader =>
        new Customer(
        reader.GetGuid(0),
        reader.GetString(1));

    public CustomerRepository(SqlConnection sqlConnection)
        : base(sqlConnection)
    {
    }

    public async Task<int> AddCustomer(Customer customer)
    {
        return await ExecuteCommand($"INSERT INTO dbo.Customer VALUES ('{customer.Id}', '{customer.Name}')");
    }

    public async Task<IReadOnlyList<Customer>> GetAllCustomers()
    {
        return await ExecuteQuery("SELECT * FROM dbo.Customer", _customerFunc);
    }

    public async Task<Customer> GetById(Guid id)
    {
        return (await ExecuteQuery($"SELECT TOP(1) * FROM dbo.Customer WHERE Id='{id}'", _customerFunc)).First();
    }
}
```

We have extracted our mapping function and moved it to a variable. This allows us to use it for our new method--further reducing our codebase!

## Conclusion

If what we have done today feels very similar to Dapper, you are correct. Dapper, itself, is a thin wrapper around ADO.NET that will execute SQL, but also performs some automatic object mapping. The drawback is that Dapper is incapable of mapping complex objects--which means we still need to conduct some manual mapping if we want to enforce encapsulation in the domain.

Our implementation gives us a lot of the same niceties as Dapper, but we only need a single conversion to preserve our domain's encapsulation.
