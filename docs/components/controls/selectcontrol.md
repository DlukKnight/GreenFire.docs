# **SelectControlComponent**

## **Description**

Renders a select control filled with predetermined options.

Select controls have their options fixed, unless their value depends on another property value, in which case options will be fetched from a definition in the backend known as a [**Select options provider**](../../definitions/providers/selectoptions.md), identified with **selectOptionsProviderCode**.

This control inherits all properties from **BaseControl**. The metadata below only documents the properties specific to **SelectControl**.

## **SelectControl metadata**

=== "React"

```ts
interface SelectControl extends BaseControl {
  options: any;
  includeEmptyOption: boolean;
  selectOptionsProviderCode?: string;
  dependsOnPropertyName?: string;
}
```

### options

The collection of possible values for the control.

### includeEmptyOption

Toggles the ability to chose no value from the select control. If disabled, the first available option will be the default value, unless a default value is sent from the owners of the select control.

### selectOptionsProviderCode

Identifies the definition for the options provider in the backend. Works in conjunction with **dependsOnPropertyName**.

### dependsOnPropertyName

Points to the property value that will trigger the data-fetching for options. If said property value changes, the control will retrieve new options based on the new value. Works in conjunction with **selectOptionsProviderCode**.

## Example

In the following example, two controls are created, the first one gets its options from **MaterialsEnum**, with the help of the [**SelectOptionsHelper**](../../definitions/builders/selectoptionsbuilders.md) provided by GreenFire, while the second gets its options from the **ColorOptionsProvider** in the backend, which will return them based on what value the property **Material** has, meaning that selecting a different material will change the available colors.

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.Material,
    new SelectControl()
    {
        Required = true,
        IncludeEmptyOption = false,
        Options = SelectOptionsHelper.FromEnum<MaterialsEnum>()
    });

row.AddControlToRow(
    e => e.Colors,
    new SelectControl(typeof(ColorOptionsProvider), "Material")
    {
        Required = true,
        IncludeEmptyOption = false
    });    
```
