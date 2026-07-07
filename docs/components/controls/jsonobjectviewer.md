# **JsonObjectViewerComponent**

## **Description**

Renders a control that shows the values contained inside a Json property.

The control will just render each property and its value in an informative layout. This is useful for [**audit**](../../definitions/integrations/audit.md) purposes, since it can be used to show the state of an entity on creation or deletion.

This control inherits all properties from **BaseControl**, but it uses no configurations. The only parameter needed is its value, which is a regular Json property.

## Example

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.BeforeDataJson,
    new JsonObjectViewerControl());
```
