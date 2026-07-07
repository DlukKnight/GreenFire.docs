# **CrudDto**

## **Description**

By design philosophy, GreenFire implements a Dto model for the data exchange between the definitions and the presentation. While at first glance this seems like a waste, because the created Dtos will most of the time be a copy of the entity they represent, there is a lot of value in this implementations.

## **Definition**

CrudDtos don't inherit or implement any interfaces. they are a simple bare class that should have all the properties from the entity that is going to be represented in a [**Crud process**](../processes/crudprocess.md). There are a few considerations to this:

- If the entity used is being declared with [**Value Object**](valueobjects.md) properties, the CrudDto should have the primitives that this **Value Objects** represent.
- If a property is in the entity but shouldn't be included in the process layout, the CrudDto does not need to include it in. When a modification happens, GreenFire will use the CrudDto to map only the properties sent in it, so the original values of the stored entity will be untouched.
