# Consistent Validation

I want to stress that I am talking about the validation of domain objects in this article. Validation of requests coming across the wire behave differently.

## The Problem Space

Why not just use the [IValidatableObject](https://learn.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations.ivalidatableobject?view=net-8.0) and call it a day?

While the interface is convenient, it has one fatal flaw. It requires validation to be executed outside of the constructor. With the "IValidatableObject" interface, you are required to call a separate method to ensure the object is valid. We want validation to be built-in and allow us to fail fast and easy.

Our situation is made obvious during unit testing. We want our tests and the state of our objects we test to reflect possible production values.

```csharp
public class Airplane : IValdatableObject
{
    // Properties and methods.

    public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)
    {
        // Implementation.
    }
}
```

In every unit test we would have to do the following.

```csharp
[TestMethod]
public void Airplane_SomeMethod_IsCorrect()
{
    var airplane = new Airplane();

    var validationContext = new ValidationContext(airplane);

    var results = airplane.Validate(context);

    if(!results.Any())
    {
        // keep going.
    }
}
```

Yes, you could put this behind some kind of helper method, but you would still have to remember to call said method for each unit test. When we validate in the constructor, we get the validation process built in. This turns into much more consistent behavior from our application.

## Validation Belongs in the Constructor

> Validation for domain objects should always be performed in the constructor.

I am using [FluentValidation](https://docs.fluentvalidation.net/en/latest/) for this example. You may choose any library you prefer.

Our domain object:

```csharp
public class Airplane
{
    public Airplane(Guid id)
    {
        Id = id;

        new AirplaneValidator().ValidateAndThrow(this);
    }

    public Guid Id { get; }

    // More properties and methods below.
}
```

Our validator:

```csharp
public class AirplaneValidator : AbstractValidator<Airplane>
{
    public AirplaneValidator()
    {
        RuleFor(airplane => airplane.Id).NotEqual(Guid.Empty);
        // Rest of our rules.
    }
}
```

## Return Invalid Objects Gracefully

Now that we have properly moved our validation to the constructor. We have a small problem, how do we handle invalid object that throw exceptions?

We can't return objects from constructors and static factory methods may not by fully supported by certain libraries or frameworks such as an ORM. We are stuck with constructors, but need a way to gracefully handle exceptions.

Our solution is a simple helper function that uses a callback.

We will use an envelope object to wrap our entity and validation result.

```csharp
public class ValidationEnvelope<TEntity>
{
    public ValidationEnvelope(bool isInvalid, TEntity entity, IEnumerable<ValidationFailure> errors)
    {
        IsInvalid = isInvalid;
        Entity = entity;
        Errors = errors;
    }

    public bool IsInvalid { get; }

    public TEntity Entity { get; }

    public IEnumerable<ValidationFailure> Errors { get; }

    public static ValidationEnvelope<TEntity> Success(TEntity entity)
    {
        return new ValidationEnvelope<TEntity>(false, entity, Enumerable.Empty<ValidationFailure>());
    }

    public static ValidationEnvelope<TEntity> Failure(IEnumerable<ValidationFailure> errors)
    {
        return new ValidationEnvelope<TEntity>(true, default!, errors);
    }
}
```

And our helper method for object instantiation.

```csharp
public static ValidationEnvelope<TEntity> TryValidate<TEntity>(Func<TEntity> initializationFunc)
{
    TEntity result;

    try
    {
        result = initializationFunc.Invoke();
    }
    catch (ValidationException exception)
    {
        return ValidationEnvelope<TEntity>.Failure(exception.Errors);
    }

    return ValidationEnvelope<TEntity>.Success(result);
}
```

In our application, we can use our new helpers in a factory when creating domain objects.

```csharp
public static class AirplaneFactory
{
    public static ValidationEnvelope<Airplane> Airplane(CreateAirplaneRequest request)
    {
        return FactoryHelpers.TryValidate(() => new Airplane(request.Id));
    }
}
```

In our handler or service:

```csharp
public class CreateAirplaneHandler
{
    public static void Handle(CreateAirplaneRequest request)
    {
        var result = AirplaneFactory.Airplane(request);

        if (result.IsInvalid)
        {
            // handle gracefully
        }

        // complete operation
    }
}
```

## Conclusion
