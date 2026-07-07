# **ReadOnlyProcess**

## **Description**

Defines the metadata and behavior required to render and operate a complete data presentation form interface, with filters.

## **Lifecycle**

A ReadOnlyProcess follows this general flow:

1. The process is discovered through the ReadOnlyProcessCode attribute.
2. The grid is built using BuildGridDataQuery().
3. The RootLayout, FiltersLayout, conditions and validation rules are loaded.
4. The user may interact with the filters.
5. Filters are applied.
6. The grid is refresh showing the filtered data.
7. The user selects a record to view.
8. The read-only controls are displayed.

## **First steps**

Read-only Processes are easy to define, by inheriting from the **ReadOnlyProcess** abstract class and decorating them with the **ReadOnlyProcessCode** attribute. This code is unique and there should be only one among all the processes, trying to register a second process with the same code will end in an API validation failure.

=== "ASP.NET"

```csharp
public abstract class ReadOnlyProcess<TEntity, TIdentity, TFiltersParameters>(IEntityService<TEntity, TIdentity> service) : IReadOnlyProcess
```

After those criteria are met, an entity has to be defined as the generic **TEntity**, a type for your entity's Id field has to be declared as the generic **TIdentity**, a class with the properties that are going to be used as filters as the **TFiltersParameters** parameter, and a service that implements [**IEntityService**](../infrastructure/services.md#ientityservice) has to be injected and passed to the base class. Since [**ICrudEntityService**](../infrastructure/services.md#icrudentityservice) also implements [**IEntityService**](../infrastructure/services.md#ientityservice), if you have a service defined with that interface, it could be used here.

Keep in mind that, while this process has a similar setup that [**Crud Processes**](./crudprocess.md) have, your entity does not have to inherit from the [**Entity**](../infrastructure/entities.md) base class for it to work. Nonetheless, we recommend declaring this inheritance, since in most cases the entities that are going to be displayed probably have Crud operations of some sort, and using the base entity offered by GreenFire will standardize everything under the same concept.

Basic example:

=== "ASP.NET"

```csharp
[ReadOnlyProcessCode("testReadOnlyProcess")]
public class TestViewer(ITestViewerEntityService service) : ReadOnlyProcess<TestViewerEntity, Guid, TestViewerFilters>(service)
```

After this setup is satisfied, we are ready to start implementing the different methods that configure the definition.

## **Minimal implementation**

The smallest possible ReadOnlyProcess consists of:

- ReadOnlyProcessCode
- [**GetTitle()**](#gettitle)
- [**GetGridColumns()**](#getgridcolumns)
- [**GetRootLayout()**](#getrootlayout)

Everything else is optional.

## **Methods**

### Access

#### HasAccess

=== "ASP.NET"

```csharp
public virtual bool HasAccess() => true;
```

Return a boolean value that indicates if access criteria is met when trying to do any operation with the process. By default, it always return **true**.

An example to this method could be checking if the logged user has the right permissions to access the process.

### Loading

#### BuildGridDataQuery

=== "ASP.NET"

```csharp
protected virtual IQueryable<TEntity> BuildGridDataQuery(TFiltersParameters? filters)
{
    return _service.Get();
}
```

This method is very similar to [**BuildGridDataQuery**](./crudprocess.md#buildgriddataquery) used in [**Crud Processes**](./crudprocess.md), with the difference that it receives an instance of the **TFiltersParameters** defined class to apply the filters to the grid.

Returning the base query is enough in many cases. Override this method only when filters or additional joins need to be applied before the data reaches the grid.

#### GetData

=== "ASP.NET"

```csharp
public virtual async Task<object?> GetData(TIdentity? id)
{
    if (id == null)
        return null;

    return _service.GetByIds([id]).FirstOrDefault()!;
}
```

Returns the record the process needs to display in the controls.

While this method can be overridden to change the values that are sent or apply other operations, most of the times it should be left untouched, since in a Read-only context there shouldn't be any mutations.

#### GetDefaultFiltersData

=== "ASP.NET"

```csharp
protected virtual TFiltersParameters GetDefaultFiltersData() => Activator.CreateInstance<TFiltersParameters>();
```

This method is equivalent to the [**GetDefaultData**](./wizard.md#getdefaultdata) method used by [**Wizard**](./wizard.md), but using your **TFiltersParameters**. Check that method to see its characteristics plus an example.

### Presentation

#### GetTitle

=== "ASP.NET"

```csharp
public abstract string GetTitle();
```

Returns the title that the Read-only Process will show in the header.

#### GetGridColumns

=== "ASP.NET"

```csharp
protected abstract List<ColumnDefinition> GetGridColumns();
```

Returns a list of column definitions for the main grid. Refer to [**Columns**](../coreconcepts/columndefinition.md) for more information.

#### GetRootLayout

=== "ASP.NET"

```csharp
protected abstract RootLayout? GetRootLayout();
```

This method is equivalent to the [**GetRootLayout**](./crudprocess.md#getrootlayout) method used by [**Crud Processes**](./crudprocess.md). Check that method to see its characteristics plus an example.

#### GetFiltersLayout

=== "ASP.NET"

```csharp
protected virtual RootLayout? GetFiltersLayout() => null;
```

Same definition that [**GetRootLayout**](#getrootlayout), but this one is used to define the layout for the filters, using the **TFiltersParameters** class.

### Behavior

#### GetLayoutConditions

=== "ASP.NET"

```csharp
protected virtual List<ConditionRule> GetLayoutConditions() => [];
```

This method is equivalent to the [**GetLayoutConditions**](./crudprocess.md#getlayoutconditions) method used by [**Crud Processes**](./crudprocess.md). Check that method to see its characteristics plus an example.

#### GetFiltersLayoutConditions

=== "ASP.NET"

```csharp
protected virtual List<ConditionRule> GetFiltersLayoutConditions() => [];
```

Same definition that [**GetLayoutConditions**](#getlayoutconditions), but this one is used to define the conditions for the filters, using the **TFiltersParameters** class.

#### GetFiltersLayoutValidations

=== "ASP.NET"

```csharp
protected virtual List<ValidationRule> GetFiltersLayoutValidations() => [];
```

This method is equivalent to the [**GetValidationRules**](./crudprocess.md#getvalidationrules) method used by [**Crud Processes**](./crudprocess.md), but using your **TFiltersParameters** class instead of **TEntity**. Check that method to see its characteristics plus an example.
