# **Scheduled tasks**

## **Description**

GreenFire offers **scheduler** support by allowing implementations using defined base contracts.

A scheduled task in GreenFire is composed of three responsibilities:

- **ScheduledTask** stores what should execute and when.
- **ISchedulerTaskOrchestrator** integrates with the scheduling technology (for example **HangFire**).
- **IScheduledProcessExecutor** executes GreenFire processes when the scheduler triggers them.

## **Lifecycle**

1. A **ScheduledTask** is created.
2. The application persists it.
3. The task is registered using **ISchedulerTaskOrchestrator**.
4. The scheduler triggers the execution.
5. The orchestrator calls **IScheduledProcessExecutor**.
6. GreenFire executes the corresponding process.
7. If delivery is enabled, **HandleScheduledOutput()** is executed.

!!! Note
    GreenFire does not implement its own scheduling engine. Instead, it provides contracts that allow integration with any scheduling technology such as HangFire, Quartz.NET or a custom implementation.

## **Supported process types**

Currently, the following processes support scheduling:

- [**Wizard**](../processes/wizard.md)
- [**Report Process**](../processes/reportprocess.md)

Processes expose this capability by enabling their [**ImplementsScheduler**](../processes/wizard.md#implementsscheduler) property.

## **Execution**

### ScheduledTask

This class represent an entity that will store the parameters of the task.

=== "ASP.NET"

```csharp
public class ScheduledTask : Entity<Guid>
{
    public required ProcessTypeEnum ProcessType { get; set; }

    public required string ProcessCode { get; set; }

    public required string ExecutionParametersJson { get; set; }

    public bool EnableDeliveryAction { get; set; }

    public required string DeliveryConfigurationJson { get; set; }

    public required string ScheduleExpression { get; set; }

    public bool Enabled { get; set; }
}
```

The properties represented in the class cover the minimal implementation needed for tasks to be executed.

!!! Note
    This entity is not persisted in any way, as GreenFire's philosophy is that data storage should be an application concern. Therefore, persisting it will be part of the implementation.

The properties are as follows:

|**Property**             |**Description**                                                                                                   |
|-------------------------|------------------------------------------------------------------------------------------------------------------|
|ProcessType              |Stores the type of the process that should execute the task|
|ProcessCode              |Stores the code of the process that should execute the task|
|ExecutionParametersJson  |Stores the execution data that will be sent to the process for the execution of the task. For example, the defined **TState** in the case of a [**Wizard**](../processes/wizard.md), which will be used in the [**ExecuteFinish**](../processes/wizard.md#executefinish) method|
|EnableDeliveryAction     |Specifies if the task should do something with the result of the task. If disabled, only the execution will happen|
|DeliveryConfigurationJson|Stores the configuration needed to define what to do if the delivery is enabled. For example, the data necessary to send an email with the results|
|ScheduleExpression       |The expression used to determine the frequency of the task's execution. We recommend the use of **Cron expressions**|
|Enabled                  |Defines if the task is enabled|

While this is the minimal configuration, if more properties are required, inheriting from this class is possible, which will allow to add anything needed to expand the task's scope.

!!! Note
    For a task to be executed, the process has to have the scheduler implementation. Refer to [**ImplementsScheduler**](../processes/wizard.md#implementsscheduler) for more information. Keep in mind that the same property is used in other processes.

### ScheduledProcessExecutor

This is the class that we'll use to execute the tasks, normally injecting it through its interface:

=== "ASP.NET"

```csharp
public interface IScheduledProcessExecutor
{
    Task<Result> ExecuteAsync(ScheduledTask task);
}
```

The **ExecuteAsync** method is really simple to use, by just passing a **ScheduledTask** instance, or an instance of a class that inherits it.

### ISchedulerTaskOrchestrator

This interface defines the contract used to integrate a scheduling engine with GreenFire. The **Orchestrator** will be the one in charge to register, unregister and change the state of the tasks and, as with most of GreenFire's implementations, it is technological agnostic, meaning that you could use any task environment you'd like, even multiple ones if it is necessary.

=== "ASP.NET"

```csharp
    public interface ISchedulerTaskOrchestrator
    {
        Task ScheduleAsync(ScheduledTask task);

        Task UnscheduleAsync(Guid taskId);

        Task EnableAsync(Guid taskId);

        Task DisableAsync(Guid taskId);
    }
```

For example, a minimal implementation using **HangFire**:

=== "ASP.NET"

```csharp
public interface IHangfireSchedulerTaskOrchestrator : ISchedulerTaskOrchestrator
{
}

public class HangfireSchedulerTaskOrchestrator(IScheduledProcessExecutor scheduledProcessExecutor, IScheduledTaskService scheduledTaskService) : IHangfireSchedulerTaskOrchestrator
{
    private readonly IScheduledTaskService _scheduledTaskService = scheduledTaskService;
    private readonly IScheduledProcessExecutor _scheduledProcessExecutor = scheduledProcessExecutor;

    public async Task<Result> ExecuteAsync(Guid taskId)
    {
        ScheduledTask task = _scheduledTaskService.GetByIds([taskId]).First();

        Result result = await _scheduledProcessExecutor.ExecuteAsync(task);

        CronExpression cron = CronExpression.Parse(task.ScheduleExpression);
        DateTimeOffset? nextExecution = cron.GetNextOccurrence(DateTime.UtcNow);

        return result;
    }

    public Task ScheduleAsync(ScheduledTask task)
    {
        RecurringJob.AddOrUpdate<HangfireSchedulerTaskOrchestrator>(
            task.Id.ToString(),
            provider => provider.ExecuteAsync(task.Id),
            task.ScheduleExpression);

        return Task.CompletedTask;
    }

    public Task UnscheduleAsync(Guid taskId)
    {
        RecurringJob.RemoveIfExists(taskId.ToString());

        return Task.CompletedTask;
    }

    public Task EnableAsync(Guid taskId)
    {
        throw new NotImplementedException();
    }

    public Task DisableAsync(Guid taskId)
    {
        throw new NotImplementedException();
    }
}
```

Then, you could register your tasks in any way you prefer. For example, we could create a [**Crud process**] to manage the **ScheduledTask** persistance, and then use the **Orchestrator** on the [**After-Methods**](../processes/crudprocess.md#after-methods) method:

=== "ASP.NET"

```csharp
protected override async Task<Result<bool>> AfterSave(ScheduledTask entity)
{
    if (entity.Enabled)
    {
        await hangfireSchedulerOrchestrator.ScheduleAsync(entity);
    }
    else
    {
        await hangfireSchedulerOrchestrator.UnscheduleAsync(entity.Id);
    }

    return await base.AfterSave(entity);
}

protected override async Task<Result<bool>> AfterDelete(IEnumerable<Guid> ids)
{
    await hangfireSchedulerOrchestrator.UnscheduleAsync(ids.First());

    return await base.AfterDelete(ids);
}
```

### Collecting execution parameters

GreenFire offers a component created specifically with this goal in mind, the [**DynamicProcessRootLayout**](../../components/controls/dynamicprocessrootlayoutcontrol.md), which will trigger a [**dialog**](../../configuration/frontend/dialogs.md#showprocessparameters), in which the execution data could be collected. The best way to do this is to call the relevant processes and override their [**onFinish**](../../components/processes/wizard.md#onfinish) method, so the the parameters entered by the user can be collected instead of executed.

## **Delivery**

### HandleScheduledOutput

After the task is executed, if the **EnableDeliveryAction** was enabled, the [**HandleScheduledOutput**](../processes/wizard.md#handlescheduledoutput) method of the process will be executed with the result parameters. How to handle the implementation here is completely custom.

For example, we could create a dispatcher class for [**Wizards**](../processes/wizard.md) and implement handlers for each option, configured in the **deliveryConfigurationJson** parameter, that we also custom typed:

=== "ASP.NET"

```csharp
public class WizardDeliveryDispatcher(IEnumerable<IWizardDeliveryHandler> handlers, IJsonSerializationService serializer)
{
    public async Task<Result> DeliverAsync(WizardScheduledDto scheduledDto, string deliveryConfigurationJson)
    {
        DeliveryConfigLayout config = serializer.Deserialize<DeliveryConfigLayout>(deliveryConfigurationJson)!;

        IWizardDeliveryHandler? handler = handlers.FirstOrDefault(x => x.Mode == config.OutputMode);

        if (handler is null)
            return Result.Failure(Error.Failure("Failure", ["Invalid output mode"]));

        return await handler.DeliverAsync(scheduledDto, deliveryConfigurationJson);
    }
}
```

### Collecting delivery configuration

This property was created as a Json to evade forcing the application to implement a fixed set of options, since each application could have their own way to deliver the options. To help the definition of this property, the [**RootLayoutJsonControl**](../../components/controls/rootlayoutjsoncontrol.md) is appropriate, since it allows to define a [**RootLayout**](../../components/containers/layout/rootlayoutcontainer.md) that will return its value as a **Json string**. This will allow to parse the value against a defining class, like this one:

=== "ASP.NET"

```csharp
public class DeliveryConfigLayout
{
    public required ScheduledTaskOutputModeEnum OutputMode { get; set; }

    public Guid? SenderEmail { get; set; }

    public string? DestinyEmail { get; set; }

    public string? Subject { get; set; }

    public string? Body { get; set; }

    public ScheduledSaveFolderEnum SaveFolder { get; set; }
}
```

This allows for any type of implementation to be possible.
