# **JsonChangesComponent**

## **Description**

Renders a control that shows the comparison of old and new values inside a Json property.

The most common use for this control is for [**audit**](../../definitions/integrations/audit.md) purposes, since it can effectively show the changes introduced in a control based on a property that was stored in a point of time.

This control inherits all properties from **BaseControl**, but it uses no configurations. The only parameter needed is its value, which is presented in a format where properties have their old and new values. For example:

=== "Json"

```json
"[{\"PropertyName\":\"Name\",\"OldValue\":\"Default\",\"NewValue\":\"Test\",\"Children\":null},{\"PropertyName\":\"Enabled\",\"OldValue\":false,\"NewValue\":true,\"Children\":null}]"
```

## Example

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.ChangesJson,
    new JsonChangesControl());
```
