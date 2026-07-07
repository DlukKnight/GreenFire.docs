# **LabelContentComponent**

## **Description**

Renders a text section meant as information, which can be styled with Html.

This content inherits all properties from **ContentNode**. The metadata below only documents the properties specific to **LabelContentNode**.

## **LabelContentNode metadata**

=== "React"

```ts
interface LabelContentNode extends ContentNode {
  text: string;
}
```

### text

The text shown in the label, which supports Html.

## Example

=== "ASP.NET"

```csharp
row.AddContentToRow(
    new LabelContent() 
    { 
        LogicalKey = "InfoLabelKey", 
        Text = "This is an <u><b>information</b></u> label!" 
    });
```
