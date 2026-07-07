# **TabNodeComponent**

## **Description**

Renders a tab which will display its child nodes if active.

Tabs cannot be used by themselves, they always have to be grouped in a [**Tabs Container**](tabscontainer.md).

Similar to controls, tabs also can have conditions, in concrete visibility conditions, which will allow them to be shown under controlled circumstances. To achieve this, all contents need to initialize a **LogicalKey**, which will be used by conditions to correctly point to the correct tab.

This container inherits all properties from **ContainerNode**. The metadata below only documents the properties specific to **TabNode**.

## **TabNode metadata**

=== "React"

```ts
interface TabNode extends ContainerNode {
  title: string;
  hasBorder: boolean;
};
```

### title

The title of the tab, displayed through its label.

### hasBorder

If set to **true**, the tab will render with a border around its children. This is useful for visual purposes, since tabs inside other tabs or containers can be hard to tell apart from other groups. By activating this property, the edges of the tab will be marked and it will be clear where the tab's domain extends.

## Example

=== "ASP.NET"

```csharp
TabNodeBuilder<MyEntity> generalTab = generalTabsContainer.AddTab("General", "GeneralTabLogicalKey");
```
