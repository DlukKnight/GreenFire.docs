# **Select options provider**

## **Description**

Delivers the collection of options that a [**SelectControl**](../../components/controls/selectcontrol.md) that depends on another property will show.

## **Definition**

=== "ASP.NET"

```csharp
public abstract class BaseSelectOptionsProvider : ISelectOptionsProvider
```

Select options providers are very simple to define, by inheriting from the **BaseSelectOptionsProvider** abstract class and decorating them with the **SelectOptionsProviderCode** attribute. This code is unique and there should be only one among all the providers, trying to register a second one with the same code will end in an API validation failure.

Basic example:

=== "ASP.NET"

```csharp
[SelectOptionsProviderCode(nameof(TestOptionsProvider))]
public class TestOptionsProvider() : BaseSelectOptionsProvider
```

After this setup is satisfied, we are ready to start implementing the different methods that configure the definition.

## **Methods**

### GetOptions

=== "ASP.NET"

```csharp
public abstract IReadOnlyList<SelectOption> GetOptions(string? dependsOnPropertyValue);
```

Returns the `List<SelectOption>` that the control will use to show its options. The **dependsOnPropertyValue** parameter contains the current value of the property specified when the [**SelectControl**](../../components/controls/selectcontrol.md) was created. It can be used to dynamically change the available options.

!!! Note
    Whenever the dependent property's value changes, the frontend automatically requests the options again from this provider.

Example:

=== "ASP.NET"

```csharp
[SelectOptionsProviderCode(nameof(ColorOptionsProvider))]
public class ColorOptionsProvider() : BaseSelectOptionsProvider
{
    public override IReadOnlyList<SelectOption> GetOptions(string? dependsOnPropertyValue)
    {
        return dependsOnPropertyValue switch
        {
            nameof(MaterialEnum.Wood) => SelectOptionsHelper.FromEnum<WoodColorsEnum>(),
            nameof(MaterialEnum.Metal) => SelectOptionsHelper.FromEnum<MetalColorsEnum>(),
            _ => SelectOptionsHelper.FromEnum<GeneralColorsEnum>(),
        };
    }
}
```

And later, when adding the control, this provider is used in its constructor by passing the provider's type, along with the property for the **material's type**:

=== "ASP.NET"

```csharp
row.AddControlToRow(e => e.Color_Id, new SelectControl(typeof(ColorOptionsProvider), nameof(ComponentEntity.MaterialType))
{
    Required = true,
    IncludeEmptyOption = false
});
```
