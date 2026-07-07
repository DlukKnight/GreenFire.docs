# **DateTimeControlComponent**

## **Description**

Renders a date and time selector.

The control allows empty values, but both date ant time have to be sent, it won't allow partial values. In case these are needed, refer to [**DateControlComponent**](datecontrol.md) and [**TimeControlComponent**](timecontrol.md).

This control inherits all properties from **BaseControl**. It does not receive any special metadata from **DateTimeControl**.

## Example

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.DateTimeValue, new DateTimeControl());
```
