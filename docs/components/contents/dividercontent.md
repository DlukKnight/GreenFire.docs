# **DividerContentComponent**

## **Description**

Renders a divider line, that may contain a description text. Meant to visually separate areas, while providing context if needed.

This content inherits all properties from **ContentNode**. The metadata below only documents the properties specific to **DividerContentNode**.

## **DividerContentNode metadata**

=== "React"

```ts
interface DividerContentNode extends ContentNode {
  text: string;
  height: number;
}
```

### text

The text shown in the divider, located at the left start of it.

### height

Controls the thickness of the line produced by the divider. The default value is 1, which equals 1px.

## Example

=== "ASP.NET"

```csharp
row.AddContentToRow(
    new DividerContent() 
    { 
        LogicalKey = "DivideKey" ,
        Text = "Colors",
        Height = 2
    });
```
