# **Layout builders**

## **Description**

Builders are intended to be short-lived objects. They are typically instantiated inside definition methods (such as [**GetRootLayout**](../processes/crudprocess.md#getrootlayout)) and discarded once the resulting layout has been returned.

## **RootLayoutBuilder**

=== "ASP.NET"

```csharp
public class RootLayoutBuilder<TEntity>
```

Builds anything related to [**RootLayouts**](../../components/containers/layout/rootlayoutcontainer.md)

For using it, just create a new instance:

=== "ASP.NET"

```csharp
RootLayoutBuilder<TestEntity> rootLayoutBuilder = new();
```

### **Properties**

#### Layout

=== "ASP.NET"

```csharp
public RootLayout Layout { get; } = new();
```

This property will return the built instance of the [**RootLayout**](../../components/containers/layout/rootlayoutcontainer.md). This is what you should use once all the building is done.

For example, in the [**GetRootLayout**](../processes/crudprocess.md#getrootlayout) method of [**Crud processes**](../processes/crudprocess.md), you should return the layout:

=== "ASP.NET"

```csharp
RootLayout layout = rootLayoutBuilder.Layout;

return layout;
```

### **Methods**

#### AddRow

=== "ASP.NET"

```csharp
public RowNodeBuilder<TEntity> AddRow(int? columnAmount = null)
```

Adds a [**RowContainer**](../../components/containers/layout/rownodecontainer.md) to the layout and returns a [**RowNodeBuilder**](#rownodebuilder) to start building on the row.

The **columnAmount** parameter is optional, as the row will carry a columns amount equal to the nodes defined in it, but it is useful to enforce a certain number of columns for space purposes. Like, for example, defining three columns in a row that will only have a single control to prevent said control to stretch to much and be visually unpleasant.

=== "ASP.NET"

```csharp
RowNodeBuilder<TestEntity> row = rootLayoutBuilder.AddRow(2);
```

#### AddTabs

=== "ASP.NET"

```csharp
public TabsContainerBuilder<TEntity> AddTabs(bool hasBorder = false)
```

Adds a [**TabsContainer**](../../components/containers/layout/tabscontainer.md) to the layout and returns a [**TabsContainerBuilder**](#tabscontainerbuilder) to start building on the tabs container.

The **hasBorder** parameter will define if the container should have a border to visually show the limits of the control. Useful for situations where the limits of the container are diffuse because it looks like they mix with other controls outside the tabs container.

=== "ASP.NET"

```csharp
TabsContainerBuilder<TestEntity> generalTabsContainer = rootLayout.AddTabs();
```

## **RowNodeBuilder**

Builds a [**RowContainer**](../../components/containers/layout/rownodecontainer.md) node.

!!! Note
    This builder should never be instantiated by itself, and instead use it from an **AddRow** method from a container.

### **Methods**

#### AddControlToRow

=== "ASP.NET"

```csharp
public void AddControlToRow<TProperty>(Expression<Func<TEntity, TProperty>> propertyExpression, BaseControl control)
```

This method adds a new control to the row, binding it to a property of **TEntity** defined with the **propertyExpression** through a lambda expression. This makes it really easy to define new controls, by just creating the expression and then passing a new instance of the desired control as the **control** parameter.

The next example is a simple control managing a price, which will always have to have a value and a minimum value of 1:

=== "ASP.NET"

```csharp
row.AddControlToRow(e => e.Price, new NumberControl() { Required = true, Min = 1 });
```

#### AddContentToRow

=== "ASP.NET"

```csharp
public void AddContentToRow(ContentNode content)
```

Adds a [**ContentNode**](../../components/contents/index.md). to the row. Contents are simpler than controls, as they are not linked to any property.

The next example is a simple content showing a warning:

=== "ASP.NET"

```csharp
row.AddContentToRow(new LabelContent() { LogicalKey = "AvailabilityLabel", Text = "<b>Warning</b>: Remember to check for availability" });
```

## **TabsContainerBuilder**

Builds a [**TabsContainer**](../../components/containers/layout/tabscontainer.md) node.

!!! Note
    This builder should never be instantiated by itself, and instead use it from a **AddTabs** method from a container.

### **Methods**

#### AddTab

=== "ASP.NET"

```csharp
public TabNodeBuilder<TEntity> AddTab(string title, string? logicalKey)
```

Adds a [**TabNode**](../../components/containers/layout/tabcontainer.md) to the layout and returns a [**TabNodeBuilder**](#tabscontainerbuilder) to start building on the tab node.

The **title** represent the label shown on the tab, while the optional **logicalKey** can be used to define conditions on the tab.

=== "ASP.NET"

```csharp
TabNodeBuilder<TestEntity> generalTab = tabsContainer.AddTab("General", "GeneralTabKey");
```

## **TabNodeBuilder**

Builds a [**TabNode**](../../components/containers/layout/tabcontainer.md) node.

!!! Note
    This builder should never be instantiated by itself, and instead use it from a **AddTab** method from a container.

### **Methods**

#### AddRow

=== "ASP.NET"

```csharp
public RowNodeBuilder<TEntity> AddRow(int? columnAmount = null)
```

Method equivalent to the [**AddRow**](#addrow) of [**RootLayoutBuilder**](#rootlayoutbuilder), but adding the row to the tab in this case.

#### AddTabs

=== "ASP.NET"

```csharp
public TabsContainerBuilder<TEntity> AddTabs(bool hasBorder = false)
```

Method equivalent to the [**AddTabs**](#addtabs) of [**RootLayoutBuilder**](#rootlayoutbuilder), but adding the tabs container to the tab in this case.

## **DetailControlBuilder**

```csharp
public class DetailControlBuilder<TDetail>
```

Builds a [**DetailControl**](../../components/controls/detailcontrol.md) node.

It is very similar to a [**RootLayoutBuilder**](#rootlayoutbuilder), but instead of using the **TEntity** as generic parameter, it uses a **TDetail** representing the entity associated with the detail.

For using it, just create a new instance:

=== "ASP.NET"

```csharp
DetailControlBuilder<TestDetail> detailBuilder = new();
```

### **Properties**

#### Detail

=== "ASP.NET"

```csharp
public DetailControl Detail { get; }
```

This property will return the built instance of the [**DetailControl**](../../components/controls/detailcontrol.md). This is what you should use once all the building is done.

For example, after building the control, we add it to the **RootLayout** pointing to the correct property:

=== "ASP.NET"

```csharp
row.AddControlToRow(e => e.Details, detailBuilder.Detail);
```

### **Methods**

#### AddRow

=== "ASP.NET"

```csharp
public RowNodeBuilder<TDetail> AddRow(int? columnAmount = null)
```

Method equivalent to the [**AddRow**](#addrow) of [**RootLayoutBuilder**](#rootlayoutbuilder), but adding the row to the detail in this case.

### **Detail example**

Details can feel different, but they are basically a **RootLayout** inside a **RootLayout**. Here is an example of a detail added to a **RootLayout** with a few controls defined:

=== "ASP.NET"

```csharp
DetailControlBuilder<TestDetail> detailBuilder = new();

RowNodeBuilder<TestDetail> detailRow = detailBuilder.AddRow();
detailRow.AddControlToRow(d => d.Name, new TextControl { Required = true }); //Notice that d in the expression is the detail entity
detailRow.AddControlToRow(d => d.NumericValue, new NumberControl());
detailRow.AddControlToRow(d => d.DetailCheckbox, new CheckboxControl { NodeLayoutMode = NodeLayoutMode.Aligned });

row5.AddControlToRow(e => e.Details, detailBuilder.Detail); //Notice that e in the expression is the process entity
```

## **Example**

The next example provides a short demonstration of a simple layout using a different containers. A three columns design pattern is used for better visual styling:

=== "ASP.NET"

```csharp
RootLayoutBuilder<Product> layout = new();

RowNodeBuilder<Product> row1 = layout.AddRow(3);

row1.AddControlToRow(p => p.Name, new TextControl() { Required = true });
row1.AddControlToRow(p => p.Price, new NumberControl() { Min = 0 });

TabsContainerBuilder<Product> tabs = layout.AddTabs();

TabNodeBuilder<Product> advanced = tabs.AddTab("Advanced");

RowNodeBuilder<Product> row2 = advanced.AddRow(3);

row2.AddControlToRow(p => p.Enabled, new CheckboxControl());

return layout.Layout;
```
