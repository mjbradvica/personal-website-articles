# Return Enums As Objects

When working with any presentation layer be it an REST API, MVC, or GraphQL app, you should always return the correct abstraction for the type you are working with.

Enums are a notoriously bad abstraction to pass to clients because they, by default, lack the necessary details a client needs to discern them.

API's by default through Open API clients such as Swagger or Scalar, are only aware of the values an enum has, not the meaning behind it.

So the following enum:

```csharp
public enum Airplane
{
    Boeing = 0,
    Airbus = 1,
    Embraer = 2,
}
```

Will only show up in Swagger or Scalar like so:

```csharp
[
  0,
  1,
  2
]
```

How would you know what values to popular in a Select input if you had half the information necessary?

Enums are more than just an integer value, they are the integer plus the linguistic value that gives the integer significance.

An enum is really an integer value with a string name. It may be modeled like so:

```csharp
public class EnumResponse
{
    public EnumResponse(Enum enumeration)
    {
        Value = Convert.ToInt32(enumeration);
        Name = enumeration.ToString();
    }

    [Required]
    public int Value { get; init; }

    [Required(AllowEmptyStrings = false)]
    public string Name { get; init; }
}
```

> I have added the attributes so Open API knows that needs are non-nullable fields.

With a proper abstraction, Open API is able to discern the fullness of the enumeration.

```csharp
[
  {
    "value": 0,
    "name": "Boeing"
  },
  {
    "value": 1,
    "name": "Airbus"
  },
  {
    "value": 2,
    "name": "Embraer"
  }
]
```

By using an object, you also avoid the awkwardness of configuring Swagger to [describe enums as strings](https://swagger.io/docs/specification/v2_0/enums/).

Using the correct abstraction can make a big difference for you the developer and your clients. You avoid frustration on both ends and keep your application simple and easy to understand.
