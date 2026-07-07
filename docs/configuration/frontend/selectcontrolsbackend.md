# **GreenFireSelectControlsBackend**

## **Description**

This is the backend responsible for controls that have dynamic selectable options data retrieval.

### getCheckboxListItems

Retrieves all the options for a CheckboxList control type.

=== "React"

```ts
async getCheckboxListItems(serviceUrl: string, processCode: string, checkboxListCode: string, processEntityId?: any): Promise<CheckboxListItem[]> {
    const res = await axiosApi.get(`${serviceUrl}/checkboxlist/${checkboxListCode}/get`, {
        params: {
            processCode: processCode,
            processEntityId: processEntityId
        }
    });

    return res.data;
}
```

Expected return:

=== "Json"

```json
[
  {
    "id": 1,
    "optionDisplayName": "Option 1",
    "checked": false
  },
  {
    "id": 1,
    "optionDisplayName": "Option 2",
    "checked": false
  }
]
```

### getLookupOptions

Retrieves all the options for a Lookup control type.

=== "React"

```ts
async getLookupOptions(serviceUrl: string, processCode: string, lookupCode: string, processEntityId?: any): Promise<any[]> {
    const res = await axiosApi.get(`${serviceUrl}/lookup/${lookupCode}/get`, {
        params: {
            processCode: processCode,
            processEntityId: processEntityId
        }
    });

    return res.data;
}
```

Expected return:

=== "Json"

```json
[
  {
    "value": 6,
    "label": "Green"
  },
  {
    "value": 12,
    "label": "Red"
  },
  {
    "value": 15,
    "label": "Yellow"
  }
]
```

### getSelectOptions

Retrieves all the options for a Select control type that depends on another property value.

=== "React"

```ts
async getSelectOptions(serviceUrl: string, selectOptionsProviderCode: string, dependsOnPropertyValue: string): Promise<SelectOption[]> {
    const res = await axiosApi.get(`${serviceUrl}/selectoptionsprovider/${selectOptionsProviderCode}/get`, {
        params: {
            dependsOnPropertyValue: dependsOnPropertyValue,
        }
    });

    return res.data;
}
```

Expected return:

=== "Json"

```json
[
  {
    "value": 6,
    "label": "Green"
  },
  {
    "value": 12,
    "label": "Red"
  }
]
```
