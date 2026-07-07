# **List widget provider**

## **Description**

Delivers the data that a [**ListWidget**](../../../components/widgets/listwidget.md) will show.

## **Definition**

=== "ASP.NET"

```csharp
public abstract class ListWidgetProvider<TState> : BaseDashboardWidgetProvider<ListWidgetData, TState>
```

List widget providers are very simple to define, by inheriting from the **ListWidgetProvider** abstract class, defining the generic **TState** parameter to reflect the class used for the filters in the containing dashboard and decorating the provider with the **DashboardWidgetCode** attribute. This code is unique and there should be only one among all the widgets, trying to register a second one with the same code will end in an API validation failure.

Basic example:

=== "ASP.NET"

```csharp
[DashboardWidgetCode(nameof(TestListWidget))]
public class TestListWidget : ListWidgetProvider<TestDashboardState>
```

After this setup is satisfied, we are ready to start implementing the different methods that configure the definition.

## **Methods**

### GetData

=== "ASP.NET"

```csharp
public abstract Task<TData> GetData(TState? state);
```

Returns the data that the control will display, in this case, **TData** being the **ListWidgetData** class. The **state** parameter will carry the filters selected values to correctly filter the data if needed.

The **ListWidgetData** class is defined as follows:

=== "ASP.NET"

```csharp
public class ListWidgetData : IWidgetData
{
    public List<ListWidgetItemData> Items { get; set; } = [];
}

public class ListWidgetItemData
{
    public string Title { get; set; } = "";
    public ParametersMessage? Description { get; set; }

    public string? Badge { get; set; }
    public WidgetBadgeVariant? BadgeColor { get; set; }

    public string? Image { get; set; }

    public string? Url { get; set; }
}
```

Each **ListWidgetItemData** in **Items** represents an item in the list.

For **ListWidgetItemData**, the properties definitions are as follows:

|**Property**   |**Description**                                                                                                               |
|---------------|------------------------------------------------------------------------------------------------------------------------------|
|Title          |The primary text displayed for the list item                                                                                  |
|Description    |Secondary text displayed below the title. Uses a [**ParameterMessage**](../../coreconcepts/parametersmessage.md) so values can be formatted and localized |
|Badge          |If sent, a badge will appear at the right of the item, containing the text sent in this property                              |
|BadgeColor     |If sent, it will define the badge's color                                                                                     |
|Image          |The image to display if the widget is set to show images. If not sent, a no-image indicator will be shown                     |
|Url            |If sent, a small link icon will appear, which will open the address sent into a new browser tab                               |

A really basic static example:

=== "ASP.NET"

```csharp
[DashboardWidgetCode(nameof(TestListWidget))]
public class TestListWidget : ListWidgetProvider<TestDashboardState>
{
    public override async Task<ListWidgetData> GetData(TestDashboardState? state)
    {
        return new ListWidgetData()
        {
            Items = [
                new ListWidgetItemData() { Title = "Green", Description = new ParametersMessage { Key = "The color of the trees" }, Badge = "Natural", BadgeColor = WidgetBadgeVariant.Success },
                new ListWidgetItemData() { Title = "Red", Description = new ParametersMessage  { Key = "The color of the fire" }, Badge = "Dangerous", BadgeColor = WidgetBadgeVariant.Danger },
                new ListWidgetItemData() { Title = "Yellow", Description = new ParametersMessage { Key = "The color of the sun" } }
            ]
        };
    }
}
```

And an advanced example, returning a more complex configuration:

=== "ASP.NET"

```csharp
[DashboardWidgetCode(nameof(TestListWidget))]
public class TestListWidget(ITestEntityService service) : ListWidgetProvider<TestDashboardState>
{
    public override async Task<ListWidgetData> GetData(TestDashboardState? state)
    {
        IQueryable<TestEntity> entities = playgroundService.Get();

        if (state.DateFrom is not null)
            entities = entities.Where(p => p.DateTimeValue == null || p.DateTimeValue >= state.DateFrom);

        if (state.DateTo is not null)
            entities = entities.Where(p => p.DateTimeValue == null || p.DateTimeValue <= state.DateTo);        

        return new ListWidgetData()
        {
            Items = [.. entities.ToList().Select(p => new ListWidgetItemData()
            {
                Title = p.Name,
                Description = new ParametersMessage {
                    Key= "{0}: {1}",
                    Params ={
                        "Price",
                        p.Price != null ? p.Price.ToString()! : "N/A"
                    }
                },
                Badge = p.Active ? "Active" : "Inactive",
                BadgeColor = p.Active ? WidgetBadgeVariant.Success : WidgetBadgeVariant.Danger,
                Image = p.ImageUrl.Value,
                Url = p.Url
            })]
        };
    }
}
```

And later, when adding the widget, this provider is used in its constructor by passing the provider's type:

=== "ASP.NET"

```csharp
row.AddWidgetToRow(new ListWidget(typeof(TestListWidget), "Test list widget") 
{ 
    ShowImages = true,
    UpdateTimeInSeconds = 5 
});
```
