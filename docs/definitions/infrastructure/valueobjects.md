# **Value Objects**

## **Description**

GreenFire offers its own **Value Object** implementation to be used as contract in case the domain is meant to be defined by **Value Objects**. This is specially useful when trying to eliminate the *primitive obsession*, which we encourage when possible.

Our implementation is friendly to use and very straightforward and it was meant to be used in a **Domain Driven Design** pattern driven application.

## **ValueObject**

This is the base abstract class that should be inherited by all your **Value Object** properties.

=== "ASP.NET"

```csharp
public abstract class ValueObject : IComparable<ValueObject>
{
    protected abstract IEnumerable<object> GetEqualityComponents();

    public virtual object? GetRawValue();

    public override bool Equals(object? obj);

    public override int GetHashCode();

    public int CompareTo(ValueObject? other);

    private static int CompareObjects(object? a, object? b);

    public static bool operator ==(ValueObject? a, ValueObject? b);

    public static bool operator !=(ValueObject? a, ValueObject? b) => !(a == b);
}
```

The implementation is quite simple, the next example shows a regular object for a code-like property under the **Domain Driven Design** pattern:

=== "ASP.NET"

```csharp
public class ColorCode : ValueObject
{
    private readonly string _value;
    public string Value => _value;

    private ColorCode(string code) => _value = code;

    public static Result<ColorCode> Create(string code)
    {
        if (string.IsNullOrEmpty(code))
            return Result<ColorCode>.Failure(Error.Validation("Code", ["Code is required"]));

        return Result<ColorCode>.Success(new ColorCode(code));
    }

    protected override IEnumerable<object> GetEqualityComponents()
    {
        yield return _value;
    }

    public static implicit operator string(ColorCode code) => code.Value;
}
```

Then, in your entity you just use this object as the type for your property:

=== "ASP.NET"

```csharp
public ColorCode Code { get; private set; }
```

Of course, if you are using an ORM, this type of properties will need to be declared, most likely with a conversion method, so the ORM can understand how to persist it and how to read it.

Even if you are not following **Domain Driven Design**, **Value Objects** can still be useful for encapsulating validation rules and preventing invalid states.

If you are not familiar with **Domain Driven Design**, we strongly encourage it, since it is one of the best patterns to ensure the integrity of the data. It does have the downside of being verbose, but the pay off is really good at long term.

## **IdentityValueObject**

This abstract class is meant to be used with **Id** fields. While you can do everything with the regular **Value Object** implementations, this class will make it easier to read the value stored in the object, and with the Ids being so needed to several database operations, this will simplify repository and service implementations in the future.

=== "ASP.NET"

```csharp
public abstract class IdentityValueObject<T>(T value) : ValueObject
{
    public virtual T Value { get; } = value;

    public override string ToString()
    {
        return Value?.ToString() ?? string.Empty;
    }
}
```

This class is also easy to implement, the next example shows a basic identity object for an Id of Guid type:

=== "ASP.NET"

```csharp
public class EntityGuidId : IdentityValueObject<Guid>, IEquatable<EntityGuidId>
{
    private readonly Guid _value;

    private EntityGuidId(Guid id) : base(id)
    {
        _value = id;
    }

    public static Result<EntityGuidId> Create(Guid id)
    {
        if (id == Guid.Empty)
            return Result<EntityGuidId>.Failure(Error.Validation("Id", ["Invalid Guid"]));

        return Result<EntityGuidId>.Success(new EntityGuidId(id));
    }

    protected override IEnumerable<object> GetEqualityComponents()
    {
        yield return _value;
    }

    public bool Equals(EntityGuidId? other)
    {
        if (other is null) return false;
        return Value == other.Value;
    }

    public override bool Equals(object? obj)
    {
        return obj switch
        {
            EntityGuidId other => Equals(other),
            Guid guidValue => Value == guidValue,
            _ => false
        };
    }

    public override int GetHashCode() => Value.GetHashCode();

    public static implicit operator Guid(EntityGuidId id) => id.Value;

    public static implicit operator Guid?(EntityGuidId? id) => id?.Value;
}
```
