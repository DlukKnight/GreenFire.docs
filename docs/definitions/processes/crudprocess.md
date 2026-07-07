# **CrudProcess**

## **Description**

Defines the metadata and behavior required to render and operate a complete Crud interface.

## **Lifecycle**

A CrudProcess follows this general flow:

1. The process is discovered through the ProcessCode attribute.
2. The grid is built using BuildGridDataQuery().
3. The RootLayout, conditions and validation rules are loaded.
4. The user interacts with the process.
5. Before methods are executed.
6. The corresponding service operation is called.
7. After methods are executed.
8. The grid is refreshed.

## **First steps**

Crud Processes are easy to define, by inheriting from the **CrudProcess** abstract class and decorating them with the **ProcessCode** attribute. This code is unique and there should be only one among all the processes, trying to register a second process with the same code will end in an API validation failure.

=== "ASP.NET"

```csharp
public abstract class CrudProcess<TEntity, TIdentity, TDto>(ICrudEntityService<TEntity, TIdentity, TDto> service)
```

After those criteria are met, an entity that inherits from the [**Entity**](../infrastructure/entities.md) base class has to be declared as the generic **TEntity**, a type for your entity's Id field has to be declared as the generic **TIdentity**, a dto with the properties that will be sent as metadata (see: [**CrudDto**](../infrastructure/cruddto.md)) has to be declared as the generic **TDto** and a service that implements [**ICrudEntityService**](../infrastructure/services.md#icrudentityservice) has to be injected and passed to the base class.

Basic example:

=== "ASP.NET"

```csharp
[ProcessCode("myCrudProcessCode")]
public class TestCrudProcess(ITestEntityService service) : CrudProcess<TestEntity, int, TestEntityDto>(service)
```

After this setup is satisfied, we are ready to start implementing the different properties and methods that configure the definition.

## **Minimal implementation**

The smallest possible CrudProcess consists of:

- ProcessCode
- [**GetTitle()**](#gettitle)
- [**GetGridColumns()**](#getgridcolumns)
- [**GetRootLayout()**](#getrootlayout)

Everything else is optional.

## **Properties**

### UseDuplicateOption

=== "ASP.NET"

```csharp
protected virtual bool UseDuplicateOption => false;
```

Shows the *Duplicate* button in the toolbar. the default value is **false**.

### ExecutionMode

=== "ASP.NET"

```csharp
public virtual ProcessExecutionMode ExecutionMode => ProcessExecutionMode.Standard;
```

Defines the execution mode of the process:

- `Standard`: The default execution mode, this mode presents a main grid, with a toolbar that allows to execute each Crud operation.
- `SingleRegistry`: This mode will set the process in a single-record editing mode, where the process will always be in edit mode, and the toolbar will present the *Save* button only, which will persist the modifications in said record. The [**ResolveEntity**](#resolveentity) method has the responsibility of defining which record will be set as the single-edit.

### ImplementsAudit

=== "ASP.NET"

```csharp
public virtual bool ImplementsAudit => true;
```

Toggles the audit support on the process, which will only work if [**audit**](../integrations/audit.md) is implemented. By default, its value is **true**.

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
protected virtual IQueryable<object> BuildGridDataQuery()
{
    return _service.Get();
}
```

This method will return the base query used by the main grid to fetch its data. Keep in mind that this is the base query, meaning that any changes made for pagination will happen after this method is called.

Most of the time, this method will be left untouched, but there may be special cases where the query needs to solve some problem.

#### GetData

=== "ASP.NET"

```csharp
public virtual async Task<object> GetData(TIdentity? id)
{
    return BuildDto((TEntity)await ResolveEntity(id), await GetRootLayout())!;
}
```

Returns the [**CrudDto**](../infrastructure/cruddto.md) defined for the process after mapping it from a selected entity. Then this dto is sent to the frontend.

This method can be overridden to change the values that the dto is going to send, for example, we could want to obfuscate the passwords in a particular process:

=== "ASP.NET"

```csharp
public override async Task<object> GetData(int? id)
{
    TestCrudDto dto = (TestCrudDto)await base.GetData(id);

    if (!string.IsNullOrEmpty(dto.Password))
        dto.Password = "****";

    return dto;
}
```

#### ResolveEntity

=== "ASP.NET"

```csharp
public virtual async Task<object> ResolveEntity(TIdentity? id)
{
    if (id == null)
        return _service.Get().FirstOrDefault()!;

    return _service.GetByIds([id]).FirstOrDefault()!;
}
```

When an entity is required for *Edition*, this is the method that will be responsibly for selecting it. Overriding it will be crucial when using the `SingleRegistry` [**ExecutionMode**](#executionmode), since this mode is meant to operate with a concrete record. If no **id** is received, it will always deliver the first available record on the repository, which may be the intended behavior if the Crud process is made to edit single-record tables (also known as parameter tables).

#### GetDefaultData

=== "ASP.NET"

```csharp
public virtual async Task<object?> GetDefaultData()
{
    TDto? dto = Activator.CreateInstance<TDto>();

    return dto;
}
```

Returns an instance of the [**CrudDto**](../infrastructure/cruddto.md) that will be sent when the process enters edition in *New* mode. This method can be overridden to enforce default values for new records.

In the next example, the default values are set to send two properties with specific values. The rest of the untouched properties will be sent with their default values.

=== "ASP.NET"

```csharp
public override async Task<object?> GetDefaultData()
{
    TestCrudDto? dto = await base.GetDefaultData() as TestCrudDto;

    if (dto != null)
    {
        dto.BasePrice = 10;
        dto.BaseColor = "Green";
    }

    return dto;
}
```

#### GetDuplicatedData

=== "ASP.NET"

```csharp
public virtual async Task<TDto> GetDuplicatedData(TDto dto)
{
    return dto;
}
```

This method will be called when the *Duplicate* button is clicked. The process will enter *New* mode, but instead of receiving a default dto, it will receive a dto made from mapping the property values of the selected record. This is enough for the user to operate, but it may be necessary to change some values. For example, a duplicated article may want to make the required price property null to force the user to enter a new price.

In the next example, the **Name** property of the dto gets a prefix to signal that it has been duplicated:

```csharp
public virtual async Task<TDto> GetDuplicatedData(TDto dto)
{
    if (dto is null)
        return default!;

    dto.Name = $"Copy of {dto.Name}";
    return dto;
}
```

### Presentation

#### GetTitle

=== "ASP.NET"

```csharp
public abstract string GetTitle();
```

Returns the title that the Crud Process will show in the header.

#### GetGridColumns

=== "ASP.NET"

```csharp
protected abstract List<ColumnDefinition>? GetGridColumns();
```

Returns a list of column definitions for the main grid. Refer to [**Columns**](../coreconcepts/columndefinition.md) for more information.

#### GetRootLayout

=== "ASP.NET"

```csharp
protected abstract Task<RootLayout> GetRootLayout();
```

Returns the [**RootLayout**](../../components/containers/layout/rootlayoutcontainer.md) that the process will show in *New* and *Edit* modes. GreenFire offers several [**Builders**](../builders/index.md) to create each container, while the end-nodes are defined inside containers that support them. Refer to [**Layout builders**](../builders/layoutbuilders.md) for more information.

The next example builds a basic layout with two controls:

=== "ASP.NET"

```csharp
protected override async Task<RootLayout> GetRootLayout()
{
    RootLayoutBuilder<TestEntity> rootLayout = new();

    RowNodeBuilder<TestEntity> row = rootLayout.AddRow(2);

    row.AddControlToRow(e => e.Name, new TextControl() { Required = true, MaxLength = 50 });
    row.AddControlToRow(e => e.Number, new NumberControl());

    return rootLayout.Layout;
}
```

### Behavior

#### GetLayoutConditions

=== "ASP.NET"

```csharp
protected virtual async Task<List<ConditionRule>> GetLayoutConditions() => [];
```

Returns the nodes conditions for the layout. The [**PropertyConditionBuilder**](../builders/conditionsbuilders.md#propertyconditionbuilder) can be used to easily build any condition that affects a node linked to a property, while the [**NodeConditionBuilder**](../builders/conditionsbuilders.md#nodeconditionbuilder) will be used for nodes in general (for example, a [**LabelContent**](../../components/contents/labelcontent.md) node, which is independent of properties).

These conditions are sent as metadata to the frontend and evaluated client-side, allowing layouts to react immediately without additional server requests.

By default, no conditions are sent.

In the next example, two conditions are sent. The first one will disable the **NumericValue** control when the **UseNumber** checkbox is unchecked. The second one will make the label content with the key "*InformationLabel*" only be visible when **NumericValue** is 0, displaying some kind of information.

=== "ASP.NET"

```csharp
protected override async Task<List<ConditionRule>> GetLayoutConditions()
{
    List<ConditionRule> layoutConditions = [];

    PropertyConditionBuilder<TestEntity>.AddPropertyCondition(layoutConditions, ConditionType.Disable, p => p.NumericValue, p => p.UseNumber == false);

    NodeConditionBuilder<TestEntity>.AddNodeCondition(layoutConditions, ConditionType.Visible, "InformationLabel", p => p.NumericValue == 0);

    return layoutConditions;
}
```

#### GetValidationRules

=== "ASP.NET"

```csharp
protected virtual async Task<List<ValidationRule>> GetValidationRules() => [];
```

Returns the nodes validation rules for the layout. The [**PropertyValidationBuilder**](../builders/validationsbuilders.md) can be used to easily build any validation rule. Validations display their message to the [**ParametersMessage**](../coreconcepts/parametersmessage.md) class, which can render the error message while replacing placeholders for parameters, making it very flexible.

These rules are sent as metadata to the frontend and evaluated client-side, allowing layouts to react immediately without additional server requests.

By default, no validations are sent.

In the next example, a validation is defined, where the **PriceValue** control will receive a validation error if the PriceType value is "*Low cost*" and **PriceValue** is above a maximum defined in the process. In this case, a message is sent with parameters, so if any of the comparison values change later, the validation message will also reflect this.

=== "ASP.NET"

```csharp
protected override async Task<List<ValidationRule>> GetValidationRules()
{
    List<ValidationRule> validationRules = [];

    PropertyValidationBuilder<TestEntity>.AddPropertyValidation(validationRules, p => p.PriceValue, p => p.PriceType == PriceTypeEnum.LowCost && p.PriceValue > maxPrice , new ParametersMessage()
    {
        Key = "The price cannot be above {0} while the type is {1}",
        Params =
        [
            maxPrice,
            PriceTypeEnum.LowCost.ToString()
        ]
    });

    return validationRules;
}
```

### Toolbar

#### GetProcessMainButtons

=== "ASP.NET"

```csharp
public virtual List<ProcessMainButtonDefinition> GetProcessMainButtons()
{
    List<ProcessMainButtonDefinition> buttons = [
        new("primary", ProcessMainButtonActions.New),
        new("secondary", ProcessMainButtonActions.Edit),
        new("danger", ProcessMainButtonActions.Delete)
    ];

    if (UseDuplicateOption)
        buttons.Insert(2, new("primary", ProcessMainButtonActions.Duplicate));

    return buttons;
}
```

Defines the buttons displayed in the process toolbar. Overriding it could be used to prevent some buttons from showing. There is intended support for sending custom buttons in the future, but they will most probably be sent from another source.

### Execution hooks

#### Before methods

=== "ASP.NET"

```csharp
protected virtual async Task<Result<bool>> BeforeAdd(TDto dto)
{
    return Result<bool>.Success(true);
}

protected virtual async Task<Result<bool>> BeforeUpdate(TEntity original, TDto dto)
{
    return Result<bool>.Success(true);
}

protected virtual async Task<Result<bool>> BeforeSave(TDto dto)
{
    return Result<bool>.Success(true);
}

protected virtual async Task<Result<bool>> BeforeDelete(IEnumerable<TIdentity> ids)
{
    return Result<bool>.Success(true);
}
```

This methods will be called before each of the operations they cover executes:

- `BeforeAdd`: Called before the *Add* operation is executed. It receives the [**CrudDto**](../infrastructure/cruddto.md) with the values of the new record.
- `BeforeUpdate`: Called before the *Update* operation is executed. It receives the [**CrudDto**](../infrastructure/cruddto.md) with the modified values of the record, as well as the original entity, so comparisons between their properties can be made.
- `BeforeAdd`: Called before either the *Add* or the *Update* operations are executed. It receives the [**CrudDto**](../infrastructure/cruddto.md) with the values of either the new  or the modified record. This method fits situations where the actions before saving can happen in both scenarios.
- `BeforeDelete`: Called before the *Delete* operation is executed. It receives the list of ids that will be deleted.

Since any code can be executed here, any type of action can be applied, like returning failure after a validation against other entities in the repository (for example, a duplicated record), or a special action, like inserting new values in a related table if some conditions are met.

#### After methods

=== "ASP.NET"

```csharp
protected virtual async Task<Result<bool>> AfterAdd(TEntity createdEntity)
{
    return Result<bool>.Success(true);
}

protected virtual async Task<Result<bool>> AfterUpdate(TEntity original, TEntity updated)
{
    return Result<bool>.Success(true);
}

protected virtual async Task<Result<bool>> AfterSave(TEntity entity)
{
    return Result<bool>.Success(true);
}

protected virtual async Task<Result<bool>> AfterDelete(IEnumerable<TIdentity> ids)
{
    return Result<bool>.Success(true);
}

protected virtual async Task<Result<TEntity?>> AfterOperation(TEntity? result)
{
    return Result<TEntity?>.Success(result);
}
```

This methods will be called after each of the operations they cover executes:

- `AfterAdd`: Called after the *Add* operation is executed. It receives the newly created entity.
- `AfterUpdate`: Called after the *Update* operation is executed. It receives both the original and the actual entities.
- `AfterSave`: Called after either the *Add* or the *Update* operations are executed. It receives either the newly created or the modified entity. This method fits situations where the actions after saving can happen in both scenarios.
- `AfterDelete`: Called after the *Delete* operation is executed. It receives the list of ids that have been deleted.
- `AfterOperation`: Called after any operation. Useful when something should happen when any change happens in the domain. An example of this would be sending a notification when any operation occurs.

Since any code can be executed here, any type of action can be applied, like in the [**Before**](#before-methods) methods, but keep in mind that once this methods have been reached, the operation has been persisted in the database.
