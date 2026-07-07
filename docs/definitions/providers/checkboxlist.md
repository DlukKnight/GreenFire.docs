# **Checkbox list provider**

## **Description**

Delivers collection of options that a [**CheckboxListControl**](../../components/controls/checkboxlistcontrol.md) will show.

## **Definition**

=== "ASP.NET"

```csharp
public class BaseCheckboxList : ICheckboxListControl
```

Checkbox list providers are very simple to define, by inheriting from the **BaseCheckboxList** abstract class and decorating them with the **CheckboxListCode** attribute. This code is unique and there should be only one among all the checkbox list providers, trying to register a second one with the same code will end in an API validation failure.

Basic example:

=== "ASP.NET"

```csharp
[CheckboxListCode(nameof(TestCheckboxList))]
public class TestCheckboxList(ITestEntityService service) : BaseCheckboxList
```

After this setup is satisfied, we are ready to start implementing the different methods that configure the definition.

## **Methods**

### GetCheckboxListData

=== "ASP.NET"

```csharp
public virtual IReadOnlyList<CheckboxListDataModel> GetCheckboxListData(string processCode, string? processEntityId)
```

Returns the `List<CheckboxListDataModel>` that the control will use to show its options. Both the **processCode** and **processEntityId** parameters exist for the purpose of filtering or altering the options if a specific process and/or record is requesting this data.

Example:

=== "ASP.NET"

```csharp
public override IReadOnlyList<CheckboxListDataModel> GetCheckboxListData(string processCode, string? processEntityId)
{
    IQueryable<TestEntity> query = service.Get();

    // If the Crud process operating this list is discountedKits, and the first entity is selected, only the entities with price above 500 should be presented

    if(processCode == "discountedKits")
    {
        if(processEntityId == "1")
            query = query.Where(p => p.Price > 500);
    }

    List<TestEntity> data = [.. query];

    List<CheckboxListDataModel> result = [.. data
        .Select(e => new CheckboxListDataModel(e.Id, e.Name))];

    return result;
}
```
