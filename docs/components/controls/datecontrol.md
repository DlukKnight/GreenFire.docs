# **DateControlComponent**

## **Description**

Renders a date selector.

The control allows empty values. In case a control with both date and time components is needed, refer to [**DateTimeControlComponent**](datetimecontrol.md).

This control inherits all properties from **BaseControl**. It does not receive any special metadata from **DateControl**.

## Example

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.DateValue, new DateControl());
```
