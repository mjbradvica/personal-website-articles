# Replacing Switch Statements with ChainStrategy

## Problem Statement

Sometimes we have use cases in our code that lead to natural logical branches where a process separates into sub-processes and may return a common response.

In the example below, a service implementing a use-case runs conditional logic depending on what the request is doing.

```csharp
public class MyService
{
    // Injected dependencies

    public MyResponse DoSomething(MyRequest request)
    {
        if (request.property == conditionA)
        {
            return ConditionA();
        }
        else if (request.property == conditionB)
        {
            return ConditionB();
        }

        return ConditionC();
    }

    private MyResponse ConditionA()
    {
        // A implementation
    }

    private MyResponse ConditionB()
    {
        // B implementation
    }

    private MyResponse ConditionC()
    {
        // C implementation
    }
}
```

This code, while workable, is sub-optimal. The problems with this code sample are:

- We have private methods when we should have public ones.
- Unit testing requires mocking dependencies which may not be used

A strategy here would allow us to break each condition

## Enter the Strategy

The strategy pattern is a simple way to employ an object-oriented switch statement. You can divide a problem into multiple handlers, each with its specific dependencies. This directly leads to easier automated testing because each test can focus on a single purpose.

Every strategy has a dedicated request and response object.

```csharp
public class MyResponse
{
    // response properties
}
```

```csharp
public class MyRequest : IStrategyRequest<MyResponse>
{
    // request properties
}
```

For each possible logical condition, we will define a custom handler that only uses the required dependencies to fulfill each logical branch.

```csharp
public class ConditionAStrategy : IStrategyHandler<MyRequest, MyResponse>
{
    
}
```

> The Strategy pattern is a low-usage pattern. It has minimal applicable situation where it can best be utilized.

## Conclusion
