# **Dashboard runtime context**

## **Description**

[**DashboardLayouts**](./containers/dashboard/dashboardlayoutcontainer.md) have their own runtime context, being simpler than the regular runtime context, since they don't use more complex scenarios, like validations.

## **Context properties**

### Dashboard context

#### dashboardCode

Identifies the [**Dashboard**](../definitions/processes/dashboard.md) owning the layout.

#### serviceUrl

The base Url for the service that will be passed to child nodes, so they can correctly retrieve data if needed.

### Layout context

#### dashboardLayout

The [**DashboardLayout**](./containers/dashboard/dashboardlayoutcontainer.md) definition necessary to render the layout.

### dashboardState

The [**Dashboard**](../definitions/processes/dashboard.md) filters form data.

<!-- ### activeTabs
### setActiveTabs -->

### Dashboard display state

#### disabled

When true, the layout is disabled and cannot be interacted. It also changes the visual style of all child nodes to reflect it.

### isLoading

Represents the readiness state of the owning [**Dashboard**](../definitions/processes/dashboard.md). If it is **true**, then the [**Dashboard**](../definitions/processes/dashboard.md) should be not ready still to operate, therefore, all child nodes should be disabled to prevent inconsistent behavior.
