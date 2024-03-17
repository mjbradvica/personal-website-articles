# Prefer Empty Objects over Compiler tricks

## Listen to the Compiler

There are certain situations in C# where you have nullable references that are not initialized in the constructor. The most common situation is Entity Framework navigation properties.

The following shows an Entity Framework entity "Airplane" that has a navigation property of "AircraftType".

```csharp
public class Airplane
{
    // other fields and methods

    public AircraftType AircraftType { get; }
}
```

This will produce a [CS8618 - Resolve Nullable Warnings](<https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/compiler-messages/nullable-warnings?f1url=%3FappId%3Droslyn%26k%3Dk(CS8618)>) error. The compiler is telling us we have a nullable object that is leaving the constructor without being initialized. This will throw a null reference exception if we attempt to de-reference the object.

The issue is that **we** know that the object will be initialized, but the compiler does not.

The most common solution is to use the "default!" trick to tell the compiler to ignore this nullable situation.

```csharp
public class Airplane
{
    public Airplane()
    {
        AircraftType = default!;
    }

    public AircraftType AircraftType { get; }
}
```

This is not an ideal situation because we are silencing the compiler. We are leaving our application in a possible situation of undefined behavior. Unit testing or running the application without the database will yield possible errors. We need to ensure that de-referencing the property in any situation will not throw exceptions.

> You should adhere to a strict policy of putting the compiler first. Nothing, including you, is above it.

Instead of using the "default!" trick, use an object that contains empty data to satisfy the compiler. This is a variant of the [null object pattern](https://en.wikipedia.org/wiki/Null_object_pattern). Using an empty object will use default values in place of real-life data.

```csharp
public class AircraftType
{
    // implementation

    public static AircraftType EmptyObject()
    {
        return new AircraftType(Guid.Empty(), string.Empty, 0);
    }
}
```

Your other class will be updated like so:

```csharp
public class Airplane
{
    public Airplane()
    {
        AircraftType = AircraftType.EmptyObject();
    }

    public AircraftType AircraftType { get; }
}
```
