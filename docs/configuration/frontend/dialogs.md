# **Dialogs**

## **Description**

The dialogs configuration allows for implementation of any type of dialog, from a simple alert or window.confirm to something more elaborated, like context defined dialogs. GreenFire provides [**parameters**](dialogsParameters.md) to send to the dialogs, as well as a result type to handle confirmations.

## Methods

### showAlertPopup

Shown when information needs to be displayed to the user.

=== "React"

```ts
    showAlertPopup: async (parameters: AlertPopupParameters) => {
        alert(parameters.text);
    }
```

### showConfirmationPopup

Shown when confirmation input is needed from the user.

=== "React"

```ts
    showConfirmationPopup: async (parameters: ConfirmationPopupParameters) => {
        const userConfirmed = window.confirm(parameters.text);
        const result: ConfirmationPopupResult = {
            id: "1",
            type: userConfirmed ? ConfirmationPopupResultType.accepted : ConfirmationPopupResultType.canceled
        }

        return result;
    }
```

### showProcessParameters

Complex popup meant to be used with [**DynamicProcessRootLayoutComponent**](../../components/controls/dynamicprocessrootlayoutcontrol.md). This control should return a Json value representing a process execution data. ExecutionParameters can be used to store relevant data.

=== "React"

```ts
    showProcessExecutionParametersDialog: async (parameters: ExecutionParametersRequest, language: any, translate: any) => {
        switch (parameters.processType) {
            case ProcessType.Report:
                const reportParametersModalParams: ReportParametersPopupParameters = {
                    id: "parameters",
                    processCode: parameters.processCode,
                    processCodePropertyName: parameters.processCodePropertyName,
                    formData: parameters.formData,
                    propertyValue: parameters.propertyValue,
                    propertyName: parameters.propertyName,
                    onChange: parameters.onChange,
                    nodeState: parameters.nodeState,
                    serviceUrl: parameters.serviceUrl,
                    acceptButtonText: translate("Save"),
                    cancelButtonText: translate("Cancel"),
                }

                const showReportParametersPopup = getReportPopup();
                const resultReportPopup = await showReportParametersPopup(reportParametersModalParams);

                if (resultReportPopup.type == ReportParametersPopupResultType.accepted) {
                    const timezone = Intl.DateTimeFormat().resolvedOptions().timeZone;

                    const parametersValue: ExecutionParameters = {
                        parametersJson: resultReportPopup.formData,
                        language: language,
                        timezone: timezone,
                        reportOutputType: resultReportPopup.reportSelectedOutputType
                    }

                    parameters.onChange(parameters.propertyName, JSON.stringify(parametersValue));
                }

                break;
            case ProcessType.Wizard:
                const wizardParametersModalParams: WizardParametersPopupParameters = {
                    id: "parameters",
                    processCode: parameters.processCode,
                    processCodePropertyName: parameters.processCodePropertyName,
                    formData: parameters.formData,
                    propertyValue: parameters.propertyValue,
                    propertyName: parameters.propertyName,
                    onChange: parameters.onChange,
                    nodeState: parameters.nodeState,
                    serviceUrl: parameters.serviceUrl,
                    cancelButtonText: translate("Cancel"),
                }

                const showWizardParametersPopup = getWizardPopup();
                const resultWizardPopup = await showWizardParametersPopup(wizardParametersModalParams);

                if (resultWizardPopup.type == WizardParametersPopupResultType.accepted) {
                    const timezone = Intl.DateTimeFormat().resolvedOptions().timeZone;

                    const parametersValue: ExecutionParameters = {
                        parametersJson: resultWizardPopup.formData,
                        language: language,
                        timezone: timezone
                    }

                    parameters.onChange(parameters.propertyName, JSON.stringify(parametersValue));
                }

                break;
            default:
                break;
        }
    }
```

### image

Shown when previewing an image.

=== "React"

```ts
image: async (title: string, imagePath: string) => {
    const confirmationModalParams: ImagePreviewPopupParameters = {
        title: title,
        imagePath: imagePath
    }

    await showImagePreviewPopup(confirmationModalParams);
}
```
