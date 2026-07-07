# **Runtime context**

## **Description**

[**RootLayouts**](./containers/layout/rootlayoutcontainer.md) and, by extension, all their children receive a common set of properties as context for operation.

## **Context properties**

### Process context

#### processCode

Identifies the process owning the layout.

#### serviceUrl

The base Url for the service that will be passed to child nodes, so they can correctly retrieve data if needed.

### Layout context

#### rootLayout

The [**RootLayout**](./containers/layout/rootlayoutcontainer.md) definition necessary to render the layout.

#### activeTabs

Collection of active tab components.

#### setActiveTabs

Setter for active tabs, enabling controls to activate a tab if it needs to be focused. Used mostly when errors are validated.

#### nodeStates

Actual states for common properties. Refer to [**NodeStates**](./nodestates/index.md) for more information.

#### controlRefs

Control references used for focus purposes, normally to lead the user to the correct controls when errors appear during validations.

### Form state

#### formData

Data of the form that the layout represents.

#### errors

List of errors that appeared during the layout's validations. If one of those errors is attached to a control, it will trigger an error display badge below the control.

#### onChange

Callback invoked whenever a control value changes. Makes the value impact the form data containing the control.

### Display state

#### readOnly

Toggles the read-only mode on the layout, disabling any change of value on any node that allows input.

#### disabled

When true, the layout is disabled and cannot be edited. It also changes the visual style of all child nodes to reflect it. If this property is set to **true**, it will take precedence over runtime condition rules.

### isLoading

Represents the readiness state of the owning process. If it is **true**, then the process should be not ready still to operate, therefore, all child nodes should be disabled to prevent inconsistent behavior.

### Specialized scenarios

#### selectedRows

Selected rows from a grid from a containing process. Used by some controls to send the selected entity id when fetching their own data.

#### prefix

Used to identify controls contained in another control that has its own form data (like details). RootLayouts contained in processes shouldn't have a prefix, but the ones that are contained in nested nodes, like details, should use it to correctly identify their containing nodes.
