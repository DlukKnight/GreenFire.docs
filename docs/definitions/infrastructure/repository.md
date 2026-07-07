# **Repository (optional)**

## **Description**

GreenFire exposes an **IEntityRepository** interface, meant to standardize all implementations and complement the service interfaces. Its application is completely optional.

## **IEntityRepository**

=== "ASP.NET"

```csharp
public interface IEntityRepository<TEntity, TIdentity> where TEntity : Entity<TIdentity>
{
    IQueryable<TEntity> GetAll(bool withTracking = true);

    Task<int> GetTotalCount();

    IQueryable<TEntity> GetByIds(List<TIdentity> ids, bool withTracking = true);

    void Add(TEntity entity);

    void Update(TEntity entity, object? rowVersion);

    void Delete(IReadOnlyList<TEntity> entities);

    Task<Result> SaveChangesAsync();
}
```
