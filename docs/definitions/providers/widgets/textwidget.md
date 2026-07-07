# **Text widget provider**

## **Description**

Delivers the data that a [**TextWidget**](../../../components/widgets/textwidget.md) will show.

## **Definition**

=== "ASP.NET"

```csharp
public abstract class TextWidgetProvider<TState> : BaseDashboardWidgetProvider<TextWidgetData, TState>
```

Text widget providers are very simple to define, by inheriting from the **TextWidgetProvider** abstract class, defining the generic **TState** parameter to reflect the class used for the filters in the containing dashboard and decorating the provider with the **DashboardWidgetCode** attribute. This code is unique and there should be only one among all the widgets, trying to register a second one with the same code will end in an API validation failure.

Basic example:

=== "ASP.NET"

```csharp
[DashboardWidgetCode(nameof(TestTextWidget))]
public class TestTextWidget : TextWidgetProvider<TestDashboardState>
```

After this setup is satisfied, we are ready to start implementing the different methods that configure the definition.

## **Methods**

### GetData

=== "ASP.NET"

```csharp
public abstract Task<TData> GetData(TState? state);
```

Returns the data that the control will display, in this case, **TData** being the **TextWidgetData** class. The **state** parameter will carry the filters selected values to correctly filter the data if needed.

The **TextWidgetData** class is defined as follows:

=== "ASP.NET"

```csharp
public class TextWidgetData : IWidgetData
{
    public required string Text { get; set; }
    public List<TextWidgetParameter> Parameters { get; set; } = [];
}

public class TextWidgetParameter
{
    public string? Type { get; set; } = null;
    public object? Value { get; set; }
}
```

The use of this class is to pass in **Text** a message using placeholders, like this example:

`Name is {0} and value is {1}`

And then define the **Parameters** in the order that the placeholders should be replaced by them, setting their **Type** so it can be correctly parsed, along their **Value**.

Example:

=== "ASP.NET"

```csharp
[DashboardWidgetCode(nameof(TestTextWidget))]
public class TestTextWidget : TextWidgetProvider<TestDashboardState>
{
    public override async Task<TextWidgetData> GetData(TestDashboardState? state)
    {
        return new TextWidgetData()
        {
            Text = "Selected filters date range: {0} - {1}",
            Parameters =
            [
                new TextWidgetParameter() { Type = nameof(DateTime), Value = state?.DateFrom },
                new TextWidgetParameter() { Type = nameof(DateTime), Value = state?.DateTo }
            ]
        };
    }
}
```

And later, when adding the widget, this provider is used in its constructor by passing the provider's type:

=== "ASP.NET"

```csharp
row.AddWidgetToRow(new TextWidget(typeof(TestTextWidget)) 
{ 
    UpdateTimeInSeconds = 5 
});
```
