# Dependency Injection Do's and Don'ts

## Overview

- Most of this talk is what **NOT** to do
- Going to talk about some other topics by proxy, but they're still related
- I am assuming that most you have some basic knowledge about DI
- Every example good or bad, I have seen in a real-life codebase
- These examples are for application code, **NOT** library code

**Figure 1** - I am unstoppable
![thanks](https://media.giphy.com/media/RuP38JSfj14je/giphy.gif)

## TL:DR (If you have to leave early)

- Please use dependency injection
- Because testing
- Use constructor injection 99.99% of the time

**Figure 2** - Sad
![shame](https://media.giphy.com/media/hURsrHYlao1Ko/giphy.gif)

## First Question

Can anyone here tell me the difference between Dependency Inversion, Inversion of Control, and Dependency Injection?

Or are they the same thing?

**Figure 3** - Maximum Thonks
![think](https://media.giphy.com/media/2H67VmB5UEBmU/giphy.gif)

**Dependency Inversion** - a principle realized at the application level; use interfaces, abstractions, and layers to separate major aspects of your application

**Inversion of Control** - a principle realized at the module level; separate the what and the how via abstractions

**Dependency Injection** - a technique implemented at the object level; implement relationships between objects via aggregation

## Why?

**Figure 2** - Hoobastonk
![reason](https://media.giphy.com/media/O7q8InqlZFyeI/giphy.gif)

Why do we even bother with DI?

1. It makes our testing life easier
2. Therefore, every decision we make with should be made with testing in mind

## The Rules

### DO NOT rely on IDisposable for everything

- Do not substitute a using statement for proper injection

```csharp
    public class Service : IService
    {
        public void HandleRequest()
        {
            using (var dependency = new Dependency())
            {
                dependency.DoSomething();
            }
        }
    }
```

### DO use IDisposable for unmanaged resources

- Use IDisposable and using statement for unmanaged objects
- Make sure to wrap in a try/catch

```csharp
public void HandleRequest()
{
    try
    {
        using (var fileStream = File.Create("myPath"))
        {
            fileStream.Write(new UTF8Encoding(true).GetBytes("Hello There!"));
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine(exception);
        throw;
    }
    
}
```

#### Question time

- What is wrong with the code above?

![whats wrong](https://media.giphy.com/media/8acGIeFnqLA7S/giphy.gif)

The file path/name is a dependency that we do not want to write to in a test!

##### Possible Solution

- Is this the solution?

```csharp
public class Service : IService
{
    private readonly string _filePath;

    public Service(string filePath)
    {
        _filePath = filePath;
    }

    public void HandleRequest()
    {
        try
        {
            using (var fileStream = File.Create(_filePath))
            {
                fileStream.Write(new UTF8Encoding(true).GetBytes("Hello There!"));
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine(exception);
            throw;
        }
    }
}
```

### DO NOT inject primitive types

- Keep injection limited to objects and interfaces

```csharp
public class Service : IService
{
    private readonly string _filePath;
    private readonly int _myValue;

    public Service(string filePath, int myValue)
    {
        _filePath = filePath;
        _myValue = myValue;
    }

    public void HandleRequest()
    {
        // handle here
    }
}
```

### DO use the IConfiguration interface as a proxy for primitives

- You already do this for connection strings

```csharp
public class Service : IService
{
    private readonly IConfiguration _configuration;

    public Service(IConfiguration configuration)
    {
        _configuration = configuration;
    }

    public void HandleRequest()
    {
        try
        {
            using (var fileStream = File.Create(_configuration["myFilePath"]))
            {
                fileStream.Write(new UTF8Encoding(true).GetBytes("Hello There!"));
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine(exception);
            throw;
        }
    }
}
```

### DO NOT inject the container

- The container is not supposed to be visible outside the composition root

```csharp
public class Service : IService
{
    private readonly IServiceProvider _serviceProvider;

    public Service(IServiceProvider serviceProvider)
    {
        _serviceProvider = serviceProvider;
    }

    public void HandleRequest()
    {
        var service = _serviceProvider.GetService(typeof(IDependency));
    }
}
```

### DO NOT resolve from the container

- Applies to anywhere in the application, even your composition root

```csharp
var service = _serviceProvider.GetService(typeof(IDependency));
```

### DO NOT over inject

![not a hard rule](https://media.giphy.com/media/uXUmaREltwja1dEqXi/giphy.gif)

- Stay with up to ~four dependencies per class.
- Look to refactor at five, six is too many.

```csharp
public class Service : IService
{
    private readonly IFirst _first;
    private readonly ISecond _second;
    private readonly IThird _third;
    private readonly IThird _fourth;
    private readonly IThird _fifth;
    private readonly IThird _sixth;

    public Service(IFirst first, ISecond second, IThird third, IThird fourth, IThird fifth, IThird sixth)
    {
        _first = first;
        _second = second;
        _third = third;
        _fourth = fourth;
        _fifth = fifth;
        _sixth = sixth;
    }

    public void HandleRequest()
    {
        // handle
    }
}
```

- Why should we limit the number of our dependencies?

![thonks](https://media.giphy.com/media/XWJhb2RI0qjnO/giphy.gif)

#### Alternatives to lots of dependencies

1. Chain of Responsibility
2. Events
3. Strategy
4. Re-think your responsibilities and boundaries

#### USE CAUTION around aggregate services

[This article](https://blog.ploeh.dk/2010/02/02/RefactoringtoAggregateServices/) recommends using an aggregate service. I would use caution with this approach.

```csharp
public class AggregateService: IAggregateService
{
    private readonly IThird _fourth;
    private readonly IThird _fifth;
    private readonly IThird _sixth;

    public Service(IThird fourth, IThird fifth, IThird sixth)
    {
        _fourth = fourth;
        _fifth = fifth;
        _sixth = sixth;
    }

    public void DoSomething()
    {
        // stuff
    }
}
```

- Our new service

```csharp
public class Service : IService
{
    private readonly IFirst _first;
    private readonly ISecond _second;
    private readonly IThird _third;
    private readonly IAggregateService _aggregateService;

    public Service(IFirst first, ISecond second, IThird third, IAggregateService aggregateService)
    {
        _first = first;
        _second = second;
        _third = third;
        _aggregateService = aggregateService;
    }

    public void HandleRequest()
    {
        var result = _aggregateService.DoSomething();
    }
}
```

- This **CAN** lead to kicking the can down the road in terms of where your dependencies are.

### USE CAUTION with how deep your dependency chains go

A -> B -> C -> D -> E, ect

- No hard number, but 3-4 is ideal
- Again, I would say look to refactor at 5, and don't go past 6

### Service Locators ARE NOT Dependency Injection

- The container is a service locator, the same rule above applies

```csharp
public class Service : IService
{
    private readonly IDependency _dependency;

    public Service()
    {
        _dependency = ServiceLocator.Find<IDependency>();
    }
}
```

### DO NOT inject anything into any domain object

- Domain objects should have zero knowledge of implementations

```csharp
public class MyDomainObject
{
    private readonly IDependency _dependency;

    public MyDomainObject(IDependency dependency)
    {
        _dependency = dependency;
    }
}
```

### DO NOT inject an interface when a class will do

What is the actual purpose of using interfaces?

![popquiz](https://media.giphy.com/media/3orifgBu5DCkYU0nM4/giphy.gif)

- Interfaces are used exclusively as a proxy

If a class contains only pure methods, then you DO NOT need an interface

```csharp
public class MyFactory
{
    public MyObject Create()
    {
        // always returns same thing
    }
}
```

### DO create interfaces for certain objects

- Most notable are the HttpClient and DateTime objects

```csharp
public interface ISystemClock
{
    DateTime Now();
}

public class SystemClock : ISystemClock
{
    public Now()
    {
        return DateTime.Now;
    }
}
```

```csharp
public class Service : IService
{
    private readonly ISystemClock _systemClock;

    public Service(ISystemClock systemClock)
    {
        _systemClock = systemClock;
    }
}
```

> I Recommend the [NodeTime](https://nodatime.org/) library for any datetime heavy lifting needs

### DO NOT use more than one DI framework per solution

- Had a client using multiple frameworks in the same solution.

### DO prefer the built in .NET core container

- Back in ye ole' times of .NET Framework, DI was not a first-class citizen and required a third-party library

1. There is a 99.99% change you DO NOT need any of the extra features in those frameworks
2. The built in container has zero fluff, just the basics
3. With .NET core, any third-party library is more of a plug-in than full blown alternative, you're still using the build in library to an extent

> If you use a third-party container, I recommend [AutoFac](https://autofac.org/) or [Lamar](https://jasperfx.github.io/lamar/) (updated version of StructureMap)

### DO NOT use method or property injection

- I have yet to see a proper use case for method or property injection over constructor.
- Not supported in base container.
- Very confusing to register.

> NServiceBus uses method injection, but again, library vs app code.

```csharp
public class Service : IService
{
    public void Handle(IDependency dependency)
    {
        // handle
    }
}
```

- Property injection breaks encapsulation!

```csharp
public class Service : IService
{
    public IDependency Dependency { get; set; }
}
```

### DO NOT use the FromServices attribute

- This is a leaky abstraction, your code should have ZERO knowledge of DI

```csharp
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet(Name = "GetWeatherForecast")]
    public IEnumerable<WeatherForecast> Get([FromServices] IService service) 
    {
        // handle request
    }
}
```

### DO register your dependencies correctly

1. Transient - Instance per Request
2. Scope - Instance per LifeTime Scoped (singleton per request)
3. Singleton - Single Instance

- When in doubt, use Transient/Instance per Request
- Remember, you can only register a type a single time (generics are the exception)

### DO NOT swallow dependencies

- Child dependencies inherit the lifetime of their owners

```csharp
public class Second : ISecond
{
    private readonly IFirst _first;

    public Second(IFirst first)
    {
        _first = first;
    }
}
```

```csharp
// First will now be locked as a Scoped dependency
services.AddTransient<IFirst, First>();

services.AddScoped<ISecond, Second>();
```

### DO register called constructors via lambdas

```csharp
// Service will be locked as Singleton because the resource is never disposed
services.AddTransient(new Service());
```

```csharp
// Will now be disposed of properly
services.AddTransient(_ => new Service());
```

### USE CAUTION if registering using reflection

![thanks](https://media.giphy.com/media/MCZ39lz83o5lC/giphy.gif)

```csharp
Assembly.GetExecutingAssembly()
    .GetTypes()
    .Where(x => x.Name.EndsWith("Factory"))
    .ToList().
    ForEach(type => builder.Services.AddTransient(type));
```

> If you are uncomfortable with reflection, this is where AutoFac/Lamar may be better suited for you.

### DO USE Modules if your application becomes beefy

- AutoFac has support for Modules out of the box.

```csharp
public static class MyBoundContextAModule
{
    public static void Register(IServiceCollection services)
    {
        // register bound context a in here
    }
}

public static class MyBoundContextBModule
{
    public static void Register(IServiceCollection services)
    {
        // register bound context b in here
    }
}
```

### DO register is the same assembly as your composition root or in your infrastructure layer

![composition root](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fi.ytimg.com%2Fvi%2F5OtUm1BLmG0%2Fmaxresdefault.jpg&f=1&nofb=1&ipt=ff9ad2335aceec0caa75c153f8af21fda1d5559504afd439c844bc110e7937b7&ipo=images)

### DO NOT worry about the composition root have a dependency on lower assemblies

- Your composition root **WILL** have a dependency on all lower assemblies one way or the other, there is **NO** way around this

## Question Time?

![questions](https://media.giphy.com/media/l0HlRnAWXxn0MhKLK/giphy.gif)
