# **Overview**

## **Description**

Dashboard containers are elements that group nodes specific to [**Dashboards**](../../processes/dashboard.md) to achieve a certain purpose.

Their base container is the [**DashboardLayout**](dashboardlayoutcontainer.md) container, which is the starting point for building the more complex layouts that get used through all the [**Dashboards**](../../../definitions/processes/dashboard.md).

## **DashboardContainerNode metadata**

This is the basic type for the **containerType** property that has the common basic properties shared by all containers.

=== "React"

```ts
interface DashboardContainerNode extends DashboardNode {
  children: DashboardNode[];
  containerType: DashboardContainerType;
};
```

### children

The collection of nodes that the container owns.

### containerType

The type of the container.
