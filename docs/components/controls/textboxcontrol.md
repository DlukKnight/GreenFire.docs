# **TextControlComponent**

## **Description**

Renders a standard single-line text input.

This control inherits all properties from **BaseControl**. The metadata below only documents the properties specific to **TextControl**.

## **TextControl metadata**

=== "React"

```ts
interface TextControl extends BaseControl {
  minLength?: number;
  maxLength?: number;
}
```

### minLength

Minimum number of characters accepted.

It will be shown as error after validations.

### maxLength

Maximum number of characters accepted.

It will be shown as error after validations.

This value is also applied to the underlying HTML input, preventing the user from typing more characters than allowed.

## Example

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.Name,
    new TextControl
    {
        Required = true,
        MinLength = 3,
        MaxLength = 50
    });
```
