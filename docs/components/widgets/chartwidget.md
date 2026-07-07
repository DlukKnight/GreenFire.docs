# **ChartWidgetComponent**

## **Description**

Displays one or more data series using a configurable chart visualization.

Chart widgets are intended to visualize aggregated or grouped data. Examples include:

* Sales per month
* Articles by category
* Tickets by priority
* CPU usage over time

Chart widgets have their own definition in the backend through a [**Chart Widget Provider**](../../definitions/providers/widgets/chartwidget.md), which can be identified by their **code**.

This widget inherits all properties from **BaseWidget**. The metadata below only documents the properties specific to **ChartWidget**.

## **Choosing a chart type**

| **Chart**| **Best used for**                                                                   |
| -------- | ----------------------------------------------------------------------------------- |
| Bar      | Comparing values across categories (sales by region, articles by color).            |
| Line     | Showing trends over time (daily sales, monthly revenue).                            |
| Area     | Similar to a line chart, but emphasizes accumulated volume over time.               |
| Pie      | Showing proportions that add up to a whole. Best with a small number of categories. |
| Doughnut | Same as Pie, with a more compact visual style and room for central information.     |

## **ChartWidget metadata**

=== "React"

```ts
interface ChartWidget extends BaseWidget {
  title: string;
  chartType: WidgetChartType;
  showLegend: boolean;
  showGrid: boolean;
  showTooltip: boolean;
  stacked: boolean;
};
```

### title

Text title of the widget.

### chartType

The type of chart to be displayed. There are several options.

=== "React"

```ts
enum WidgetChartType {
  Bar = "Bar",
  Line = "Line",
  Pie = "Pie",
  Doughnut = "Doughnut",
  Area = "Area"
}
```

### showLegend

Shows or hides the legend that identifies each rendered series.

### showGrid

Shows or hides the background grid lines used to improve readability of the chart. This property affects **Bar**, **Line** and **Area** widget types.

### showTooltip

Enables the rendering of a tooltip showing information when hovering with the mouse over the graph.

### stacked

Displays multiple series stacked on top of one another instead of side-by-side. This property affects **Bar**, and **Area** widget types.

## Example

=== "ASP.NET"

```csharp
row.AddWidgetToRow(
  new ChartWidget(typeof(ColorsChartWidget), "Colors per article") 
  { 
      UpdateTimeInSeconds = 5,
      ShowLegend = true, 
      ChartType = WidgetChartType.Bar,
      ShowLegend = true, 
      ShowGrid = true, 
      ShowTooltip = true, 
      Stacked = false
  });
```
