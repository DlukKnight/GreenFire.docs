# **TableWidgetComponent**

## **Description**

Displays a collection of data delivered in rows through a table format, displaying information in rows and columns. They support color styling from the backend.

Table widgets are very useful for organizing raw data, allowing you to spot trends at a glance, perform side-by-side comparisons, and easily navigate through large sets of variables or metrics.

They can display large volumes of data (like financial logs, inventory, or system metrics) compactly without overwhelming the screen and allow users to look across rows and columns to contrast different items or time periods directly.

Table widgets have their own definition in the backend through a [**Table Widget Provider**](../../definitions/providers/widgets/tablewidget.md), which can be identified by their **code**.

This widget inherits all properties from **BaseWidget**. The metadata below only documents the properties specific to **TableWidget**.

## **TableWidget metadata**

=== "React"

```ts
interface TableWidget extends BaseWidget {
  title: string;
};
```

### title

Text title of the widget.

## Example

=== "ASP.NET"

```csharp
row.AddWidgetToRow(
  new TableWidget(typeof(SalesTableWidget), "Top 10 sales") 
  { 
      UpdateTimeInSeconds = 5 
  });
```
