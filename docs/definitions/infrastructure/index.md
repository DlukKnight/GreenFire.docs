# **Overview**

## **Description**

GreenFire does not impose a specific persistence technology. Instead, it relies on a small set of contracts that allow the framework to interact with your domain model regardless of the underlying implementation. Entities, repositories and services only need to implement a few interfaces or inherit from lightweight base classes. These base classes are intentionally minimal so they do not interfere with your own architecture. GreenFire also offers a repository implementation that makes it easy to integrate your classes with the system's flow.
