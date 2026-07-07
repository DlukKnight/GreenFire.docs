# **Entities**

## **Description**

GreenFire uses the **Entity** class identifier to perform Crud operations, relate entities together, and automatically resolve navigation properties used by controls such as Lookups and Details

For the processes to correctly operate with database models representations, inheriting from the **Entity** class is necessary.

## **Requirements**

An entity used by GreenFire must:

- expose an identifier
- represent a single database record
- expose navigation properties when relations are required

Nothing else is required.

## **Base class**

=== "ASP.NET"

```csharp
namespace GreenFire.Domain.Shared.Domain;

using System;

public abstract class Entity<TIdentity> : IEquatable<Entity<TIdentity>>
{
    public virtual TIdentity Id { get; protected set; } = default!;

    ...
}
```

Beyond exposing an identifier, **Entity** imposes no behavior or persistence requirements. You remain free to decorate the class with ORM-specific attributes, implement additional interfaces, or inherit from your own intermediate base classes. The **Id** field is even marked as virtual, in case you want to do changes, like applying a different column name.

Being persistence-agnostic, the framework has been designed so that the persistence layer can be implemented using, but not limited too:

- Entity Framework Core
- Dapper
- MongoDB
- ADO.NET
- Any custom repository implementation

As you can see, the **Id** doesn't have a forced data type, which means that any type of primitive could be used as your identifier. GreenFire also supports [**Value Objects**](valueobjects.md) identifiers exactly the same as primitive identifiers, provided they implement the required contracts

## **Relations**

Support for relations is also included, since processes will be able to render and operate with relations like, for example, details or lookups, and it does this via navigation properties. For one to one relations, an Id pointing to the related entity and a navigation property will be enough:

=== "ASP.NET"

```csharp
public int? RelationId { get; set; }
public RelationClass? relation { get; set; }
```

For one to many relations, a collection of the related entities will also suffice:

=== "ASP.NET"

```csharp
public ICollection<DetailClass> Details { get; set; }
```

GreenFire does not control how related entities are loaded. Whether they come from eager loading, explicit loading, lazy loading, projection, or manual mapping is entirely the application's responsibility. As long as the expected properties are populated when the framework receives the entity, GreenFire will operate correctly.
