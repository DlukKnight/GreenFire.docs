# **UrlControlComponent**

## **Description**

Renders a standard single-line Url input.

It will automatically validate that the correct format for an Url is entered.

This control inherits all properties from **BaseControl**. It does not receive any special metadata from **UrlControl**.

## Example

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.Url,
    new UrlControl
    {
        Required = true
    });
```
