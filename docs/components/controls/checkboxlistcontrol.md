# **CheckboxListComponent**

## **Description**

Renders an options selector by a collection of checkboxes.

Checkbox lists have their own definition in the backend through a [**CheckboxList Provider**](../../definitions/providers/checkboxlist.md), which can be identified by their **checkboxListCode**.

This control inherits all properties from **BaseControl**. The metadata below only documents the properties specific to **CheckboxListControl**.

## **CheckboxListControl metadata**

=== "React"

```ts
interface CheckboxListControl extends BaseControl {
  checkboxListCode: string;
}
```

### checkboxListCode

Identifies the definition in the backend to retrieve the selectable options.

## Example

=== "ASP.NET"

In the following example, a checkbox list control is created, which retrieves its options from the backend via the **CheckboxListProvider** definition.

```csharp
row.AddControlToRow(
    e => e.CheckboxList,
    new CheckboxListControl(typeof(CheckboxListProvider))
    {
        Required = true
    });
```
