# **LookupComponent**

## **Description**

Renders a select control filled with fetched options.

Lookups have their own definition in the backend through a [**Lookup Provider**](../../definitions/providers/lookup.md), which can be identified by their **lookupCode**.

This control inherits all properties from **BaseControl**. The metadata below only documents the properties specific to **LookupControl**.

## **LookupControl metadata**

=== "React"

```ts
interface LookupControl extends BaseControl {
  lookupCode: string;
  includeEmptyOption: boolean;
}
```

### lookupCode

Identifies the definition linked to the control in the backend.

### includeEmptyOption

Toggles the ability to chose no value from the lookup. If disabled, the first available option will be the default value, unless a default value is sent from the owners of the lookup.

## Example

In the following example, a lookup control is created, which retrieves its options from the backend via the **GeneralLookupProvider** definition.

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.Lookup_Id,
    new LookupControl(typeof(GeneralLookupProvider))
    {
        Required = false,
        IncludeEmptyOption = true
    });
```
