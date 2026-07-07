# **Wizard**

## **Description**

Defines the metadata and behavior required to render and operate a step-by-step interface which executes custom defined operations.

## **Lifecycle**

A Wizard follows this general flow:

1. The process is discovered through the WizardCode attribute.
2. First step is loaded and built.
3. The RootLayout, conditions and validation rules of the step are loaded.
4. The user interacts with the step.
5. Next or previous steps are loaded as needed until last step is confirmed.
6. Finish method is executed, completing the wizard's objective.
7. Wizard result is shown to the user.
8. User can return to the first step and rerun all the flow with a new instance of the wizard.

## **First steps**

Wizards are easy to define, by inheriting from the **Wizard** abstract class and decorating them with the **WizardCode** attribute. This code is unique and there should be only one among all the wizards, trying to register a second one with the same code will end in an API validation failure.

=== "ASP.NET"

```csharp
public abstract class Wizard<TState> : IWizard
```

After those criteria are met, the only missing piece is the definition of the **TState** generic, which should be a class with all the properties that will be used for the Wizard for its final execution. Most of these properties are going to be entered by the user himself while operating through controls, but there could also be properties needed to do internal operations. There are no limitations in what the state can define, although we recommend to keep anything that isn't a control linked property to a minimum for clarity.

Basic example:

=== "ASP.NET"

```csharp
[WizardCode("myTestWizardCode")]
public class TestWizard() : Wizard<TestWizardState>
```

After this setup is satisfied, we are ready to start implementing the different properties and methods that configure the definition, as well as the [**steps**](#step) necessary for the flow.

## **Minimal implementation**

The smallest possible Wizard consists of:

- WizardCode
- [**Steps**](#step)
- [**GetTitle()**](#gettitle)
- [**ExecuteFinish()**](#executefinish)

Everything else is optional.

## **Step**

Wizards are composed of steps, which define the flow that the user will interact with to input the parameters necessary for the final execution. Step are easy to define, being a simple class:

=== "ASP.NET"

```csharp
public abstract class WizardStep<TState>
```

Every **WizardStep** receives the same **TState** instance. Each step may read or modify it before passing it to the next one. By the time [**ExecuteFinish**](#executefinish) is called, **TState** contains the complete execution data gathered across the whole wizard.

Each step has its own definition and methods to navigate in the flow.

When the last step has been completed, the wizard executes its resolution.

### **Minimal step implementation**

The smallest possible Step consists of:

- [**Name**](#name)
- [**GetRootLayout()**](#getrootlayout)

Everything else is optional.

## **Wizard definition**

### **Properties**

#### ImplementsScheduler

=== "ASP.NET"

```csharp
public virtual bool ImplementsScheduler => false;
```

Enables the use of the Wizard with the [**scheduler implementation**](../integrations/scheduledtasks.md). By default, this value is **false**.

#### Steps

=== "ASP.NET"

```csharp
public abstract List<WizardStep<TState>> Steps { get; }
```

A collection of all the steps available for the Wizard's flow. The order they are presented in the List is the order they will be shown, unless the [**GetNextStepIndex**](#getnextstepindex) or the [**GetPreviousStepIndex**](#getpreviousstepindex) methods in steps return different indexes.

### **Methods**

#### Access

##### HasAccess

=== "ASP.NET"

```csharp
public virtual bool HasAccess() => true;
```

Return a boolean value that indicates if access criteria is met when trying to do any operation with the wizard. By default, it always return **true**.

An example to this method could be checking if the logged user has the right permissions to access the wizard.

#### Loading

##### GetDefaultData

=== "ASP.NET"

```csharp
public virtual async Task<TState> GetDefaultData()
{
    TState dto = Activator.CreateInstance<TState>();

    return dto;
}
```

Returns an instance of the **TState** that will be sent when the wizard is built to load the first values. This method can be overridden to enforce custom default values.

In the next example, the default values are set to send two properties with specific values. The rest of the untouched properties will be sent with their default values.

=== "ASP.NET"

```csharp
public override async Task<TestWizardState> GetDefaultData()
{
    TestWizardState defaultState = new()
    {
        MinPrice = 50,
        OperationType = TestOperations.CreateDiscount
    };

    return defaultState;
}
```

#### Presentation

##### GetTitle

=== "ASP.NET"

```csharp
public abstract string GetTitle();
```

Returns the title that the Wizard will show in the header.

#### Behavior

##### ExecuteFinish

=== "ASP.NET"

```csharp
public virtual async Task<WizardResultDto<TState>> ExecuteFinish(TState state)
{
    throw new NotImplementedException();
}
```

The main method of the Wizard, this will execute any operation that the Wizard was made for and return a **WizardResultDto** that will communicate the result of said operation, as well as deliver relevant data to ensure a correct presentation of the results.

=== "ASP.NET"

```csharp
public class WizardResultDto
{
    public required bool Success { get; set; }
    public required List<string> Messages { get; set; }
    public List<string> SchedulerReport { get; set; } = [];
    public RootLayout? Layout { get; set; }
}

public class WizardResultDto<TState> : WizardResultDto
{
    public TState? Data { get; set; }
}
```

The **WizardResultDto** properties are:

- `Success`: Defines if the operation was successful or not. The Wizard's result screen will react accordingly to this, displaying the correct message regarding the result as step title in the header.
- `Messages`: A list of all messages that are desired to show. They will be rendered on their own line each, and will appear in regardless of the success, so they can be used to either show the result of a positive operation, or communicate errors from a negative operation.
- `SchedulerReport`: This list of messages will be used when executing the **ExecuteFinish** method as a [**scheduled task**](../integrations/scheduledtasks.md), giving the possibility to have a different message to cover a different context, like sending an email with the results.
- `RootLayout`: If this property is sent, the Wizard will render a full read-only layout to convey the information in a better visual way, using controls. The **TState** will be used to define the link to properties for this nodes, which can be modified in the **ExecuteFinish** to carry relevant values. For example, if I want to show the result of a mathematical add operation, I can set a property of **TState** to carry that result, and then show it with a [**NumberControl**](../../components/controls/numbercontrol.md).
- `Data`: The final **TState** instance after going through the **ExecuteFinish**. This needs to be sent for the frontend to correctly render controls if `RootLayout` is sent.

In the next example, the operation that is executed is a simple division. The Wizard will communicate a failure if the divide value chosen is zero, and will show a layout with the results if the division is correct. The **SchedulerReport** property is formatted for this Wizard to be able to deliver its output when scheduled.

```csharp
public override async Task<WizardResultDto<TestWizardTest>> ExecuteFinish(TestWizardTest state)
{
    if (state.DivideValue == 0)
        return new WizardResultDto<TestWizardTest> { Success = false, Messages = ["Cannot divide by zero"] };

    int resultNumber = state.Number / state.DivideValue;

    state.ResultNumber = resultNumber;

    RootLayoutBuilder<TestWizardTest> finishLayout = new();

    RowNodeBuilder<TestWizardTest> row1 = finishLayout.AddRow(3);

    row1.AddControlToRow(p => p.Number, new NumberControl() { Label = "Number" });
    row1.AddControlToRow(p => p.DivideValue, new NumberControl() { Label = "Divide value" });

    RowNodeBuilder<TestWizardTest> row2 = finishLayout.AddRow(3);

    row3.AddControlToRow(p => p.ResultNumber, new NumberControl() { Label = "Result" });

    RowNodeBuilder<TestWizardTest> row3 = finishLayout.AddRow();

    row5.AddContentToRow(new LabelContent() { LogicalKey = ContentKeys.InfoLabel, Text = "Remember: never divide by zero!" });

    return new WizardResultDto<TestWizardTest>()
    {
        Success = true,
        Messages = ["Division"],
        SchedulerReport = [$"Number: {state.Number}",
            $"Divided by: {state.DivideValue}",
            "",
            $"Result: {state.ResultNumber}",],
        Layout = finishLayout.Layout,
        Data = state
    };
}
```

##### HandleScheduledOutput

=== "ASP.NET"

```csharp
public virtual async Task<Result> HandleScheduledOutput(WizardScheduledDto scheduledDto, string deliveryConfigurationJson)
{
    return Result.Failure(Error.NotFound("NotImplemented", ["Scheduled output handling not implemented for this wizard"]));
}
```

This method will be called when the Wizard gets executed as a [**scheduled task**](../integrations/scheduledtasks.md) to handle how the output of that operation should be handled. The **WizardScheduledDto** is created from the scheduler automatically and received here.

=== "ASP.NET"

```csharp
public class WizardScheduledDto
{
    public required string Title { get; set; }
    public required WizardResultDto Result { get; set; }
    public required TimeZoneInfo? TimeZoneInfo { get; set; }
    public required string? Language { get; set; }
}
```

The **WizardScheduledDto** properties are:

- `Title`: Defines a title to be used as it is needed. For example, it could be the first line on an email, or the first row in an Excel file.
- `Result`: The **WizardResultDto** generated in the [**ExecuteFinish**](#executefinish) method.
- `TimeZoneInfo`: Property useful to handle localization. Should mirror the locale used on the frontend.
- `Language`: Defines the language used when the task was created. For example, `"en"` will be *english*, while `"es"` while be *spanish*.

## **Step definition**

### **Properties**

#### Name

=== "ASP.NET"

```csharp
public abstract string Name { get; set; }
```

The name of the step, shown as subtitle in the Wizard's header.

### **Methods**

#### Presentation

##### GetRootLayout

=== "ASP.NET"

```csharp
protected abstract RootLayout GetRootLayout();
```

This method is equivalent to the [**GetRootLayout**](./crudprocess.md#getrootlayout) method used by [**Crud Processes**](./crudprocess.md), but using your **TState** class instead of **TEntity**. Check that method to see its characteristics plus an example.

##### GetLayoutConditions

=== "ASP.NET"

```csharp
protected virtual List<ConditionRule> GetLayoutConditions() => [];
```

This method is equivalent to the [**GetLayoutConditions**](./crudprocess.md#getlayoutconditions) method used by [**Crud Processes**](./crudprocess.md), but using your **TState** class instead of **TEntity**. Check that method to see its characteristics plus an example.

##### GetValidationRules

=== "ASP.NET"

```csharp
protected virtual List<ValidationRule> GetValidationRules() => [];
```

This method is equivalent to the [**GetValidationRules**](./crudprocess.md#getvalidationrules) method used by [**Crud Processes**](./crudprocess.md), but using your **TState** class instead of **TEntity**. Check that method to see its characteristics plus an example.

#### Lifecycle

These methods allow the wizard to implement conditional navigation. A wizard can skip steps, branch into different flows, or return to different previous steps depending on the current state.

##### GetNextStepIndex

=== "ASP.NET"

```csharp
public virtual Task<int> GetNextStepIndex(int index, TState state, IList<WizardStep<TState>> steps)
{ return Task.FromResult(index + 1); }
```

Returns the index value of the next step. Useful if the Wizard can fork its flow depending on the data inputted ny the user.

##### GetPreviousStepIndex

=== "ASP.NET"

```csharp
public virtual Task<int> GetPreviousStepIndex(int index, TState state, IList<WizardStep<TState>> steps)
{ return Task.FromResult(index - 1 < 0 ? 0 : index - 1); }
```

Returns the index value of the previous step. Useful if the Wizard has forked its flow and needs to return to a different step to ensure coherence.

#### Lifecycle hooks

##### OnEnter

=== "ASP.NET"

```csharp
public virtual async Task<Result> OnEnter(TState state)
{ return Result.Success(); }
```

This method gets executed as soon as the step is built, allowing to execute code if needed to complement the flow.

##### OnNext

=== "ASP.NET"

```csharp
public virtual async Task<Result> OnNext(TState state)
{ return Result.Success(); }
```

This method gets executed when the next step is requested, before it takes precedence over the old one, allowing to execute code if needed to complement the flow.

##### OnBack

=== "ASP.NET"

```csharp
public virtual async Task<Result> OnBack(TState state)
{ return Result.Success(); }
```

This method gets executed when the previous step is requested, before it takes precedence over the actual one, allowing to execute code if needed to complement the flow.
