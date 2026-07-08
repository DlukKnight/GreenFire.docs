# **Welcome to GreenFire Docs**

## **Description**

GreenFire is a metadata-driven framework for building business applications on ASP.NET and React, where processes, forms and dashboards are defined through reusable contracts instead of repetitive UI and CRUD code.

## **Features**

GreenFire provides:

* ASP.NET backend
* React frontend
* Dynamic forms
* Generic [**Crud processes**](./definitions/processes/crudprocess.md)
* [**Wizards**](./definitions/processes/wizard.md)
* [**Dashboards**](./definitions/processes/dashboard.md)
* [**Reports**](./definitions/processes/reportprocess.md)
* [**Scheduler**](./definitions/integrations/scheduledtasks.md) support
* [**Audit support**](./definitions/integrations/audit.md)
* [**Localization**](./definitions/additionalfeatures/localization.md)

## **Philosophy**

GreenFire embraces several design decisions, which are strongly present through all the framework.

* **Light and easy**: GreenFire aims to minimize boilerplate while keeping its internal architecture simple and predictable. Most concepts are intentionally small and composable instead of relying on large inheritance hierarchies or complex configuration.
* **Contract based**: GreenFire prefers contracts over implementations. Most extension points are defined through interfaces and base contracts, allowing applications to integrate their own infrastructure and business logic. Only a small number of foundational classes, such as [**Entity**](./definitions/infrastructure/entities.md#entities), are required.
* **Agnostic**: GreenFire does not require any particular persistence technology, scheduler, audit engine, localization system or external package. Applications are free to integrate whichever solutions best fit their needs.
* **Convention over configuration**: Whenever possible, GreenFire relies on conventions and discovery instead of manual registration. Providers, processes and components are automatically discovered through attributes and registries, reducing setup and keeping projects organized.

## **Architecture diagram**

```text
ASP.NET
    │
Processes
    │
Layouts
    │
Metadata
    │
React UI
```
