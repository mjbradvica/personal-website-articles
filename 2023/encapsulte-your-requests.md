# Encapsulate Your Requests

## Privacy for Model Binding

Traditionally in order to have model binding work properly with an ASP.NET API or MVC project your request objects needed to have a minimum of private setters and an empty constructor.

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

With the onset of Swagger and Open API, the requirements have widened by now requiring full public setters as well. Public properties is how Swagger is able to distinguish your types.

This puts us as developers in a precarious situation-we want encapsulation, but we also know there are design issues that require us to compromise in certain places. However, this is an option we have that allows us to maintain encapsulation AND preserve the ability for Swagger to work properly.

The [JsonConstructor](https://learn.microsoft.com/en-us/dotnet/api/system.text.json.serialization.jsonconstructorattribute?view=net-7.0) attribute is a simple addition to any request being posted. It will also not interfere with any validation attributes that are annotated on properties as well.

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

    [Range(0, 5)]
    public string Status { get; }
}
```

It may appear to be superfluous at first, but encapsulation is not to be compromised with. Allowing public setters to proliferate in any application is an invitation for bugs and undefined behavior to fester.
