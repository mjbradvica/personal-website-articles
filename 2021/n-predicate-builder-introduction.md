# NPredicateBuilder, an Alternative to Repetitive Queries

[NPredicateBuilder](https://www.nuget.org/packages/NPredicateBuilder.EF/) [(repo here)](https://github.com/mjbradvica/n-predicate-builder) is a means to condense redundant "Where" and "Order" LINQ clauses in your application. Even better, you can test each predicate independently. By this, you can be certain that any large query you create--which is unable to be tested by itself--works properly.

NPredicateBuilder allows you to turn...

```csharp
var results = people
    .Where(x => x.Name != "Joe")
    .Where(x => x.Age >= 10)
    .Where(x => x.IsEligible)
    .OrderBy(x => x.Name)
    .ThenBy(x => x.Age)
    .ToList();
```

into...

```csharp
var predicateBuilder = people
    .NPredicateBuilderWhere(query)
    .NPredicateBuilderOrder(order)
    .ToList();
```

"Query" and "order" are objects that you build of individual predicates.

```csharp
var query = new PersonQueries()
    .IsNotNamedJoe()
    .IsOlderThan(10)
    .IsEligible();


var order = new PersonOrders()
    .ByName()
    .ThenByAge();
```

The main difference is that you can re-use your predicates with any desired query. So, instead of redundant clauses that may appear in dozens of queries such as--

```csharp
    .Where(x => x.Age >= 10);
```

--you now have re-usable, testable methods.

```csharp
public PersonQueries IsOlderThan(int age)
{
    AddAndCriteria(x => x.Age >= age);

    return this;
}
```

## Advantages of NPredicateBuilder

### Queries and Orders are Declarative

Your queries are far more descriptive and easier to read with NPredicateBuilder. Being forced to name a method "NameIsNotJoeAndIsOlderThanTenAndIsEligigleOrderedByNameThenAge" is pure insanity. NPredicateBuilder is declarative by nature. Your queries and orders describe themselves in a clear and concise fashion.

### Everything is Now Testable

It is near impossible to test EF queries because the DbContext object is almost impossible to mock. The only way around this is to extract every predicate in your application so you can test it individually. NPredicateBuilder performs this behavior by default. Because every single Where and Order clause is separate, testing is easy.

The following demonstrates how to test an Order:

```csharp
[TestMethod]
public void ByName_OrdersCorrectly()
{
    var bob = new Person { Name = "Bob" };
    var billy = new Person { Name = "Billy" };

    var people = new List<Person>
    {
        bob, billy
    };

    _order = new PersonOrders().ByName();

    var result = people
        .AsQueryable()
        .NPredicateBuilderOrder(_order).ToList();

    Assert.AreEqual(billy, result.First());
}
```

### Removes Redundant Data Access Code

You may have seen interfaces littered with methods like this...

```csharp
public interface IPersonRepository
{
    List<People> GetByName(string name);
    List<People> GetByNameAndAge(string name, int age);
    List<People> GetByStatusOrAddress(Status status, int zipcode);
    
    // There are probably many more methods in this interface all like the ones above.
}
```

With NPredicateBuilder, the above can become simply...

```csharp
public interface IPersonRepository
{
    List<People> Search(BaseQuery<Person> query, BaseOrder<Person> order);
}
```

### Actually Easy to Learn

NPredicateBuilder is easy to learn because it's tiny. It includes two base classes and a couple of extension methods. That's it.

### It Plays Nicely with Everything

Because NPredicateBuilder has two extension methods, you can plug it into any LINQ expression.

```csharp
var predicateBuilder = people
            .NPredicateBuilderWhere(query)
            .NPredicateBuilderOrder(order)
            .GroupBy(x => x.Name)
            .Skip(10)
            .Take(100)
            .ToList();
```

## In Short

If your application mostly deals with CRUD and you have only a few joins, NPredicateBuilder may not be worth it. Where NPredicateBuilder really shines is with applications that have many queries and orders that are duplicated or partially duplicated multiple times. It allows any developer to reduce their code base into reusable, testable code nuggets that make life easier.
