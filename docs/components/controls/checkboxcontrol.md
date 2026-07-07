# **CheckboxComponent**

## **Description**

Renders a standard checkbox control.

Checkboxes always have value. If no value is sent, they will default to **false**.

This control inherits all properties from **BaseControl**. The metadata below only documents the properties specific to **CheckboxControl**.

## **CheckboxControl metadata**

=== "React"

```ts
interface CheckboxControl extends BaseControl {
  nodeLayoutMode: NodeLayoutMode;
}

enum NodeLayoutMode {
  Aligned,
  Compact
}
```

### nodeLayoutMode

Controls the way the control aligns vertically.

In **Aligned** mode, the control will align itself with other controls that have labels by getting the vertical position in the middle of the body of the control. Best used when the control shares the same row with other control types.

In **Compact** mode, the control will not try to align and take the natural position. Best used when the control is by itself or when it is grouped with other checkboxes.

## Example

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.Checkbox,
    new CheckboxControl
    {
        NodeLayoutMode = NodeLayoutMode.Aligned
    });
```
