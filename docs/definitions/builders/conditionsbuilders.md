# **Conditions builders**

## **Description**

Condition builders are static helper classes used to create condition metadata in a concise and readable way. They are typically used in methods such as [**GetLayoutConditions**](../processes/crudprocess.md#getlayoutconditions).

## **PropertyConditionBuilder**

=== "ASP.NET"

```csharp
public static class PropertyConditionBuilder<TEntity>
```

Builds a condition targeting a property.

There is only one method that has several overloads, the more complete of them being the next one:

=== "ASP.NET"

```csharp
public static void AddPropertyCondition<TPropertyTarget>(List<ConditionRule> list, ConditionType conditionType, ConditionScope conditionScope, Expression<Func<TEntity, TPropertyTarget>> propertyExpression, Expression<Func<TEntity, bool>>? conditionExpression, object? value)
```

The parameters used are as follows:

- `list`: This is the list that will hold all conditions related to its container (normally a [**RootLayout**](../../components/containers/layout/rootlayoutcontainer.md))
- `conditionType`: The type of the condition, selected from the next values:

    |**ConditionType**|**Description**                                                                                           |
    |-----------------|----------------------------------------------------------------------------------------------------------|
    |Disable          |While the condition is valid, the control will be disabled                                                |
    |Visible          |While the condition is valid, the control will be visible                                                 |
    |Required         |While the condition is valid, the control will be required                                                |
    |ChangeValue      |If the condition is valid, the control will change its value to the **value** property sent by this method|

- `conditionScope`: This defines in which mode of the process the condition will be effective. It is used in processes that have different modes, like [**Crud Processes**](../processes/crudprocess.md). the options are as follows:

    |**ConditionScope**|**Description**|
    |------------------|---------------|
    |New               |The condition will only apply in *New* mode. For example, a password will always be required in *New* mode when a new user is created, but it will be optional when modifying. The required condition in that case should be sent with this option|
    |Edit              |The condition will only apply in *Edit* mode. For example, a record may have a required code to be defined when creating it, but that code shouldn't be editable later. In that case, an always disabled condition could be sent with *Edit* scope, which will make that field only available for modifications in *New* mode|
    |All               |The condition works always, this is what normally will be sent in overloads where the scope isn't part of the parameters|

- `propertyExpression`: Points to a property of **TEntity** through a lambda expression. This allows for an easy definition on which property to bind to the condition. If the desired field to apply an expression is inside a detail, then this property expression has to be applied using the **First()** method, to be able to correctly point to the property inside the detail. The **First()** method will be discarded internally, it's just a way to write the expression. for example:

    `p => p.Details.First().NumericValue`

- `conditionExpression`: The expression that needs to happen for the condition to be valid, sent through a lambda expression. Conditions support equals, greater and lower than comparisons, as well as OR and AND logical operators.
- `value`: The value used if a **ChangeValue** condition is applied.

!!! Note
    **Multiple conditions** may target the same property. GreenFire evaluates each condition independently.

Some examples applying different scenarios:

=== "ASP.NET"

```csharp
protected override async Task<List<ConditionRule>> GetLayoutConditions()
{
    List<ConditionRule> layoutConditions = [];

    //Next example disables the Name control in edit mode. So it can only be modified in new mode, and in edit the control works as information, and is immutable.
    PropertyConditionBuilder<TestEntity>.AddPropertyCondition(layoutConditions, ConditionType.Disable, ConditionScope.Edit, p => p.Name);

    //The next three examples are very common when using a checkbox to enable a control. When enabled, it makes it required, when disabled, the control should not be editable, and it should lose its value.
    PropertyConditionBuilder<TestEntity>.AddPropertyCondition(layoutConditions, ConditionType.Required, p => p.Price, p => p.UsePrice == true);
    PropertyConditionBuilder<TestEntity>.AddPropertyCondition(layoutConditions, ConditionType.Disable, p => p.Price, p => p.UsePrice == false);
    PropertyConditionBuilder<TestEntity>.AddPropertyCondition(layoutConditions, ConditionType.ChangeValue, p => p.Price, p => p.UsePrice == false, null);

    //Next example makes the Floor control inside the Addresses detail only visible when the address is an apartment.
    PropertyConditionBuilder<TestEntity>.AddPropertyCondition(layoutConditions, ConditionType.Visible, p => p.Addresses.First().Floor, p => p.Addresses.First().IsApartment == true);

    return layoutConditions;
}
```

## **NodeConditionBuilder**

=== "ASP.NET"

```csharp
public static class NodeConditionBuilder<TEntity>
```

Builds a condition linked to a node.

As with [**PropertyConditionBuilder**](#propertyconditionbuilder), there is only one method that has several overloads, the more complete of them being the next one:

=== "ASP.NET"

```csharp
public static void AddNodeCondition(List<ConditionRule> list, ConditionType conditionType, ConditionScope conditionScope, string logicalKey, Expression<Func<TEntity, bool>>? conditionExpression, object? value)
```

The behavior is the same as [**PropertyConditionBuilder**](#propertyconditionbuilder), with the difference that instead of sending a property expression to link the condition to a property, this time a **logicalKey** is sent, defined by the user when the node is created. With this implementation, it is possible to use conditions in **Containers** and **Content** nodes.

Since content and container nodes do not hold editable values, only **Visible** and, in some cases, **Disable** conditions are generally meaningful. Conditions such as Required or ChangeValue are intended for controls bound to properties.

Some examples applying different scenarios:

=== "ASP.NET"

```csharp
protected override async Task<List<ConditionRule>> GetLayoutConditions()
{
    List<ConditionRule> layoutConditions = [];

    //Next example shows a label with the text "Verify voltage values" when the item is electrical
    NodeConditionBuilder<TestEntity>.AddNodeCondition(layoutConditions, ConditionType.Visible, "LabelInformation", p => p.ItemType == ItemTypes.Electrical);

    //Next example makes a tab named "Sales" only visible in edit mode, since it doesn't make sense in new mode, as the item wouldn't have any sales yet.
    NodeConditionBuilder<TestEntity>.AddNodeCondition(layoutConditions, ConditionType.Visible, ConditionScope.Edit, "SalesTab");

    //Same example makes as before, but disabling a "Tariffs" tab when in new mode.
    NodeConditionBuilder<TestEntity>.AddNodeCondition(layoutConditions, ConditionType.Disable, ConditionScope.New, "Tariffs");

    return layoutConditions;
}
```
