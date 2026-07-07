# **RowNodeComponent**

## **Description**

Represents a collection of nodes ordered in a row pattern, with each node equally occupying a vertical position.

Row containers are very simple, requiring almost no configuration.

This container inherits all properties from **ContainerNode**. The metadata below only documents the properties specific to **RowNode**.

## **RowNode metadata**

=== "React"

```ts
interface RowNode extends ContainerNode {
  rowIndex: number;
  columnsAmount: number;
}
```

### rowIndex

Index property of the row, automatically assigned by the backend.

### columnsAmount

The amount of columns the row possesses. By default, this value is the same as the amount of child nodes the row has, but it can be sent to change the rendering behavior. For example, if the row contains two controls, but this property is set to three, the row will behave as if it has three columns, equally assigning space to each of them. This is useful in cases where a few controls are in a row, but they stretch too much and look odd, like, for example, a single [**Text control**](../../controls/textboxcontrol.md).

## Example

=== "ASP.NET"

```csharp
RowNodeBuilder<MyEntity> row = rootLayout.AddRow(3); // With 3 columns defined

RowNodeBuilder<MyEntity> rowInsideTab = tab.AddRow();

RowNodeBuilder<DetailEntity> detailRow = detail.AddRow();
```
