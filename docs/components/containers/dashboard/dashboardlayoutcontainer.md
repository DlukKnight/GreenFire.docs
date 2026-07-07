# **DashboardRootLayoutComponent**

## **Description**

Represents a collection of [**widgets**](../../widgets/index.md) meant to be displayed by a [**Dashboard**](../../processes/dashboard.md) component.

DashboardLayouts are similar to [**RootLayouts**](../layout/rootlayoutcontainer.md), but meant to be used exclusively with dashboard related components.

Receives the standard GreenFire [**dashboard runtime context**](../../dashboardruntimecontext.md).

This control doesn't inherit from **DashboardContainerNode** and instead has its own metadata type: **DashboardLayout**.

## **DashboardLayout metadata**

=== "React"

```ts
interface DashboardLayout {
  children: DashboardContainerNode[];
};
```

### children

The collection of nodes that the layout owns.

## Example

=== "ASP.NET"

```csharp
DashboardLayout layout = new();
```
