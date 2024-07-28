# Replacing Switch Statements with ChainStrategy

## Problem Statement

> This article is a quick introduction and example of implementing a strategy via the [ChainStrategy](https://github.com/mjbradvica/ChainStrategy) library. Please see the official documentation for more information and samples.

Sometimes we have use cases in our code that lead to natural logical branches where a process separates into sub-processes and may return a common response.

In the example below, a service implementing a use-case runs conditional logic depending on what the request is doing.

```csharp
public class TrackingService
{
    private readonly IUpsDependency _upsDependency;
    private readonly IFedexDependency _fedExDependency;
    private readonly IUspsDependency _uspsDependency;

    public TrackingService(
        IUpsDependency upsDependency,
        IFedexDependency fedExDependency,
        IUspsDependency uspsDependency
    )
    {
        _upsDependency = upsDependency;
        _fedexDependency = fedexDependency;
        _uspsDependency = uspsDependency;
    }

    public TrackingResponse TrackPackage(TrackingRequest request)
    {
        if (request.trackingNumber == upsCode)
        {
            return TrackUps();
        }
        else if (request.trackingNumber == fedExCode)
        {
            return TrackFedEx();
        }

        return TrackUsps();
    }

    private TrackingResponse TrackUps()
    {
        // Ups implementation
    }

    private TrackingResponse TrackFedEx()
    {
        // FedEx implementation
    }

    private TrackingResponse TrackUsps()
    {
        // Usps implementation
    }
}
```

This code, while workable, is sub-optimal. The problems with this code sample are:

- We have private methods when we should have public ones.
- Unit testing requires mocking dependencies which may not be used

A strategy here would allow us to break each condition into an individual handler. This would transform our use case into smaller chunks that each fulfill a single responsibility. This allows us to easily unit test each handler without having to stub and mock dependencies that each condition does not rely on.

## Enter the Strategy

The strategy pattern is a simple way to employ an object-oriented switch statement. You can divide a problem into multiple handlers, each with its specific dependencies. This directly leads to easier automated testing because each test can focus on a single purpose.

A Strategy is best used when:

- You have conditional logic that has complex processes
- Each process shares common input and output

> The Strategy pattern is a low-usage pattern. It does not need to replace every switch statement in your code.

Every strategy has a dedicated request and response object.

```csharp
public class TrackingResponse
{
    // response properties
}
```

```csharp
public class TrackingRequest : IStrategyRequest<TrackingResponse>
{
    // request properties
}
```

For each possible logical condition, we will define a custom handler that only uses the required dependencies to fulfill each logical branch.

```csharp
public class FedexTrackingHandler : IStrategyHandler<TrackingRequest, TrackingResponse>
{
    private readonly IFedexDependency _fedExDependency;

    public FedexTrackingHandler(IFedexDependency fedExDependency)
    {
        _fedexDependency = fedexDependency;
    }

    public async Task<TrackingResponse> Handle(TrackingRequest request, CancellationToken cancellationToken)
    {
        // implementation
    }
}
```

Testing each handler is a lot easier than trying to write tests for a larger service because you are mocking and testing fewer dependencies and logical branches.

```csharp
[TestClass]
public class FedExTrackingHandlerTests
{
    private readonly Mock<IFedexDependency> _fedex;

    public FedexTrackingHandlerTests()
    {
        _fedex = new Mock<IFedexDependency>();
    }

    [TestMethod]
    public void Handler_Condition_IsCorrect()
    {
        // implement tests
    }
}
```

Finally, we need to create a profile which will be used to determine what handler to use.

```csharp
public class TrackingProfile
{
    public TrackingProfile()
    {
        AddStrategy<FedExTrackingHandler>(x => x.TrackingNumber == fedExCode);
        AddStrategy<UpsTrackingHandler>(x => x.TrackingNumber == upsCode);
        AddStrategy<UspsTrackingHandler>(x => x.TrackingNumber == uspsCode);
    }
}
```

Utilizing our new strategy from a controller is easy:

```csharp
[ApiController]
[Route("[controller]")]
public class PackageController : ControllerBase
{
    private readonly IStrategyFactory _factory;

    public PackageController(IStrategyFactory factory)
    {
        _factory = factory;
    }

    [HttpGet("{trackingNumber:int}")]
    public async Task<IActionResult> TrackPackage(int trackingNumber)
    {
        var response = await _factory.Execute(new TrackingRequest(trackingNumber));

        return Ok(response);
    }
}
```

## Conclusion

Use cases with complex conditional logic tend to lead to large services that tend to have lots of dependencies and private methods. This results in a frustrating testing experience when dealing with lots of mocking and brittle conditional testing. A Strategy may help to alleviate this situation by breaking up a problem into smaller chunks that can be implemented and tested in isolation from other code blocks. This leads to a stronger code base that is easier to maintain in the long run.