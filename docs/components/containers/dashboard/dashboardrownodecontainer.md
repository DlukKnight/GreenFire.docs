# **DashboardRowNodeComponent**

## **Description**

Represents a collection of dashboard nodes ordered in a row pattern.

Row containers are very simple, requiring almost no configuration.

This container inherits all properties from **DashboardContainerNode**. The metadata below only documents the properties specific to **DashboardRowNode**.

## **DashboardRowNode metadata**

=== "React"

```ts
interface DashboardRowNode extends DashboardContainerNode {
  rowIndex: number;
  columnsAmount: number;
};
```

### rowIndex

Index property of the row, automatically assigned by the backend.

### columnsAmount

The amount of columns the row possesses, automatically assigned by the backend.

## Example

=== "ASP.NET"

```csharp
DashboardRowNode row = layout.AddRow();
```
