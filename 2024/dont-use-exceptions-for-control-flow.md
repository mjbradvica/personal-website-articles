# Don't Use Exceptions for Control Flow

Note: This article concerns application level code. Rules for library-level code differ.

## Exceptions are the exception



> Throw only when you did not expect something.

## Naked Throws are goto Statements

When you throw an exception, you are

## Catch and handle gracefully

One of the easiest ways to reduce the need to throw exceptions is the use of an Envelope object to wrap the status of an operation.

```csharp
public Envelope<MyResponse> Handle(MyRequest request)
{
    var entity = _entityRepository.FindById(request.Id);

    if (entity == null)
    {
        return Envelope.NotFound();
    }

    return Envelope.Success(entity);
}
```

## Continue to use a Global Handler



## Conclusion