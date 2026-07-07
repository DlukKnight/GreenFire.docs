# **Localization**

## **Description**

By default, GreenFire provides English language for all terms, but these can be completely overridden. There is also support for custom translations.

=== "React"

```ts
export interface GreenFireLocalization {
    language: string;
    translate: (value: string, params?: any[]) => string;
    commonTerms: GreenFireLocalizationCommonTerms;
}
```

### **language**

This property will impact the datagrid language. As of now, GreenFire uses MUI datagrid for all grid related locale and the languages supported are both English and Spanish, which can be activated by sending **en** and **es** respectively.

### **translate**

This method will be called in any place where text and values are used. This means that it is possible to use this method to enforce a custom translation system, with proper dictionaries and terms, by combining this with the **commonTerms** property. These are a series of examples that could be used to translate in many ways:

=== "React"

```ts
const translate = (key: string) => {
    if (!Number.isNaN(Number(key)))
        return key;

    return dictionaries[language]?.[key] ?? key
};

const format = (key: string, params: any[]) => {
    const template = translate(key);

    let result = template;

    params.forEach((param, index) => {
        result = result.replace(
            `{${index}}`,
            translateTermOrValue(param)
        );
    });

    return result;
};

const translateValue = (value: any) => {
    if (!Number.isNaN(Number(value)))
        return value;

    if (!value?.startsWith("@@"))
        return value;

    const split = value.split("|")
    return format(split[0], split.slice(1));
};

const translateTermOrValue = (key: any) => {
    if (typeof key !== "string")
        return key;

    if (!key?.startsWith("@@"))
        return translate(key);

    return translateValue(key);
};
```

This covers several forms of translation, from using parameters to replace placeholders, to using prefixes with the value sent via separator. In this example, the **translateTermOrValue** will cover all the cases by calling the other methods, so by sending that method, GreenFire will use this custom localization mini-engine in all places. Keep in mind that, by default, the translate method included in GreenFire is going to replace placeholders with the format **{index}** with each value of the parameters sent, so a message that says "{0} is greater than {1}" sent with parameters 2 and 1 will result in "2 is greater than 1".

### **commonTerms**

This dictionary-like property contains all the static terms used inside GreenFire. By replacing them, they will reflect immediately in the system. The default terms are as follows:

=== "React"

```ts
crudProcessCreateModalTitle: "Create",
crudProcessUpdateModalTitle: "Update",
crudProcessDeleteModalTitle: "Delete",
crudProcessCancelModalTitle: "Cancel",
crudProcessCreateModalMessage: "Do you want to create the record?",
crudProcessUpdateModalMessage: "Do you want to modify the record?",
crudProcessDeleteModalMessage: "Do you want to delete the record?",
crudProcessCancelModalMessage: "You have pending changes. Do you want to exit anyway?",
wizardResultMessage: "Result",
wizardResultSuccess: "Success",
wizardResultFail: "Fail",
modalRestoreDefaultValuesTitle: "Default values",
modalRestoreDefaultValuesMessage: "Do you want to restore default values?",
buttonsNew: "New",
buttonsEdit: "Edit",
buttonsDuplicate: "Duplicate",
buttonsDelete: "Delete",
buttonsConfirm: "Confirm",
buttonsSave: "Save",
buttonsCancel: "Cancel",
buttonsExit: "Exit",
buttonsBack: "Back",
buttonsNext: "Next",
buttonsFinish: "Finish",
buttonsReturn: "Return",
buttonsFilters: "Filters",
buttonsApply: "Apply",
buttonsRestore: "Restore",
buttonsGenerate: "Generate",
buttonsView: "View",
gridExportPrint: "Print",
gridExportCsv: "Export CSV",
gridSearch: "Search",
dateOpenTimeSelector: "Open time selector",
dateOpenTimeSelectorNow: "Now",
dateOpenTimeSelectorClean: "Clean",
detailAddButton: "Add",
dynamicProcessRootLayoutParameters: "Set parameters",
dynamicProcessRootLayoutParametersSet: "Parameters are set",
dynamicProcessRootLayoutParametersNotSet: "Missing parameters",
imagePreviewModalTitle: "Preview image",
imageBrowse: "Browse",
imageFileNotSelected: "No image selected",
lookupNoneOption: "None",
lookupNoValues: "No values",
selectNoneOption: "None",
selectNoValues: "No values",
conditionsRequired: "Required",
conditionsRequiredPlaceholder: "Required",
conditionsRequiredAtLeastOneValue: "At least one value should be selected",
conditionsRequiredParameters: "Parameters are required",
conditionsTextMinLengthInvalid: "Length must be greater than {0}",
conditionsTextMaxLengthInvalid: "Length must be less than {0}",
conditionsEmailInvalid: "Invalid email",
conditionsUrlInvalid: "Invalid url",
conditionsNumberInvalid: "Invalid number",
conditionsNumberLesserThan: "Must be lesser than {0}",
conditionsNumberGreaterThan: "Must be greater than {0}",
conditionsDatetimeIncomplete: "Incomplete date",
conditionsDatetimeInvalid: "Invalid date",
conditionsPasswordNoMatch: "Passwords do not match",
conditionsImageOversize: "Image over {0} file size",
conditionsImageOverwidth: "Image over {0} width",
conditionsImageOverheight: "Image over {0} height"
```

If you just don't care for localization in more than one language, then you could replace each one of these for the terms you desire. However, if you do want to have a multi-language approach, by combining these properties with the **translate** method, it is very easy to get multiple dictionaries to work with. We recommend an approach in which each term uses a recognizable format, like this one:

=== "React"

```ts
crudProcessCreateModalTitle: "CrudProcess.CreateModal.Title",
crudProcessUpdateModalTitle: "CrudProcess.UpdateModal.Title",
crudProcessDeleteModalTitle: "CrudProcess.DeleteModal.Title",
crudProcessCancelModalTitle: "CrudProcess.CancelModal.Title",
crudProcessCreateModalMessage: "CrudProcess.CreateModal.Message",
crudProcessUpdateModalMessage: "CrudProcess.UpdateModal.Message",
crudProcessDeleteModalMessage: "CrudProcess.DeleteModal.Message",
crudProcessCancelModalMessage: "CrudProcess.CancelModal.Message",
wizardResultMessage: "Info.Wizard.ResultMessage",
wizardResultSuccess: "Info.Wizard.ResultSuccess",
wizardResultFail: "Info.Wizard.ResultFail",
modalRestoreDefaultValuesTitle: "RestoreDefaultModal.Title",
modalRestoreDefaultValuesMessage: "RestoreDefaultModal.Message",
buttonsNew: "Buttons.New",
buttonsEdit: "Buttons.Edit",
buttonsDuplicate: "Buttons.Duplicate",
buttonsDelete: "Buttons.Delete",
buttonsConfirm: "Buttons.Confirm",
buttonsSave: "Buttons.Save",
buttonsCancel: "Buttons.Cancel",
buttonsExit: "Buttons.Exit",
buttonsBack: "Buttons.Back",
buttonsNext: "Buttons.Next",
buttonsFinish: "Buttons.Finish",
buttonsReturn: "Buttons.Return",
buttonsFilters: "Buttons.Filters",
buttonsApply: "Buttons.Apply",
buttonsRestore: "Buttons.Restore",
buttonsGenerate: "Buttons.Generate",
buttonsView: "Buttons.View",
gridExportPrint: "System.Grid.ExportPrint",
gridExportCsv: "System.Grid.ExportCsv",
gridSearch: "System.Grid.Search",
dateOpenTimeSelector: "System.DateTimeControls.OpenTimeSelector",
dateOpenTimeSelectorNow: "System.DateTimeControls.TimeSelectorNow",
dateOpenTimeSelectorClean: "System.DateTimeControls.TimeSelectorClean",
detailAddButton: "Buttons.Add",
dynamicProcessRootLayoutParameters: "System.DynamicRootLayout.SetParameters",
dynamicProcessRootLayoutParametersSet: "System.DynamicRootLayout.ParametersAreSet",
dynamicProcessRootLayoutParametersNotSet: "System.DynamicRootLayout.ParametersAreNotSet",
imagePreviewModalTitle: "PreviewModal.Title",
imageBrowse: "Buttons.Browse",
imageFileNotSelected: "System.File.NoSelected",
lookupNoneOption: "System.SelectControls.NoneOption",
lookupNoValues: "System.SelectControls.NoValues",
selectNoneOption: "System.SelectControls.NoneOption",
selectNoValues: "System.SelectControls.NoValues",
conditionsRequired: "Conditions.Required.Message",
conditionsRequiredPlaceholder: "Conditions.Required.Placeholder",
conditionsRequiredAtLeastOneValue: "Conditions.Required.AtLeastOneValue",
conditionsRequiredParameters: "Conditions.Required.Parameters",
conditionsTextMinLengthInvalid: "Conditions.Text.MinLength",
conditionsTextMaxLengthInvalid: "Conditions.Text.MaxLength",
conditionsEmailInvalid: "Conditions.Email.Invalid",
conditionsUrlInvalid: "Conditions.Url.Invalid",
conditionsNumberInvalid: "Conditions.Number.Invalid",
conditionsNumberLesserThan: "Conditions.Number.LesserThan",
conditionsNumberGreaterThan: "Conditions.Number.GreaterThan",
conditionsDatetimeIncomplete: "Conditions.DateTime.Incomplete",
conditionsDatetimeInvalid: "Conditions.DateTime.Invalid",
conditionsPasswordNoMatch: "Conditions.Password.NoMatch",
conditionsImageOversize: "Conditions.Image.Oversize",
conditionsImageOverwidth: "Conditions.Image.Overwidth",
conditionsImageOverheight: "Conditions.Image.Overheight"
```

Then your translate implementation can replace these terms against a dictionary, and even translate similar terms sent by the backend, like in validations.
