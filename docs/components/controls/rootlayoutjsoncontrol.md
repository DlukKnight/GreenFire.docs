# **RootLayoutJsonControl**

## **Description**

RootLayoutJsonControl allows an entire form to be embedded inside a single control. Instead of binding directly to entity properties, the nested form is serialized into a JSON string, making it ideal for storing dynamic or context-specific configuration

The use of this control is to give an option to operate with dynamic properties that can have different kinds of applications depending on the context.

You can think of this control as a mini-form. It has its own...

* [**RootLayout**](../containers/layout/rootlayoutcontainer.md)
* Conditions
* Validation rules
* Default values

...completely independent from the parent process.

Since the nested layout is a standard RootLayout, it supports the same nodes, controls, conditions and validations as any other GreenFire layout.

This control inherits all properties from **BaseControl**. The metadata below only documents the properties specific to **RootLayoutJsonControl**.

## **RootLayoutJsonControl metadata**

=== "React"

```ts
interface RootLayoutJsonControl extends BaseControl {
  definition: RootLayoutJsonControlDefinition;
};

interface RootLayoutJsonControlDefinition {
  rootLayout: any;
  layoutConditions: any;
  validationRules: any;
  defaultData: any;
}
```

### definition

The complete definition of the control's layout.

## **Definition structure**

### rootLayout

The RootLayout of the control.

### layoutConditions

The conditions for the nodes rendered from the RootLayout.

### validationRules

The validations for the nodes rendered from the RootLayout.

### defaultData

The default data loaded in each property.

## Example

In the following example, the control gets created along with its definition. A **JsonLayoutClass** is declared with the properties needed and then the definition is defined based on that class, with its own RootLayout, a simple test condition, plus default values. This example does not send validations.

=== "ASP.NET"

```csharp
public class JsonLayoutClass
{
    public string Name { get; set; }

    public int? Number { get; set; }
}

protected static async Task<RootLayoutJsonControlDefinition> GetPropertyJsonDefinition()
{
    return new RootLayoutJsonControlDefinition()
    {
        RootLayout = await GetJsonLayoutRootLayout(),
        LayoutConditions = await GetJsonLayoutClassConditions(),
        DefaultData = new JsonLayoutClass()
        {
            Name = "Default name",
            Number = null
        }
    };
}

protected static async Task<RootLayout> GetJsonLayoutRootLayout()
{
    RootLayoutBuilder<JsonLayoutClass> rootLayout = new();

    RowNodeBuilder<JsonLayoutClass> row = rootLayout.AddRow(3);

    row.AddControlToRow(
        p => p.Name,
        new TextControl()
        { 
            Required = true
        });

    row.AddControlToRow(
        p => p.Number, 
        new NumberControl());    

    return rootLayout.Layout;
}

protected static async Task<List<ConditionRule>> GetJsonLayoutClassConditions()
{
    List<ConditionRule> layoutConditions = [];

    PropertyConditionBuilder<JsonLayoutClass>.AddPropertyCondition(layoutConditions, ConditionType.ChangeValue, p => p.Number, p => p.Name == "Test", 5);

    return layoutConditions;
}

protected override async Task<RootLayout> GetRootLayout()
{
    ...

    row.AddControlToRow(
        e => e.PropertyJson, 
        new RootLayoutJsonControl() 
        { 
            Definition = await GetPropertyJsonDefinition() 
        });

    ...
}
```

An expected response for this example would be as follows:

=== "Json"

```json
{"name":"Test","number":5}
```
