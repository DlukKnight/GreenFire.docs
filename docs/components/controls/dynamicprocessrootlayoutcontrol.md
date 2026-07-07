# **DynamicProcessRootLayout**

## **Description**

Renders a control composed of a button that triggers the collection of execution values for [**Wizard**](../processes/wizard.md) or [**Report**](../processes/reportprocess.md) processes. It is meant to open a dialog that allows the user to configure the execution parameters.

This control exists to solve the problem of wanting to execute a process (Wizard or Report) directly in the backend. The use of this control is to collect the parameters used for the execution methods of those processes. This solves situations like [**scheduler executions**](../../definitions/integrations/scheduledtasks.md) where for example a [**Wizard**](../processes/wizard.md) may be programed to be executed every day.

The recommended approach is to bind this control to a property that stores a JSON string. The JSON contains the execution parameters selected by the user and can later be sent directly to the backend when executing the target process.

At runtime, the control reads the process type and process code from the properties specified by **processTypePropertyName** and **processCodePropertyName**. Those values determine which process definition will be loaded when the parameter dialog is opened.

The way the parameters are collected will be defined in the [**showProcessParameters**](../../configuration/frontend/dialogs.md#showprocessparameters) configurable method.

This control inherits all properties from **BaseControl**. The metadata below only documents the properties specific to **DynamicProcessRootLayoutControl**.

## **DynamicProcessRootLayoutControl metadata**

=== "React"

```ts
interface DynamicProcessRootLayoutControl extends BaseControl {
  processTypePropertyName: string;
  processCodePropertyName: string;
}
```

### processTypePropertyName

Identifies the property that will deliver the type of the process. It is meant to be used with a [**Select Control**](selectcontrol.md).

### processCodePropertyName

Identifies the property that will deliver the code of the process. It is meant to be used with a [**Select Control**](selectcontrol.md).

## Example

In the following example, the control stores the selected parameters in ExecutionParametersJson while obtaining the process type and process code from the ProcessType and ProcessCode properties.

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.ExecutionParametersJson, 
    new DynamicProcessRootLayoutControl("ProcessType", "ProcessCode") 
    { 
      Required = true 
    });
```
