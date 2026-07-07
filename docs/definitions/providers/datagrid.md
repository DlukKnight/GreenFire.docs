# **Datagrid provider**

## **Description**

Delivers the data collection that a [**DatagridControl**](../../components/controls/datagridcontrol.md) will show.

## **Definition**

=== "ASP.NET"

```csharp
public abstract class BaseDatagrid<TEntity, TIdentity>(IEntityService<TEntity, TIdentity> service) : IDatagridControl where TEntity : Entity<TIdentity>
```

Datagrid providers represent entities, and as such, they require defining both the entity type and its Id type under the **TEntity** and **TIdentity** generic parameters. The provider also has to inherit from the **BaseDatagrid** abstract class and be decorated with the **DatagridCode** attribute. This code is unique and there should be only one among all the providers, trying to register a second one with the same code will end in an API validation failure.

Finally, a service that implements [**IEntityService**](../infrastructure/services.md#ientityservice) has to be injected and passed to the base class.

Basic example:

=== "ASP.NET"

```csharp
[DatagridCode(nameof(TestDatagrid))]
public class TestDatagrid(ITestEntityService service) : BaseDatagrid<TestEntity, EntityGuidId>(service)
```

After this setup is satisfied, we are ready to start implementing the different methods that configure the definition.

!!! Note
    GreenFire automatically applies pagination after **GetBaseQuery()** is evaluated. Providers should return the complete query and should not manually paginate the results.

## **Methods**

### GetGridColumns

=== "ASP.NET"

```csharp
public abstract List<ColumnDefinition> GetGridColumns();
```

Returns a list of column definitions for the grid. Refer to [**Columns**](../coreconcepts/columndefinition.md) for more information.

### GetBaseQuery

=== "ASP.NET"

```csharp
public virtual IQueryable<TEntity> GetBaseQuery()
```

Defines the base query for the requested data, before pagination. Overriding this method is optional, by default it will do a **Get()** and query all the data in the repository, which will then be paginated before sending it to the frontend.

By overriding it, a specialized datagrid could be created, for example, a datagrid that shows all items that are made of metal:

=== "ASP.NET"

```csharp
public override IQueryable<TestEntity> GetBaseQuery()
{
    return service.Get().Where(p => p.Material == MaterialsEnum.Metal);
}
```

## **Minimal implementation example**

The next example show a minimal implementation with the simplest cases possible:

=== "ASP.NET"

```csharp
[DatagridCode(nameof(TestDatagrid))]
public class TestDatagrid(ITestEntityService service) : BaseDatagrid<TestEntity, EntityGuidId>(service)
{
    public override List<ColumnDefinition> GetGridColumns()
    {
        List<ColumnDefinition> columns =
        [
            new(nameof(TestEntity.Name), nameof(TestEntity.Name)),
            new(nameof(TestEntity.Description), nameof(TestEntity.Description))
        ];

        return columns;
    }
}
```

Notice how, in this case, all the entities values are represented in the datagrid, so there is no need for overriding **GetBaseQuery()**.

And later, when adding the control, this provider is used in its constructor by passing the provider's type:

=== "ASP.NET"

```csharp
row.AddControlToRow(e => e.TestEntities, new DatagridControl(typeof(TestDatagrid)) 
{
     Required = true, 
     MultiSelect = true, 
     Height = "100%" 
});
```
