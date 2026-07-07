# **CollectionGridComponent**

## **Description**

Renders a simple informative display grid.

This control inherits all properties from **BaseControl**. The metadata below only documents the properties specific to **CollectionGridControl**.

## **CollectionGridControl metadata**

=== "React"

```ts
interface CollectionGridControl extends BaseControl {
  columns: any[];
  height?: number;
  exportTitle: string;
}
```

### columns

The collection of columns to render with all their properties.

### height

Defines the height of the grid. If left empty, the height will take a 100% value, trying to take al remaining space. This is most beneficial if the grid is the last control or if it is alone, as it will be rendered using all space available.

### exportTitle

the title use for the files generated when using the "**Export CSV**" option of the grid.

## Example

=== "ASP.NET"

```csharp
List<ColumnDefinition> columns =
    [
        new("name", "Name"),
        new("date", "Date")
        {
            DataType = ColumnDataType.DateTime
        }
    ];

row.AddControlToRow(
    e => e.Collection, 
    new CollectionGridControl() 
    { 
        Columns = columns, 
        Height = "510px" 
    });
```
