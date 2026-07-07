# **Audit**

## **Description**

GreenFire offers audit support by the use of a simple contract.

## **Lifecycle**

1. A **Process** executes an operation.
2. GreenFire calls **IAuditProvider.AuditAsync()**.
3. Your implementation handles the **AuditEntry**.

!!! Note
    GreenFire does not implement its own audit engine. Instead, it provides contracts that allow integration with any technology or custom implementation.

## **Supported process types**

Currently, the following processes support auditing:

- [**Crud Processes**](../processes/crudprocess.md)
- [**Report Process**](../processes/reportprocess.md)

Processes expose this capability by enabling their [**ImplementsAudit**](../processes/crudprocess.md#implementsaudit) property, which by default is **true**.

## **Getting started**

To enable audit, simple register a provider service that implements **IAuditProvider**:

=== "ASP.NET"

```csharp
services.AddScoped<IAuditProvider, SqlAuditProvider>();
```

Once this is done, every process that supports audit will start calling the **AuditAsync** method inside your provider.

## **IAuditProvider**

=== "ASP.NET"

```csharp
public interface IAuditProvider
{
    Task AuditAsync(AuditEntry entry);
}
```

This interface works as the contract for the audit execution, along with the **AuditEntry**, which will carry all the information necessary for the audit to be correctly handled:

=== "ASP.NET"

```csharp
public class AuditEntry
{
    public ProcessTypeEnum ProcessType { get; init; }

    public required string ProcessCode { get; init; }

    public AuditActionEnum Action { get; init; }

    public string? EntityType { get; set; }

    public string? EntityId { get; set; }

    public object? BeforeData { get; init; }

    public object? AfterData { get; init; }
}
```

The properties in **AuditEntry** are as follows:

|**Property**             |**Description**                                                                                                   |
|-------------------------|------------------------------------------------------------------------------------------------------------------|
|ProcessType              |Stores the type of the process that should be audited|
|ProcessCode              |Stores the code of the process that should be audited|
|Action                   |Stores what was the action that was executed by the process, for example, *Create* or *Delete*|
|EntityType               |The C# class type of the entity, as a string|
|EntityId                 |The id of the entity, as string|
|BeforeData               |The state of the entity before the operation happened. Could be empty if not prior state existed (for example, at *Create*)|
|AfterData                |The state of the entity after the operation happened. Could be empty if not subsequent state exists (for example, at *Delete*)|

This properties will be automatically filled and sent to the **AuditAsync** method, where they can be used to do whatever implementation is needed.

## **AuditOptions Attribute**

=== "ASP.NET"

```csharp
public class AuditOptionsAttribute(AuditBehavior auditBehavior) : Attribute
```

GreenFire allows properties to customize how they appear in the audit output by decorating them with **AuditOptionsAttribute**.

=== "ASP.NET"

```csharp
public enum AuditBehavior
{
    Normal,
    Ignore,
    Mask
}
```

| **Behavior** | **Description**                                                                              |
| ------------ | -------------------------------------------------------------------------------------------- |
| Normal       | Include the value normally.                                                                  |
| Ignore       | Exclude the property completely.                                                             |
| Mask         | Include the property but replace its value with `****`. Useful for passwords or secrets.     |

## **JsonChangeNode**

=== "ASP.NET"

```csharp
public class JsonChangeNode
{
    public string PropertyName { get; set; } = "";

    public object? OldValue { get; set; }

    public object? NewValue { get; set; }

    public List<JsonChangeNode>? Children { get; set; }
}
```

While GreenFire only requires implementing **IAuditProvider**, applications frequently need to store property-by-property differences between two objects.

**JsonChangeNode** is provided as a helper model for this scenario.

The implementation is simple, by storing the **PropertyName** and then both values, as well as a **Children** property in cases where relations are also taking into consideration.

## **ObjectProjectionBuilder**

Before an object can be serialized for audit purposes, GreenFire needs to convert it into a consistent representation while also respecting **AuditOptionsAttribute**.

**ObjectProjectionBuilder** performs this transformation.

### ToAuditObject

=== "ASP.NET"

```csharp
public static object ToAuditObject(object entity)
```

This method will take any entity class object and transform it into a standardized dictionary with property names as key and values as value. This values get automatically converted to string based on the properties types. The dictionary can easily be serialized for storage. It will also apply any transformation to properties that implement the **AuditOptionsAttribute**.

### ConvertToAuditValue

=== "ASP.NET"

```csharp
public static object? ConvertToAuditValue(object? value)
```

This method will convert properties values of any type to a string value. It is used internally by **ToAuditObject**. It can be used to generate the correct values of Json properties.

## **Real-world example**

The next example shows a complex **SqlAuditProvider** that takes the data received in **AuditEntry** and persist it in a table using the **AuditEntryEntity** model. This provider offers complex functionality, like serializing the changes.

=== "ASP.NET"

```csharp
public class SqlAuditProvider(ICurrentUserService currentUserService, IAuditEntryEntityService auditEntryEntityService, IUsersService usersService) : IAuditProvider
{
    public async Task AuditAsync(AuditEntry entry)
    {
        try
        {
            AuditEntryEntity newAuditEntity = new()
            {
                ExecutionId = Guid.NewGuid(),
                CreatedAtUtc = DateTime.UtcNow,
                UserId = usersService.GetByUserEntityIds([currentUserService.UserId!]).FirstOrDefault()?.Id,
                ProcessType = entry.ProcessType,
                ProcessCode = entry.ProcessCode,
                EntityId = entry.EntityId,
                EntityType = entry.EntityType,
                Action = entry.Action
            };

            if (entry.Action == AuditActionEnum.Create)
                newAuditEntity.AfterDataJson = GetSerializedAuditData(entry.AfterData!);

            if (entry.Action == AuditActionEnum.Update)
                newAuditEntity.ChangesJson = JsonConvert.SerializeObject(BuildChanges(
                    entry.BeforeData!,
                    entry.AfterData!));

            if (entry.Action == AuditActionEnum.Delete)
                newAuditEntity.BeforeDataJson = GetSerializedAuditData(entry.BeforeData!);

            if (entry.Action != AuditActionEnum.Update || newAuditEntity.ChangesJson != "[]")
                await auditEntryEntityService.Add(newAuditEntity);
        }
        catch (Exception ex)
        {
            throw new Exception(ex.Message);
        }
    }

    private static string? GetSerializedAuditData(object? data)
    {
        if (data is null)
            return null;

        object projected = ObjectProjectionBuilder.ToAuditObject(data);
        JObject jsonObj = JObject.FromObject(projected);
        jsonObj.Remove(nameof(BaseEntity<>.RowVersion));

        return JsonConvert.SerializeObject(jsonObj);
    }

    private static List<JsonChangeNode> BuildChanges(object? before, object? after)
    {
        List<JsonChangeNode> result = [];

        if (before == null && after == null)
            return result;

        if (before == null || after == null)
        {
            result.Add(new JsonChangeNode
            {
                OldValue = before,
                NewValue = after
            });

            return result;
        }

        PropertyInfo[] properties = before.GetType().GetProperties();

        foreach (PropertyInfo property in properties)
        {
            if (property.Name == nameof(BaseEntity<>.RowVersion))
                continue;

            AuditBehavior? auditBehavior = property.GetCustomAttributes(typeof(AuditOptionsAttribute), false)
                        .Cast<AuditOptionsAttribute>()
                        .FirstOrDefault()?.AuditBehavior;

            bool maskProperty = false;

            if (auditBehavior != null)
            {
                if (auditBehavior == AuditBehavior.Ignore)
                    continue;
                if (auditBehavior == AuditBehavior.Mask)
                    maskProperty = true;
            }

            object? oldValue = ObjectProjectionBuilder.ConvertToAuditValue(property.GetValue(before));
            object? newValue = ObjectProjectionBuilder.ConvertToAuditValue(property.GetValue(after));

            JsonChangeNode? node = CompareProperty(property.Name, oldValue, newValue, maskProperty);

            if (node != null)
                result.Add(node);
        }

        return result;
    }

    private static JsonChangeNode? CompareProperty(string propertyName, object? oldValue, object? newValue, bool maskProperty)
    {
        if (oldValue is IEnumerable oldEnum && newValue is IEnumerable newEnum
            && oldValue is not string && newValue is not string)
        {
            List<object?> oldItems = [.. oldEnum.Cast<object?>()];
            List<object?> newItems = [.. newEnum.Cast<object?>()];

            if (oldItems.SequenceEqual(newItems))
                return null;
        }

        if (Equals(oldValue, newValue))
            return null;

        return new JsonChangeNode
        {
            PropertyName = propertyName,
            OldValue = maskProperty ? "****" : oldValue,
            NewValue = maskProperty ? "****" : newValue
        };
    }
}
```

!!! Note
    GreenFire intentionally keeps the auditing infrastructure lightweight. It provides the information required to audit operations, while leaving storage, visualization, compliance requirements, and integration with third-party systems entirely up to the application.
