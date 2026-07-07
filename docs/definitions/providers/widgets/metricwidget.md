# **Metric widget provider**

## **Description**

Delivers the data that a [**MetricWidget**](../../../components/widgets/metricwidget.md) will show.

## **Definition**

=== "ASP.NET"

```csharp
public abstract class MetricWidgetProvider<TState> : BaseDashboardWidgetProvider<MetricWidgetData, TState>
```

Metric widget providers are very simple to define, by inheriting from the **MetricWidgetProvider** abstract class, defining the generic **TState** parameter to reflect the class used for the filters in the containing dashboard and decorating the provider with the **DashboardWidgetCode** attribute. This code is unique and there should be only one among all the widgets, trying to register a second one with the same code will end in an API validation failure.

Basic example:

=== "ASP.NET"

```csharp
[DashboardWidgetCode(nameof(TestMetricWidget))]
public class TestMetricWidget() : MetricWidgetProvider<TestDashboardState>
```

After this setup is satisfied, we are ready to start implementing the different methods that configure the definition.

## **Methods**

### GetData

=== "ASP.NET"

```csharp
public abstract Task<TData> GetData(TState? state);
```

Returns the data that the control will display, in this case, **TData** being the **MetricWidgetData** class. The **state** parameter will carry the filters selected values to correctly filter the data if needed.

The **MetricWidgetData** class is defined as follows:

=== "ASP.NET"

```csharp
public class MetricWidgetData : IWidgetData
{
    public required string Value { get; set; }
}
```

Since the only displayed value is a numeric value, the only required property is **Value**.

Example:

=== "ASP.NET"

```csharp
[DashboardWidgetCode(nameof(TestMetricWidget))]
public class TestMetricWidget(ITestEntityService service) : MetricWidgetProvider<TestDashboardState>
{
    public override async Task<MetricWidgetData> GetData(TestDashboardState? state)
    {
        IQueryable<TestEntity> entities = service.Get();
        
        if (state.DateFrom is not null)
            entities = entities.Where(p => p.DateTimeValue == null || p.DateTimeValue >= state.DateFrom);

        if (state.DateTo is not null)
            entities = entities.Where(p => p.DateTimeValue == null || p.DateTimeValue <= state.DateTo);

        return new MetricWidgetData() { Value = entities.Count().ToString() };
    }
}
```

And later, when adding the widget, this provider is used in its constructor by passing the provider's type:

=== "ASP.NET"

```csharp
row.AddWidgetToRow(new MetricWidget(typeof(TestMetricWidget), "Test widget") 
{ 
    UpdateTimeInSeconds = 5 
});
```
