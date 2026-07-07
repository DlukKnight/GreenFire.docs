# DetailComponent

## **Description**

Renders detail control which owns its own RootLayout representing the controls inside the detail.

The control conditions and validations are declared at the parent's level, and the NodeStates are also passed to the control, so validations occur at the root level of the relation and work completely with the detail's layout.

This control inherits all properties from **BaseControl**. The metadata below only documents the properties specific to **DetailControl**.

## **DetailControl metadata**

=== "React"

```ts
interface DetailControl extends BaseControl {
  rootLayout: ContainerNode;
}
```

### rootLayout

The layout configuration for the detail.

## Example

=== "ASP.NET"

```csharp
DetailControlBuilder<Detail> detail = new();

RowNodeBuilder<Detail> detailRow = detail.AddRow();

detailRow.AddControlToRow(
    d => d.Name, 
    new TextControl 
    { 
        Required = true 
    });

detailRow.AddControlToRow(
    d => d.NumericValue, 
    new NumberControl());

detailRow.AddControlToRow(
    d => d.DetailCheckbox, 
    new CheckboxControl 
    { 
        NodeLayoutMode = NodeLayoutMode.Aligned 
    });

row.AddControlToRow(e => e.Details, detail.Detail);    
```
