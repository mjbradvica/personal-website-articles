# Full Encapsulation In EF

With the release of .NET 8 and EF 8, we now have the opportunity to incorporate full encapsulation with Entity Framework. Encapsulation is the easiest thing in software to let go--and fix.

There are three main areas for encapsulation in EF:

1) An entity's primitives
2) An entity's navigation and complex properties
3) The context class

## Encapsulating Primitives

Entity primitives do not require a public or private setter and can be solely passed via the constructor.

```csharp
public class AircraftType
{
    public AircraftType(Guid id, AircraftModel model)
    {
        Id = id;
        Model = model;
    }

    public AircraftType(AircraftModel model)
        : this(Guid.NewGuid(), model)
    {
    }

    public Guid Id { get; }

    public AircraftModel Model { get; }
}
```

Configuration of properties is done in your context class.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<AircraftType>(x =>
    {
        x.HasKey(y => y.Id);
        x.Property(y => y.Model);
    });
}
```

Entity Framework will now pass these properties into the object using the constructor whenever it needs to be instantiated.

## Encapsulating Navigation and Complex Properties

Navigation and Complex properties are not passed via a constructor, but rather EF will use reflection to allocate these when desired. However, unlike previous versions, you do not require a private setter or empty constructor anymore.

```csharp
public class Airplane
{
    public Airplane(Guid id, string registration)
    {
        Id = id;
        Registration = registration;
        Configuration = default!;
        AircraftType = default!;
    }

    public Airplane(string registration, Configuration configuration, AircraftType aircraftType)
        :this(Guid.NewGuid(), registration)
    {
        Configuration = configuration;
        AircraftType = aircraftType;
    }

    public Guid Id { get; }

    public string Registration { get; }

    public Configuration Configuration { get; }

    public AircraftType AircraftType { get; }
}
```

The first constructor is used by EF; we use the second constructor when creating the object for the first time.

> The "default!" is used to tell the compiler not to worry about the nullable aspect of the property.

You may also allocate each object with an "empty" version if you do not wish to use the "default!" trick.

Configuration for navigation and complex properties is done in the same fashion as primitives.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<AircraftType>(x =>
    {
        x.HasKey(y => y.Id);
        x.Property(y => y.Model);
    });

    modelBuilder.Entity<Airplane>(x =>
    {
        x.HasKey(y => y.Id);
        x.Property(y => y.Registration);
        x.HasOne(y => y.AircraftType);
        x.ComplexProperty(y => y.Configuration, y =>
        {
            y.Property(z => z.HasFlexBathrooms);
            y.Property(z => z.SeatCount);
        });
    });
}
```

## Encapsulating the Context class

DbSet properties do not require any setters. You can use the "Set" of type T method for initialization to satisfy nullable requirements.

```csharp
public sealed class ApplicationContext : DbContext
{
    public ApplicationContext(DbContextOptions options)
        : base(options)
    {
        Database.EnsureCreated();

        AircraftTypes = Set<AircraftType>();
        Airplanes = Set<Airplane>();
    }

    public DbSet<AircraftType> AircraftTypes { get; }

    public DbSet<Airplane> Airplanes { get; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // previous implementation
    }
}
```

> Make sure to seal your context class to avoid the virtual call of "Database.EnsureCreated()" in a possible derived class.

Enforcing full encapsulation in previous versions of Entity Framework was a tradeoff between good engineering and development velocity. With the latest version, you do not need to make that compromise.
