# **Lookup provider**

## **Description**

Delivers the collection of options that a [**LookupControl**](../../components/controls/lookupcontrol.md) will show.

## **Definition**

=== "ASP.NET"

```csharp
public abstract class BaseLookup : ILookupControl
```

Lookup providers are very simple to define, by inheriting from the **BaseLookup** abstract class and decorating them with the **LookupCode** attribute. This code is unique and there should be only one among all the lookups, trying to register a second one with the same code will end in an API validation failure.

Basic example:

=== "ASP.NET"

```csharp
[LookupCode(nameof(TestLookupProvider))]
public class TestLookupProvider(ITestEntityService service) : BaseLookup
```

After this setup is satisfied, we are ready to start implementing the different methods that configure the definition.

## **Methods**

### GetLookupData

=== "ASP.NET"

```csharp
public abstract IReadOnlyList<LookupOptionDto> GetLookupData(string processCode, string? processEntityId);
```

Returns the `List<LookupOptionDto>` that the control will use to show its options. Both the **processCode** and **processEntityId** parameters exist for the purpose of filtering or altering the options if a specific process and/or record is requesting this data.

Example:

=== "ASP.NET"

```csharp
public override IReadOnlyList<LookupOptionDto> GetLookupData(string processCode, string? processEntityId)
{
    IQueryable<BoxesEntity> query = service.Get();

    // If I'm accessing this lookup from the articles Crud Process, I only want to show boxes made of wood.
    if(processCode == "articlesCrud")
        query = query.Where(p => p.Material == MaterialsEnum.Wood); 

    return [.. query.Select(p => new LookupOptionDto
    {
        Value = p.Id.Value,
        Label = p.Name.Value
    })];
}
```

### DefaultValue

=== "ASP.NET"

```csharp
public abstract object? DefaultValue(string processCode, string? processEntityId);
```

Returns the **default value** that the control will show if no value is sent. Both the **processCode** and **processEntityId** parameters exist for the purpose of filtering or altering this value if a specific process and/or record is requesting this data.

Example:

=== "ASP.NET"

```csharp
public override object? DefaultValue(string processCode, string? processEntityId)
{
    // If I'm accessing this lookup from the articles Crud Process, I want it's default to be the last record.
    if(processCode == "articlesCrud")
        return service.Get().LastOrDefault()?.Id.Value.ToString();
    
    return service.Get().FirstOrDefault()?.Id.Value.ToString();
}
```

## **Minimal implementation example**

The next example show a minimal implementation with the simplest cases possible:

=== "ASP.NET"

```csharp
[LookupCode(nameof(TestLookupProvider))]
public class TestLookupProvider(ITestEntityService service) : BaseLookup
{
    public override object? DefaultValue(string processCode, string? processEntityId)
    {
        return service.Get().FirstOrDefault()?.Id.Value.ToString();
    }

    public override IReadOnlyList<LookupOptionDto> GetLookupData(string processCode, string? processEntityId)
    {
        return [.. service.Get().Select(p => new LookupOptionDto
        {
            Value = p.Id.Value,
            Label = p.Name.Value
        })];
    }
}
```

And later, when adding the control, this provider is used in its constructor by passing the provider's type:

=== "ASP.NET"

```csharp
row.AddControlToRow(e => e.Lookup_Id, new LookupControl(typeof(TestLookupProvider)));
```
