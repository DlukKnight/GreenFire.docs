# **Overview**

## **Description**

Providers define the data fetching of controls that have dynamic values, like [**lookups**](../../components/controls/lookupcontrol.md) or [**datagrids**](../../components/controls/datagridcontrol.md).

The definition is simple, by inheriting from a base class and applying a code via a custom attribute. This definitions are automatically discovered by GreenFire, without needing any registration, and instantiated when needed.

Providers are instantiated only when requested by a control. They are stateless and should simply return the data required by that request.
