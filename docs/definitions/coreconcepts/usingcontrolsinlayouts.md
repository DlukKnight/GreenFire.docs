# **Using controls in layouts**

## **Description**

[**Controls**](../../components/controls/index.md) are instantiated directly inside layouts.

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.Name,
    new TextControl()
    {
        Required = true,
        MaxLength = 50
    });
```

The metadata sent for the control is configured in the initializer, either by constructor parameters or by properties.

Every control inherits from a common **BaseControl** class, and as such, share a pool of common properties, like, for example, **Required**. Check the [**BaseControlComponent**](../../components/controls/basecontrol.md) documentation for more information.

For the complete list of properties and behavior of each control, as well as an example of usage, refer to the [**Frontend Controls documentation**](../../components/controls/index.md).
