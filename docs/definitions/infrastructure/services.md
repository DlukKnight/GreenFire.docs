# **Services**

## **Description**

GreenFire has two interfaces that are necessary for services to integrate with it. The **IEntityService** interface is used for services that represent entities that won't be used in [**Crud processes**](../processes/crudprocess.md), where the **ICrudEntityService** is meant for those that will be used in Crud processes. The **ICrudEntityService** also implements **IEntityService**, so it basically extends its function to include the three persistance operations of Crud.

## **IEntityService**

This service exposes properties necessary to correctly retrieve and represent entities in read-only models.

Intended usage:

- [**Read-only Processes**](../processes/readonlyprocess.md)
- [**Datagrids**](../providers/datagrid.md)
- [**Checkbox Lists**](../providers/checkboxlist.md)
- Any other context where data won't be modified.

=== "ASP.NET"

```csharp
public interface IEntityService<TEntity, TIdentity>
{
    IQueryable<TEntity> Get(bool withTracking = true);

    IQueryable<TEntity> GetByIds(List<TIdentity> ids, bool withTracking = true);

    Task<int> GetTotalCount();
}
```

### **Get**

This method should return an *IQueryable* of all the data persisted for the entity. The **withTracking** parameter is useful for indicating if this *IQueryable* should track the materialized results or should treat them independently.

### **GetByIds**

Similar to **Get**, but with the extra *Where* filter using a list of Ids. For example, this method is used internally when *Editing* an entity from a Crud Process, by sending one Id in the **ids** parameter list, the one selected in the main grid.

### **GetTotalCount**

Returns the amount of records that the **Get** method should return. Used for the main grid, to know the full amount of entities and communicate it when paginated results are being displayed.

## **ICrudEntityService**

This service exposes properties necessary to correctly execute Crud operations.

Intended usage:

- [**Crud processes**](../processes/crudprocess.md)
- [**Details**](../../components/controls/detailcontrol.md)
- Any other context where data will be modified.

Keep in mind that you could technically use this interface for every entity in the system and it will work, since this one implements **IEntityService**, but we encourage to define the services as they are being used to help clarity and protect entities from unwanted mutations.

A Dto is defined for this service, the generic [**TCrudDto**](cruddto.md). The philosophy behind this is to separate entity representation against UI representation.

=== "ASP.NET"

```csharp
    public interface ICrudEntityService<TEntity, TIdentity> : IEntityService<TEntity, TIdentity> where TEntity : Entity<TIdentity>
    {
        Task<Result<IEnumerable<TIdentity>>> Delete(IEnumerable<TIdentity> ids);
    }

    public interface ICrudEntityService<TEntity, TIdentity, TCrudDto> : ICrudEntityService<TEntity, TIdentity> where TEntity : Entity<TIdentity>
    {
        Task<Result<TEntity>> CreateFromCrudProcess(TCrudDto dto);

        Task<Result<TEntity>> UpdateFromCrudProcess(TCrudDto dto);
    }
```

### **CreateFromCrudProcess**

This method should call your regular *Create* or *Add* method from your service and will be invoked when a Crud process executes the *Add* operation. If you are using your own Dto, or you send the entity directly, you will have to transform the received **dto** property to the data format you need.

The entity **Id** property will be always null inside the **dto**, since this is a new record.

### **UpdateFromCrudProcess**

Same principle than **CreateFromCrudProcess**, but this one will be invoked when executing the *Modify* operation.

The entity **Id** property will have the proper value inside the **dto**, corresponding to the entity we are trying to modify.

### **Delete**

This method will simply give you a list of Ids to delete records. It is invoked when the *Delete* operation is executed.
