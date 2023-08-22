# Encapsulate Your Requests

## Privacy for Model Binding

Traditionally for model binding to work properly with an ASP.NET API, or an MVC project, your request objects need to have a minimum of private setters and an empty constructor.

```csharp
public class MyRequest
{
    public MyRequest()
    {
    }

    public Guid Id { get; private set; }

    public string Status { get; private set; }
}
```

Yet, with the introduction of Swagger and Open API, the requirements have widened by now requiring full public setters as well. Public properties is how Swagger is able to distinguish your types.

This puts developers in a precarious situation--we want encapsulation, but we also know that design issues that require will require to compromise in certain situations. However, an option allows us to maintain encapsulation AND preserve the ability for Swagger to work properly.

The [JsonConstructor](https://learn.microsoft.com/en-us/dotnet/api/system.text.json.serialization.jsonconstructorattribute?view=net-7.0) attribute is a simple addition to any request being posted. Importantly, it will not interfere with any validation attributes that are annotated on properties.

```csharp
public class MyRequest
{
    [JsonConstructor]
    public MyRequest(Guid id, string status)
    {
        Id = id;
        Status = status;
    }

    public Guid Id { get; }

    [Required]
    public string Status { get; }
}
```

Although it may appear to be superfluous at first, you should not compromise with encapsulation. Allowing public setters to proliferate in any application is an invitation for bugs and undefined behavior to fester.
