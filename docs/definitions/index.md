# **Overview**

## **Description**

GreenFire is a metadata-driven framework. The backend exposes metadata definitions through API endpoints, while the frontend renders user interfaces dynamically from those definitions.

Definitions are classes defined by special inheritances and marked with data annotations that tag them with a code. Once they are constructed, GreenFire will automatically detect them and invoke them when needed, requiring no registration.

GreenFire does not require a specific persistence framework, ORM or architectural pattern. It integrates naturally with existing applications, provided the required contracts and base classes are implemented.

## Philosophy

GreenFire aims to move UI construction to the backend through strongly typed metadata definitions.

Instead of manually building forms, validations, grids and workflows in the frontend, developers describe them declaratively in C#. The framework generates the metadata required to render them dynamically in React.

## **Features**

* Metadata-driven architecture.
* Automatic discovery of definitions.
* Automatic metadata serialization.
* Support for [**Value Objects**](./infrastructure/valueobjects.md).
* [**Result**](./infrastructure/result.md)-pattern based operations.
* Fluent layout, condition and validation builders.
* Scheduler support.
* Audit support.
