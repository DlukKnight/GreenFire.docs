# **BaseControlComponent**

## **Description**

BaseControlComponent is the internal renderer used by GreenFire to display every control type.

In most scenarios it is not used directly. Instead, it is used by process layouts to render the correct control according to the metadata received from the backend.

Receives the standard GreenFire [**runtime context**](../runtimecontext.md).

Understanding this component is useful because all controls share the same base properties described below.

## Metadata properties

### control

Control specific property that carries the control unique properties. Refer to each control for more information.

## **BaseControl metadata**

This is the basic type for the **control** property that has the common basic properties shared by all controls.

=== "React"

```ts
interface BaseControl extends RenderNode {
  propertyName: string;
  label: string;
  type: ControlType;
  required: boolean;
  value: any;
}
```

### propertyName

The name of the property that the control is bounded to in reference to the entity it is representing.

### label

The text shown in the label that is accompanying the control. For example, the description label above a textbox. If omitted, no text is displayed, but the label area is preserved to maintain vertical alignment with other controls.

### type

The type of the control.

### required

When **true**, the control is treated as mandatory regardless of dynamic conditions. This property takes precedence over runtime condition rules.

### value

Represents the current value displayed by the control. The expected type depends on the specific control implementation (string, number, boolean, Date, collection, etc.).
