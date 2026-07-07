# **Registries**

## **Description**

While all of the definitions are handled automatically, there may be cases where access to a specific one is needed, for example, accessing a specific Wizard to execute its finish method based on saved parameters. For those cases, the next registry classes are available:

* CheckboxListRegistry
* CrudProcessRegistry
* DashboardRegistry
* DatagridRegistry
* LookupRegistry
* ReadOnlyProcessRegistry
* ReportProcessRegistry
* SelectOptionsProviderRegistry
* WidgetRegistry
* WizardRegistry

Each of this registries can be injected, and they will return any of their contained definitions by using the **Resolve** method, which receives the definition code as parameter.

Example:

=== "ASP.NET"

```csharp
 Result<IWizard> wizard = await wizardRegistry.Resolve("testCodeWizard");
 IWizard? wizardProcess = wizard.Value;

 WizardResultDto result = await wizardProcess.ExecuteFinish(wizardParameters);
```

Some of these registries expose a **GetAll** method that will return all their stored definitions.
