# Routing with Variables in Blazor

Routing to components in Blazor can be done using variables very easily. This can be a cleaner alternative than using raw strings everywhere.

This example uses the default Counter component that is included when a new Blazor project is created. All we need to do is add a class that contains constant strings to use for our routes:

```csharp
    public static class PageRoutes
    {
        public const string Counter = "counter";

        public const string CounterRoute = "/counter";
    }
```

Note: At the time I am writing this, constant interpolated strings are in a preview stage. That will allow you to cut down on the repetition in this file.

And yes, you can use routes with parameters as well:

```csharp
    public static class PageRoutes
    {
        public const string MyParams = "/myparams/{id:int}";
    }
```

You can now update your NavLinks to the following:

```html
<NavLink href="@PageRoutes.Counter">
```

Your components will use the "@attribute" directive instead of "@page" now.

```csharp
@page "/counter"
```

becomes...

```csharp
@attribute [Route(PageRoutes.CounterRoute)]
```

Using variables for routing in Blazor is a small change that increases reusability in your code base and helps to remove naked strings.
