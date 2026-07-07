# **EmailControlComponent**

## **Description**

Renders a standard single-line email input.

It will automatically validate that the correct format for an email is entered.

This control inherits all properties from **BaseControl**. It does not receive any special metadata from **EmailControl**.

## Example

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.Email,
    new EmailControl
    {
        Required = true
    });
```
