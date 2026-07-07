# **Chart widget provider**

## **Description**

Delivers the data that a [**ChartWidget**](../../../components/widgets/chartwidget.md) will show.

## **Definition**

=== "ASP.NET"

```csharp
public abstract class ChartWidgetProvider<TState> : BaseDashboardWidgetProvider<ChartWidgetData, TState>
```

Chart widget providers are very simple to define, by inheriting from the **ChartWidgetProvider** abstract class, defining the generic **TState** parameter to reflect the class used for the filters in the containing dashboard and decorating the provider with the **DashboardWidgetCode** attribute. This code is unique and there should be only one among all the widgets, trying to register a second one with the same code will end in an API validation failure.

Basic example:

=== "ASP.NET"

```csharp
[DashboardWidgetCode(nameof(TestChartWidget))]
public class TestChartWidget() : ChartWidgetProvider<TestDashboardState>
```

After this setup is satisfied, we are ready to start implementing the different methods that configure the definition.

## **Methods**

### GetData

=== "ASP.NET"

```csharp
public abstract Task<TData> GetData(TState? state);
```

Returns the data that the control will display, in this case, **TData** being the **ChartWidgetData** class. The **state** parameter will carry the filters selected values to correctly filter the data if needed.

The **ChartWidgetData** class is defined as follows:

=== "ASP.NET"

```csharp
public class ChartWidgetData : IWidgetData
{
    public required List<string> Labels { get; set; } = [];

    public required List<ChartSeriesData> Series { get; set; } = [];
}

public class ChartSeriesData
{
    public required string Name { get; set; } = default!;

    public required List<decimal> Values { get; set; } = [];

    public string? Color { get; set; }
}
```

**Labels** defines the values shown on the chart's X axis. In most scenarios these correspond to the grouping key, such as dates, categories or months
For **Series**, each **ChartSeriesData** represents one plotted series. The values must be in the same order as the labels. The **Color** property in the series is an optional hexadecimal color, and if not send, the frontend will assign one.

Bottom line, a chart is composed of two elements:

- **Labels**, which define the X axis.
- **Series**, which define one or more sets of values aligned with those labels.

For example, representing sales per day:

| **Labels**| **Sales** |
| --------- | --------: |
| Monday    |        100|
| Tuesday   |        150|
| Wednesday |         80|

This table will translate to:

=== "ASP.NET"

```csharp
Labels = [
    "Monday",
    "Tuesday",
    "Wednesday"
]

Series =
[
    new ChartSeriesData
    {
        Name = "Sales",
        Values = [100, 150, 80]
    }
]
```

And in the case of multiple series, we could have the same example as before, but adding **Expenses**:

| **Day**       | **Sales** | **Expenses** |
| ------------- | --------: | -----------: |
| Monday        |       100 |           40 |
| Tuesday       |       150 |           60 |
| Wednesday     |        80 |           30 |

The our series definition will have both **ChartSeriesData**:

=== "ASP.NET"

```csharp
Series =
[
    Sales,
    Expenses
]
```

A really basic static example:

=== "ASP.NET"

```csharp
[DashboardWidgetCode(nameof(TestChartWidget))]
public class TestChartWidget() : ChartWidgetProvider<TestDashboardState>
{
    public override async Task<ChartWidgetData> GetData(TestDashboardState? state)
    {        
        return new ChartWidgetData
        {
            Labels =
            [
                "January",
                "February",
                "March"
            ],

            Series =
            [
                new ChartSeriesData
                {
                    Name = "Sales",
                    Values =
                    [
                        120,
                        95,
                        160
                    ]
                }
            ]
        };
    }
}
```

And an advanced example, using projection:

=== "ASP.NET"

```csharp
[DashboardWidgetCode(nameof(TestChartWidget))]
public class TestChartWidget(ITestEntityService service) : ChartWidgetProvider<TestDashboardState>
{
    public record TestChartProjection(DateTimeOffset Date, int? PriceSold);

    public override async Task<ChartWidgetData> GetData(TestDashboardState? state)
    {        
        IQueryable<TestEntity> entities = service.Get();

        if (state.DateFrom is not null)
            entities = entities.Where(p => p.DateTimeValue == null || p.DateTimeValue >= state.DateFrom);

        if (state.DateTo is not null)
            entities = entities.Where(p => p.DateTimeValue == null || p.DateTimeValue <= state.DateTo);

        List<TestChartProjection> projected = await entities.Where(p => p.DateTimeValue != null && p.PriceSold != null).Select(p => new TestChartProjection(p.DateTimeValue, p.PriceSold)).ToListAsync();
        List<IGrouping<DateTime, TestChartProjection>> grouped = [.. projected.GroupBy(p => p.Date.Date).OrderBy(g => g.Key)];

        ChartWidgetData data = new()
        {
            Labels = [.. grouped.Select(g => g.Key.ToString())],
            Series =
            [
                new ChartSeriesData()
                {
                    Name = "Total sales money",
                    Values = [.. grouped.Select(g => g.Sum(x => (decimal?)x.PriceSold.Value) ?? 0)]
                }
            ]
        };

        return data;
    }
}
```

And later, when adding the widget, this provider is used in its constructor by passing the provider's type:

=== "ASP.NET"

```csharp
row.AddWidgetToRow(new ChartWidget(typeof(TestChartWidget), "Test chart widget") 
{ 
    UpdateTimeInSeconds = 5, 
    ChartType = WidgetChartType.Bar
});
```
