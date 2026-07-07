# **Dashboard**

## **Description**

Defines the metadata and behavior required to render and operate a complete Dashboard interface, with filters and [**widgets**](../../components/widgets/index.md).

## **Lifecycle**

A Dashboard follows this general flow:

1. The dashboard is discovered through the DashboardCode attribute.
2. The DashboardRootLayout, FiltersRootLayout, conditions and validation rules are loaded.
3. Widgets are loaded and present their data.
4. The user may interact with the filters.
5. Filters are applied.
6. Widgets reload their data.

## **First steps**

Dashboards are easy to define, by inheriting from the **Dashboard** abstract class and decorating them with the **DashboardCode** attribute. This code is unique and there should be only one among all the dashboards, trying to register a second one with the same code will end in an API validation failure.

=== "ASP.NET"

```csharp
public abstract class Dashboard<TState> : IDashboard
```

After those criteria are met, the only thing a dashboard needs is a class with the properties that are going to be used as filters pass as the **TState** parameter.

Basic example:

=== "ASP.NET"

```csharp
[DashboardCode("testDashboard")]
public class TestDashboard : Dashboard<TestDashboardState>
```

After this setup is satisfied, we are ready to start implementing the different methods that configure the definition.

## **Minimal implementation**

The smallest possible Dashboard consists of:

- DashboardCode
- [**GetTitle()**](#gettitle)
- [**BuildFilters()**](#buildfilters)
- [**GetWidgets()**](#getwidgets)

Everything else is optional.

## **Methods**

### Access

#### HasAccess

=== "ASP.NET"

```csharp
public virtual bool HasAccess() => true;
```

Return a boolean value that indicates if access criteria is met when trying to do any operation with the dashboard. By default, it always return **true**.

An example to this method could be checking if the logged user has the right permissions to access the process.

### Loading

#### GetDefaultData

=== "ASP.NET"

```csharp
public virtual async Task<TState> GetDefaultData()
{
    TState dto = Activator.CreateInstance<TState>();

    return dto;
}
```

This method is equivalent to the [**GetDefaultData**](./wizard.md#getdefaultdata) method used by [**Wizard**](./wizard.md). Check that method to see its characteristics plus an example.

### Presentation

#### GetTitle

=== "ASP.NET"

```csharp
public abstract string GetTitle();
```

Returns the title that the dashboard will show in the header.

#### BuildFilters

=== "ASP.NET"

```csharp
protected abstract Task<RootLayout> BuildFilters();
```

This method is equivalent to the [**GetRootLayout**](./crudprocess.md#getrootlayout) method used by [**Crud Processes**](./crudprocess.md), but using your **TState** class instead of **TEntity**. Check that method to see its characteristics plus an example.

### Behavior

#### GetFiltersConditions

=== "ASP.NET"

```csharp
protected virtual List<ConditionRule> GetFiltersConditions() => [];
```

This method is equivalent to the [**GetLayoutConditions**](./crudprocess.md#getlayoutconditions) method used by [**Crud Processes**](./crudprocess.md), but using your **TState** class instead of **TEntity**. Check that method to see its characteristics plus an example.

#### GetFiltersValidations

=== "ASP.NET"

```csharp
protected virtual List<ValidationRule> GetFiltersValidations() => [];
```

This method is equivalent to the [**GetValidationRules**](./crudprocess.md#getvalidationrules) method used by [**Crud Processes**](./crudprocess.md), but using your **TState** class instead of **TEntity**. Check that method to see its characteristics plus an example.

#### GetWidgets

=== "ASP.NET"

```csharp
public abstract Task<DashboardLayout> GetWidgets();
```

This method is very similar to any of the RootLayout methods to build layouts, with the difference that uses nodes exclusive to dashboards, which end up building [**widgets**](../../components/widgets/index.md).

The widget layout is built only once when the dashboard is initialized. Individual widgets are then responsible for refreshing their own data according to their configured update interval.

Check the next example to see a very basic layout with varied [**widgets**](../../components/widgets/index.md).

```csharp
public override async Task<DashboardLayout> GetWidgets()
{
    DashboardLayout layout = new();

    DashboardRowNode rowTexts = layout.AddRow();

    rowTexts.AddWidgetToRow(new TextWidget(typeof(TestTextWidget)) { UpdateTimeInSeconds = 5 });

    DashboardRowNode rowMetrics = layout.AddRow();

    rowMetrics.AddWidgetToRow(new MetricWidget(typeof(TestMetricWidget), "My metric widget") { UpdateTimeInSeconds = 5 });

    DashboardRowNode rowCharts = layout.AddRow();

    rowCharts.AddWidgetToRow(new ChartWidget(typeof(TestChartWidget), "My chart widget") { UpdateTimeInSeconds = 5, ChartType = WidgetChartType.Bar });

    DashboardRowNode rowTables = layout.AddRow();

    rowTables.AddWidgetToRow(new TableWidget(typeof(TestTableWidget), "My table widget") { UpdateTimeInSeconds = 5 });
    rowTables.AddWidgetToRow(new ListWidget(typeof(TestListWidget), "My list widget") { UpdateTimeInSeconds = 5 });

    return layout;
}
```
