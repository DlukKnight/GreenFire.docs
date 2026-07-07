# **TimeControlComponent**

## **Description**

Renders a time selector.

The control allows empty values. In case a control with both date and time components is needed, refer to [**DateTimeControlComponent**](datetimecontrol.md).

This control inherits all properties from **BaseControl**. It does not receive any special metadata from **TimeControl**.

## Example

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.TimeValue, new TimeControl());
```
