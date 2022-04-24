# Covariance & Contravariance Quick Guide

Covariance and Contravariance are both concepts concerned with the ability or inability to assign base or derived types from generic interfaces. You can think of these keywords as enabling [Liskov Substitution Principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle) for generic interfaces.

A common example is the IEnumerable interface. You can assign a base type from what is initialized.

```csharp
    IEnumerable<object> vals = new List<MyClass>();
```

Looking at the interface, you'll see the following:

```csharp
  public interface IEnumerable<out T> : IEnumerable
  {
    IEnumerator<T> GetEnumerator();
  }
```

Covariance is denoted by the "out" keyword. Contravariance uses the "in" keyword.

A few rules to remember:

1) They only apply to generic interfaces
2) They only use uni-directional flow

The following interface is not possible:

```csharp
public interface IWontWork<(out or in) T>
{
    T MyMethod(T parameter);
}
```

Generic parameters defined by either covariance or contravariance must be either returned or used as parameters, but not both.

## Covariance

Covariance means applying the "out" keyword to a generic interface.

```csharp
public interface IMyInterface<out T>
{
    T MyMethod();
}
```

By adding this keyword we are defining a rule that states--Any type returned by this method may be assigned to a base type.

Assuming we had class A and class B that was derived from A we could do the following.

```csharp
A baseType = IMyInterface<B>;
```

Another way to conceptualize covariance is the Task object, which is NOT covariant.

The following will **not** work:

```csharp
public async Task<IEnumerable<int>> MyMethod()
{
    List<int> values = await GetValues();

    return values;
}
```

The following error will occur:

Error: CS0029 - Cannot implicitly convert type 'type' to 'type'

If we look at the Task object we'll see the following:

```csharp
public class Task<TResult> : Task
{
    // Implementation details
}
```

TResult is not defined with the "out" keyword.

To fix our method, we will need to cast our result to the exact IEnumerable type.

```csharp
public async Task<IEnumerable<int>> MyMethod()
{
    List<int> values = await GetValues();

    return values.AsEnumerable();
}
```

## Contravariance

Contravariance is just the opposite of covariance. We can take assign a base type to a derived variable.

```csharp
public interface IMyInterface<in T>
{
    void MyMethod(T parameter);
}
```

If we had the following classes:

```csharp
public class MyBase
{
}
```

and...

```csharp
public class MyDerived : MyBase
{
}
```

Then I could do the following with my interface:

```csharp
IMyInterface<MyDerived> obj = new MyInterfaceImplementation<MyBase>();
```

The situations for using contravariance and the "in" keyword are not as robust as covariance. This is because good software is built around abstractions, not details.

If you have ever used the IComparable or IComparer interfaces, both of these interfaces use contravariance because you are able to compare both base and derived types when sorting lists.

## Conclusion

Covariance and contravariance both involve inheritance for generic interfaces. Covariance allows you assign base types from returned derived types. Contravariance allows you to assign derived types to base type parameters. Covariance is more applicable than contravariance due to our desire to prefer base types to derived types. If you are ever asked about these topics or the "in" and "out" keywords in an interview, simply respond with, "They allow you to apply the Liskov Substitution Principle to generic interfaces".
