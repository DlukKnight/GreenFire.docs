# **Table widget provider**

## **Description**

Delivers the data that a [**TableWidget**](../../../components/widgets/tablewidget.md) will show.

## **Definition**

=== "ASP.NET"

```csharp
public abstract class TableWidgetProvider<TState> : BaseDashboardWidgetProvider<TableWidgetData, TState>
```

Table widget providers are very simple to define, by inheriting from the **TableWidgetProvider** abstract class, defining the generic **TState** parameter to reflect the class used for the filters in the containing dashboard and decorating the provider with the **DashboardWidgetCode** attribute. This code is unique and there should be only one among all the widgets, trying to register a second one with the same code will end in an API validation failure.

Basic example:

=== "ASP.NET"

```csharp
[DashboardWidgetCode(nameof(TestTableWidget))]
public class TestTableWidget : TableWidgetProvider<TestDashboardState>
```

After this setup is satisfied, we are ready to start implementing the different methods that configure the definition.

## **Methods**

### GetData

=== "ASP.NET"

```csharp
public abstract Task<TData> GetData(TState? state);
```

Returns the data that the control will display, in this case, **TData** being the **TableWidgetData** class. The **state** parameter will carry the filters selected values to correctly filter the data if needed.

The **TableWidgetData** class is defined as follows:

=== "ASP.NET"

```csharp
public class TableWidgetData : IWidgetData
{
    public required ICollection<TableWidgetColumnData> Columns { get; set; }
    public required ICollection<TableWidgetRowData> Rows { get; set; }
}

public class TableWidgetColumnData
{
    public required string Key { get; set; }
    public required string Title { get; set; }
    public int? Width { get; set; }
    public required string Align { get; set; }
    public string? FontColor { get; set; }
    public string? BackgroundColor { get; set; }
}

public class TableWidgetRowData
{
    public required Dictionary<string, TableWidgetFieldData> Fields { get; set; }
}

public class TableWidgetFieldData
{
    public required object? Value { get; set; }
    public string? FontColor { get; set; }
    public string? BackgroundColor { get; set; }
}
```

Although several classes are involved, they simply describe a table by defining its columns and rows.

For **TableWidgetColumnData**, the properties definitions are as follows:

|**Property**   |**Description**                                                                                                   |
|---------------|------------------------------------------------------------------------------------------------------------------|
|Key            |Defines the key used for the column, which the rows will latter reference in each field                           |
|Title          |The title shown in the header of the columns                                                                      |
|Width          |A fixed width. If not sent, the column will try to take equal space with other columns                            |
|Align          |The alignment of the text for each field value. Options are "left", "right" and "center"                          |
|FontColor      |The color of the font on the column's header. If not sent, the default theme's font color will be used            |
|BackgroundColor|The color of the background on the column's header. If not sent, the default theme's background color will be used|

Each **TableWidgetRowData** represents one row in the table. The Fields dictionary uses the column **Key** as its dictionary key, allowing each field to be matched with the correct column.

As for **TableWidgetFieldData**, the properties definitions are as follows:

|**Property**   |**Description**                                                                                          |
|---------------|---------------------------------------------------------------------------------------------------------|
|Value          |The actual value that will be shown in the table                                                         |
|FontColor      |The color of the font on the field. If not sent, the default theme's font color will be used             |
|BackgroundColor|The color of the background on the field. If not sent, the default theme's background color will be used |

A really basic static example:

=== "ASP.NET"

```csharp
[DashboardWidgetCode(nameof(TestTableWidget))]
public class TestTableWidget(ITestEntityService service) : TableWidgetProvider<TestDashboardState>
{
    public override async Task<TableWidgetData> GetData(TestDashboardState? state)
    {
        return new TableWidgetData
        {
            Columns =
            [
                new TableWidgetColumnData() { Key = "item", Title = "Item", Align = "left"},
                new TableWidgetColumnData() { Key = "price", Title = "Price", Align = "right"},
            ],

            Rows =
            [
                new TableWidgetRowData()
            {
                Fields = new()
                {
                    ["item"] = new TableWidgetFieldData { Value = "Phone" },
                    ["price"] = new TableWidgetFieldData { Value = 1000 }
                }
            },
            new TableWidgetRowData()
            {
                Fields = new()
                {
                    ["item"] = new TableWidgetFieldData { Value = "Bottle" },
                    ["price"] = new TableWidgetFieldData { Value = 5 }
                }
            },
            new TableWidgetRowData()
            {
                Fields = new()
                {
                    ["item"] = new TableWidgetFieldData { Value = "Mouse" },
                    ["price"] = new TableWidgetFieldData { Value = 15 }
                }
            },
            new TableWidgetRowData()
            {
                Fields = new()
                {
                    ["item"] = new TableWidgetFieldData { Value = "TV" },
                    ["price"] = new TableWidgetFieldData { Value = 300 }
                }
            }
            ]
        };
    }
}
```

And an advanced example, returning a more complex configuration:

=== "ASP.NET"

```csharp
[DashboardWidgetCode(nameof(TestTableWidget))]
public class TestTableWidget(ITestEntityService service) : TableWidgetProvider<TestDashboardState>
{
    public override async Task<TableWidgetData> GetData(TestDashboardState? state)
    {
        IQueryable<TestEntity> entities = service.Get();

        if (state.DateFrom is not null)
            entities = entities.Where(p => p.DateTimeValue == null || p.DateTimeValue >= state.DateFrom);

        if (state.DateTo is not null)
            entities = entities.Where(p => p.DateTimeValue == null || p.DateTimeValue <= state.DateTo);

        List<TestEntity> latest = [.. entities.OrderByDescending(p => p.DateTimeValue).Take(10)];

        return new TableWidgetData()
        {
            Columns =
            [
                new TableWidgetColumnData() { Key = "name", Title = "Name", Align = "left", FontColor = "#000000" },
                new TableWidgetColumnData() { Key = "value", Title = "Value", Align = "right", FontColor = "#000000"},
                new TableWidgetColumnData() { Key = "date", Title = "Date", Align = "left", FontColor = "#000000" },
                new TableWidgetColumnData() {Key = "active", Title = "Active", Align = "center", FontColor = "#000000"}
            ],
            Rows =
            [
                .. latest.Select(p => new TableWidgetRowData { Fields = new Dictionary<string, TableWidgetFieldData>
                {
                    ["name"] = new TableWidgetFieldData() { Value = p.Name!.Value,
                                                            FontColor = p.Active ? "#518f5f" : "#b54656",
                                                            BackgroundColor = p.Active ? "#c8ffc5" : "#fecbc8" },
                    ["value"] = new TableWidgetFieldData() { Value = p.Value is null ? "" : p.Value,
                                                             FontColor = p.Active ? "#518f5f" : "#b54656",
                                                             BackgroundColor = p.Active ? "#c8ffc5" : "#fecbc8" },
                    ["date"] = new TableWidgetFieldData() { Value = p.DateTimeValue is null ? "" : p.DateTimeValue.Date.ToString()!,
                                                            FontColor = p.Active ? "#518f5f" : "#b54656",
                                                            BackgroundColor = p.Active ? "#c8ffc5" : "#fecbc8" },
                    ["active"] = new TableWidgetFieldData() { Value = p.Active ? "Yes" : "No",
                                                              FontColor = p.Active ? "#518f5f" : "#b54656",
                                                              BackgroundColor = p.Active ? "#c8ffc5" : "#fecbc8" }
                }
                })
            ]
        };
    }
}
```

And later, when adding the widget, this provider is used in its constructor by passing the provider's type:

=== "ASP.NET"

```csharp
row.AddWidgetToRow(new TableWidget(typeof(TestTableWidget), "Test table widget") 
{ 
    UpdateTimeInSeconds = 5 
});
```
