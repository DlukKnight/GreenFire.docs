# **MetricWidgetComponent**

## **Description**

Displays a single numeric value accompanied by an icon.

Metric widgets are designed to present a single KPI or important numeric value at a glance, like sales amount, entities created and so on. They are small, direct and easy to read.

Metric widgets have their own definition in the backend through a [**Metric Widget Provider**](../../definitions/providers/widgets/metricwidget.md), which can be identified by their **code**.

This widget inherits all properties from **BaseWidget**. The metadata below only documents the properties specific to **MetricWidget**.

## **MetricWidget metadata**

=== "React"

```ts
interface MetricWidget extends BaseWidget {
  title: string;
  iconColor: string;
  icon: string;
}
```

### title

Text title of the widget.

### iconColor

The color of the icon in hexadecimal.

### icon

Icon to show. The options are as follows, from Lucide Icons:

=== "ASP.NET"

```csharp
public enum DashboardMetricIcons
{
    Activity,
    CheckSquare2,
    TrendingUp,
    DollarSign
}
```

## Example

=== "ASP.NET"

```csharp
row.AddWidgetToRow(
  new MetricWidget(typeof(MetricColorCountWidget), "Colors") 
  { 
      UpdateTimeInSeconds = 5,
      IconColor = "#f59e0b", 
      Icon = DashboardMetricIcons.TrendingUp 
  });
```
