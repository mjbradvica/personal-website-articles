# Routing with Variables in Blazor

Routing to components in Blazor is easy when leveraging variables. This can be a cleaner alternative to using raw strings throughout.

The example below uses the default Counter component included whenever a new Blazor project is created. The only requirement is a class that contains constant strings to use for our routes:

```csharp
    public static class PageRoutes
    {
        public const string Counter = "counter";

        public const string CounterRoute = "/counter";
    }
```

Note: Constant interpolated strings are in a preview stage to cut down on redundancies.

You can use routes with parameters as well:

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

Using variables for routing in Blazor is a small change that increases reusability and removes naked strings.
