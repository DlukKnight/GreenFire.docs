# **ColumnDefinition**

## **Description**

The **ColumnDefinition** is the standard way to define all column's properties for grids. Since every grid needs to define its columns, this concept will appear in every grid implementation and in every grid component.

**ColumnDefinitions** are most of the time created in [**GetGridColumns()**](../processes/crudprocess.md#getgridcolumns) methods.

## **Definition**

=== "ASP.NET"

```csharp
public class ColumnDefinition(string field, string headerName, bool visible = true)
```

Each **ColumnDefinition** in a list represents one column in the grid. For the grid to behave correctly, the **field** parameter should be correctly sent, being the name of the property whose value should be displayed. Since GreenFire encourages working with entities, the **field** parameter will normally be the name of a property inside an entity, so we encourage the use of **nameof** to correctly send the name.

!!! Note
    If the column sent belongs to a navigation property, the correct way to send it is with the format **navigation.property**. For example, if I have an **Location** navigation property and I wand to display the **Address** property, I should send `Location.Address` as the **field** parameter.

The **headerName** parameter will be the title of the column shown in the column header.

The **visible** parameter specifies if the column is visible by default. If set to **false**, the column won't be displayed at the first loading of the grid, but can be selected by the user to be shown from the grid's toolbar column options.

There are also properties outside of the constructor that can be overridden to get different results:

|**Property**|**Description**|
|------------|---------------|
|DataType    |Defines what type of data should the column format|
|AutoAdjust  |Defines if the column should take available space to account for its width. If disabled, it will take a fixed minimal space unless **MinWidth** is sent|
|MinWidth    |Sets the minimal width of the column. If **AutoAdjust** is sent as **true**, it will adjust width above this value. If it is sent as **false**, this will override the fixed value the column normally takes |
|Options     |This property is meant to be sent when **DataType** is set to **Select**. If that is the case, the column will try to match the values it needs to show with the options sent in this property. If working with **enums**, refer to [**SelectOptionsHelper**](../builders/selectoptionsbuilders.md) for ways to construct this options|

As for **ColumnDataType**, the available members are:

|**Member**|**Description**|
|----------|---------------|
|Text      |The values will be formatted as **string**|
|Number    |The values will be formatted as **decimal**|
|Date      |The values will be formatted as **date**. If a Date value is sent, and the column has not set this, the string value of the Date will be shown|
|DateTime  |The values will be formatted as **datetime**. If a DateTime value is sent, and the column has not set this, the string value of the DateTime will be shown|
|Boolean   |The values will be formatted with a visual icon, a green check for true and a red X for false|
|Select    |The values will be matched and shown with the options sent in the **Options** property|

!!! Note
    Normally, the **Text** and **Number** data-types are not needed for proper value display. We only recommend sending the types if needed.

## **Example definition**

=== "ASP.NET"

```csharp
protected override List<ColumnDefinition>? GetGridColumns()
{
    List<ColumnDefinition> columns =
    [
        new(nameof(TestEntity.Name), "Name"),
        new(nameof(TestEntity.Price), "Price"),
        new(nameof(TestEntity.DateTime), "Full date")
        {
            DataType = ColumnDataType.DateTime
        },
        new(nameof(TestEntity.Date), "Date", false)
        {
            DataType = ColumnDataType.Date
        },
        new(nameof(TestEntity.Material), $"Material")
        {
            DataType = ColumnDataType.Select,
            Options = SelectOptionsHelper.FromEnum<MaterialsEnum>()
        },
        new($"{nameof(TestEntity.Lookup)}.{nameof(TestEntity.Lookup.Name)}", "Lookup", false)
    ];

    return columns;
}
```
