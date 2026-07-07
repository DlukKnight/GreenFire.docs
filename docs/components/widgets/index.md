# **Overview**

## **Description**

Widgets are components meant to display informative data that can be tracked and updated over periods of time.

They have their own frequency which they use to fetch the needed data that they deploy, and they work in conjunction with filters.

Widgets belong to the [**Dashboard**](../processes/dashboard.md) implementation and work with dashboard containers.

The backends owns the implementation for the widget's data retrieval, identified by a code.

## **Component properties**

### Process context

#### serviceUrl

The base Url for the service that will be used to fetch data for the widget.

#### dashboardState

Filters defined in the [**Dashboard**](../../definitions/processes/dashboard.md), sent to the server to be used in the dataset creation.

### Display state

#### disable

While the widgets do not have conditions, this property is used to disable the widgets when the filters are shown, to prevent interaction while the filters are being set.

### Metadata

#### widget

Widget specific property that carries the widget unique properties. Refer to each widget for more information.

## **BaseWidget metadata**

This is the basic type for the **widget** property that has the common basic properties shared by all widgets.

=== "React"

```ts
interface BaseWidget extends DashboardNode {
  widgetType: WidgetType;
  code: string;
  width: number;
  height: number;
  updateTimeInSeconds: number;
}
```

### widgetType

The type of the widget.

### code

Identifies the definition for the data provider in the backend.

### width

The width of the widget. The default changes depending on the widget.

### height

The height of the widget. The default changes depending on the widget.

### updateTimeInSeconds

Frequency of data retrieval in seconds.
