# **DatagridControlComponent**

## **Description**

Renders a server data driven grid with selection enabled.

Datagrids have their own definition in the backend through a [**Datagrid Provider**](../../definitions/providers/datagrid.md), which can be identified by their **datagridCode**.

This control inherits all properties from **BaseControl**. The metadata below only documents the properties specific to **DatagridControl**.

## **DatagridControl metadata**

=== "React"

```ts
interface DatagridControl extends BaseControl {
  datagridCode: string;
  multiSelect: boolean;
  height?: number;
  exportTitle: string;
}
```

### datagridCode

Identifies the definition for the data provider in the backend.

### multiSelect

Toggles multiple row selection.

### height

Defines the height of the grid. If left empty, the height will take a 100% value, trying to take al remaining space. This is most beneficial if the grid is the last control or if it is alone, as it will be rendered using all space available.

### exportTitle

the title use for the files generated when using the "**Export CSV**" option of the grid.

## Example

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.Colors, 
    new DatagridControl(typeof(ColorsDatagrid)) 
    { 
        Required = true,
         MultiSelect = true, 
         Height = "100%" 
    });
```
