# **Overview**

## **Description**

Unlike many backend frameworks, GreenFire requires almost no configuration.

Once the package is installed and registered, it is immediately ready to discover process definitions, layouts, controls, dashboards and reports.

The only optional configuration currently available is the localization dictionaries path.

=== "ASP.NET"

```csharp
builder.Services.AddGreenFire(options =>
{
    options.LocalizationPath = Path.Combine(AppContext.BaseDirectory, "Localization");
});
```

If omitted, GreenFire automatically looks for a Localization folder next to the application executable.

## **Optional integrations**

Some GreenFire features become available when additional services are registered.

For example, audit support is enabled by registering an implementation of **IAuditProvider**.

=== "ASP.NET"

```csharp
services.AddScoped<IAuditProvider, SqlAuditProvider>();
```

See the [**Audit**](../../definitions/integrations/audit.md) section for a complete implementation.
