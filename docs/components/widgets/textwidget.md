# **TextWidgetComponent**

## **Description**

Displays a block of text inside a dashboard widget.

It is intended for informational content that doesn't fit naturally into a metric or chart. Typical uses include maintenance reminders, operational notices, warnings, or any contextual information that should always be visible to the user.

Text widgets have their own definition in the backend through a [**Text Widget Provider**](../../definitions/providers/widgets/textwidget.md), which can be identified by their **code**.

This widget inherits all properties from **BaseWidget**. It does not receive any special metadata from **TextWidget**.

## Example

=== "ASP.NET"

```csharp
row.AddWidgetToRow(
  new TextWidget(typeof(InfoTextWidget)) 
  {
       UpdateTimeInSeconds = 5
  });
```
