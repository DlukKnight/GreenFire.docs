# **ReportProcess**

## **Description**

Defines the metadata and behavior required to render and operate a complete Report generator interface, with filters.

## **Lifecycle**

A Report Process follows this general flow:

1. The process is discovered through the ReportCode attribute.
2. The RootLayout, conditions and validation rules are loaded.
3. The user may interact with the filters.
4. Filters are applied.
5. The user selects the *Generate* option and choses a format.
6. The report is created and the user opens it or saves it to the disk.

## **First steps**

Report are easy to define, by inheriting from the **ReportProcess** abstract class and decorating them with the **ReportCode** attribute. This code is unique and there should be only one among all the processes, trying to register a second one with the same code will end in an API validation failure.

=== "ASP.NET"

```csharp
public abstract class ReportProcess<TParameters, TViewModel> : IReportProcess<TParameters> where TViewModel : IReportViewModel
```

After those criteria are met, the process needs to define in the **TParameters** parameter a class with all the properties that will be linked to the filters, as well as **TViewModel**, which will represent the view model used for the generation, inheriting from **IReportViewModel**:

=== "ASP.NET"

```csharp
public interface IReportViewModel
{
}

public interface IReportViewModel<TRow> : IReportViewModel where TRow : IReportViewModelRow
{
    string Title { get; set; }
    List<TRow> Rows { get; set; }
}

public interface IReportViewModelRow
{
}
```

Basic example:

=== "ASP.NET"

```csharp
public class TestReportViewRows : IReportViewModelRow
{
    public required string Name { get; set; }
    public int? Value { get; set; }
}

public class TestReportViewModel : IReportViewModel<TestReportViewRows>
{
    public required string Title { get; set; }
    public required DateTimeOffset GenerationDate { get; set; }
    public required List<TestReportViewRows> Rows { get; set; }
}

[ReportCode("testReport")]
public class TestReport() : ReportProcess<TestReportParameters, TestReportViewModel>
```

There is still an additional configuration that Greenfire provides, whi is the creation of a **Report Generator**. For that purpose, the interface **IReportGenerator** is provided:

=== "ASP.NET"

```csharp
public interface IReportGenerator<T> where T : IReportViewModel
{
    ReportResult Generate(T data, string language);
}
```

Keep in mind that while you could skip this and just use your own service, this interface offers the typing and structure needed, and the idea is to have one of this for each file type that your process will support.

After this setup is satisfied, we are ready to start implementing the different properties and methods that configure the definition.

## **Minimal implementation**

The smallest possible Dashboard consists of:

- ReportCode
- [**GetRootLayout()**](#getrootlayout)
- [**Generate()**](#generate)

Everything else is optional.

## **Properties**

### ImplementsScheduler

=== "ASP.NET"

```csharp
public virtual bool ImplementsScheduler => false;
```

Enables the use of the process with the [**scheduler implementation**](../integrations/scheduledtasks.md). By default, this value is **false**.

## **Methods**

### Access

#### HasAccess

=== "ASP.NET"

```csharp
public virtual bool HasAccess() => true;
```

Return a boolean value that indicates if access criteria is met when trying to do any operation with the process. By default, it always return **true**.

An example to this method could be checking if the logged user has the right permissions to access the process.

### Loading

#### GetDefaultData

=== "ASP.NET"

```csharp
public virtual async Task<TParameters> GetDefaultData()
{
    TParameters dto = Activator.CreateInstance<TParameters>();

    return dto;
}
```

This method is equivalent to the [**GetDefaultData**](./wizard.md#getdefaultdata) method used by [**Wizard**](./wizard.md), but using your **TParameters** class instead of **TState**. Check that method to see its characteristics plus an example.

### Presentation

#### GetTitle

=== "ASP.NET"

```csharp
public abstract string GetTitle();
```

Returns the title that the process will show in the header.

#### GetRootLayout

=== "ASP.NET"

```csharp
protected abstract Task<RootLayout> GetRootLayout();
```

This method is equivalent to the [**GetRootLayout**](./crudprocess.md#getrootlayout) method used by [**Crud Processes**](./crudprocess.md), but using your **TParameters** class instead of **TEntity**. Check that method to see its characteristics plus an example.

### Behavior

#### GetLayoutConditions

=== "ASP.NET"

```csharp
protected virtual async Task<List<ConditionRule>> GetLayoutConditions() => [];
```

This method is equivalent to the [**GetLayoutConditions**](./crudprocess.md#getlayoutconditions) method used by [**Crud Processes**](./crudprocess.md), but using your **TParameters** class instead of **TEntity**. Check that method to see its characteristics plus an example.

#### GetValidationRules

=== "ASP.NET"

```csharp
protected virtual async Task<List<ValidationRule>> GetValidationRules() => [];
```

This method is equivalent to the [**GetValidationRules**](./crudprocess.md#getvalidationrules) method used by [**Crud Processes**](./crudprocess.md), but using your **TParameters** class instead of **TEntity**. Check that method to see its characteristics plus an example.

### Behavior

#### SupportedOutputs

=== "ASP.NET"

```csharp
public abstract List<ReportOutputType> SupportedOutputs { get; }
```

Returns the available file types for generation.

#### Generate

=== "ASP.NET"

```csharp
public virtual async Task<ReportResult> Generate(ReportOutputType outputType, DateTimeOffset generationDate, TimeZoneInfo? timezoneInfo, string language, TParameters parameters)
{
    throw new NotImplementedException();
}
```

This method will be executed when the **Generate** option is clicked by the user. The received parameters are as follows:

- `outputType`: The file type selected.
- `generationDate`: Date to show in the report, sent from the frontend. It should be the actual UTC date at the moment of the operation.
- `timezoneInfo`: The timezone that the browser is handling, useful to calculate and correctly display all the dates that may be in the report.
- `language`: The language that is configured in the frontend.
- `parameters`: The filters needed to generate the report.

!!! Note
    GreenFire does not enforce any type of file generation, so the implementation of it should be custom defined. This leaves the freedom to use any libraries, methods or implementations as desired.

The **ReportResult** class should be returned here.

=== "ASP.NET"

```csharp
public class ReportResult
{
    public required bool Success { get; set; }
    public required string Message { get; set; }
    public required string FileName { get; set; }
    public required string ContentType { get; set; }
    public required byte[] Content { get; set; }
}
```

The **ReportResult** properties are:

- `Success`: Defines if the operation was successful or not.
- `Message`: A message meant to be sent as extra information from the operation.
- `FileName`: The name the file should have.
- `ContentType`: The content type used by the frontend to decode the file type. An example of this will be:

    |**ContentType**|**Value**|
    |---|---|
    |Csv|"text/csv"|
    |Xlsx|"application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"|
    |Pdf|"application/pdf"|

- `Content`: The actual data of the file as byte[].

The next example shows a really simple generation using the classes in the example shown in the [**minimal implementation**](#first-steps). The view model is created by applying the defined filters, then a generator class is instantiated and used depending in the file type selected. Each of the generators have their own independent logic, not defined by GreenFire.

=== "ASP.NET"

```csharp
protected TestReportViewModel GetViewModel(TestReportParameters parameters, DateTimeOffset generationDate, TimeZoneInfo? timezoneInfo)
{
    IQueryable<TestEntity> entities = testEntityService.Get();

    if (parameters.FilterByName)
        entities = entities.Where(p => p.Name.Contains(parameters.ContainsName));

    if (parameters.MinValue is not null)
        entities = entities.Where(p => p.Value == null || p.Value != null && p.Value >= parameters.MinValue);

    if (parameters.MaxValue is not null)
        entities = entities.Where(p => p.Value == null || p.Value != null && p.Value <= parameters.MaxValue);

    List<TestReportRowViewModel> rows = [.. entities.Select(p => new TestReportRowViewModel
    {
        Name = p.Name,
        Value = p.Value,
    })];

    return new TestReportViewModel()
    {
        Title = GetTitle(),
        GenerationDate = TimeZoneInfo.ConvertTimeFromUtc(generationDate.DateTime, timezoneInfo ?? TimeZoneInfo.Local),
        Rows = rows
    };
}

public override async Task<ReportResult> Generate(ReportOutputType outputType, DateTimeOffset generationDate, TimeZoneInfo? timezoneInfo, string language, TestReportParameters parameters)
{
    TestReportViewModel viewModel = GetViewModel(parameters, generationDate, timezoneInfo);

    if (outputType == ReportOutputType.Excel)
        return new TestReportExcelGenerator().Generate(viewModel, language);

    if (outputType == ReportOutputType.Csv)
        return new TestReportCsvGenerator().Generate(viewModel, language);

    if (outputType == ReportOutputType.Pdf)
        return new TestReportPdfGenerator().Generate(viewModel, language);

    return new ReportResult
    {
        Success = false,
        Message = "Missing output type",
        FileName = "",
        ContentType = "",
        Content = null!
    };
}
```

#### HandleScheduledOutput

=== "ASP.NET"

```csharp
public virtual async Task<Result> HandleScheduledOutput(ReportResult result, string deliveryConfigurationJson)
{
    return Result.Failure(Error.NotFound("NotImplemented", ["Scheduled output handling not implemented for this report"]));
}
```

This method will be called when the process gets executed as a [**scheduled task**](../integrations/scheduledtasks.md) to handle how the output of that operation should be handled. The **ReportResult** is created from the scheduler automatically and received here.
