# **NumberControlComponent**

## **Description**

Renders a standard single-line number input.

The control allows input of any character, but it will show an error if the value is not a valid number after validations.

This control inherits all properties from **BaseControl**. The metadata below only documents the properties specific to **NumberControl**.

## **NumberControl metadata**

=== "React"

```ts
interface NumberControl extends BaseControl {
  min?: number;
  max?: number;
}
```

### min

Minimum value accepted.

It will be shown as error after validations.

### max

Maximum value accepted.

It will be shown as error after validations.

## Example

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.NumericValue,
    new NumberControl
    {
        Required = true,
        Min = 1,
        Max = 5
    });
```
