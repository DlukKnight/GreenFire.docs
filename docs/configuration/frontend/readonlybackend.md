# **GreenFireReadOnlyProcessBackend**

## **Description**

This is the backend responsible for [**Read-only processes**](../../components/processes/readonlyprocess.md) data retrieval.

### getReadOnlyProcessServiceUrl

Retrieves the base Url where the expected process will be hosted.

=== "React"

```ts
async getReadOnlyProcessServiceUrl(readOnlyProcessCode: string) {
    let url = null;
    const serviceUrlRes = await axiosApi.get(
        `${import.meta.env.BASE_URL}/services/${readOnlyProcessCode}`
    );
    url = serviceUrlRes.status == 200 ? serviceUrlRes.data : import.meta.env.BASE_URL;

    return url;
}
```

### createReadOnlyProcessMainGridDataSource

Retrieves the data shown in the main datagrid of Read-only processes.

=== "React"

```ts
createReadOnlyProcessMainGridDataSource(serviceUrl: string, processCode: string) {
    const url = `${serviceUrl}/readonlyprocess/${processCode}/getgriddata`

    return {
        fetch: async (request: GridDataRequest<any>) => {
            return await getServerGridData(request, url);
        }
    }
}

async function getServerGridData(request: GridDataRequest<any>, url: string) {
    let rowsRes: AxiosResponse<any, any, {}>;

    if (request.query) {
        rowsRes = await axiosApi.post(`${url}`,
            request.query,
            {
                params: {
                    page: request.page,
                    pageSize: request.pageSize,
                    sortField: request.sortField,
                    sortOrder: request.sortOrder,
                }
            });
    } else {
        rowsRes = await axiosApi.get(`${url}`, {
            params: {
                page: request.page,
                pageSize: request.pageSize,
                sortField: request.sortField,
                sortOrder: request.sortOrder,
            }
        });
    }

    const result: GridDataResult<any> = {
        rows: rowsRes.data.data,
        totalCount: rowsRes.data.totalCount
    }

    return result;
}
```

Expected return:

=== "Json"

```json
{
  "data": [
    {
      "field": "Field 1",
      "number": 1,
      "id": 1
    },
    {
      "field": "Field 2",
      "number": 2,
      "id": 2
    }
  ],
  "totalCount": 2
}
```

### getReadOnlyProcessDefinition

Retrieves Read-only process title, columns, filters RootLayout, with conditions and validations and the RootLayout to correctly render nodes, with conditions, mostly for visibility reasons.

=== "React"

```ts
async getReadOnlyProcessDefinition(serviceUrl: string, readOnlyProcessCode: string) {
    const definitionRes = await axiosApi.get(`${serviceUrl}/readonlyprocess/${readOnlyProcessCode}/getdefinition`);

    return definitionRes.data;
}
```

Expected return:

=== "Json"

```json
{
  "title": "ReadOnly process title",
  "columns": [
    {
      "pascalCaseField": "Field",
      "field": "field",
      "headerName": "Field",
      "visible": true,
      "dataType": null,
      "autoAdjust": true,
      "minWidth": null
    },
    {
      "pascalCaseField": "Number",
      "field": "number",
      "headerName": "Number",
      "visible": true,
      "dataType": null,
      "autoAdjust": true,
      "minWidth": null,
      "options": null
    }
  ],
  "rootLayout": {
    "containerType": "Root",
    "nodeType": "Container",
    "children": [
      {        
        "$type": "row",
        "containerType": "Row",
        "rowIndex": 0,
        "columnsAmount": 3,
        "nodeType": "Container",
        "children": [
            {
            "$type": "text",
            "type": "Text",
            "minLength": null,
            "maxLength": null,
            "nodeType": "Control",
            "propertyName": "field",
            "logicalKey": "field",
            "pascalCasePropertyName": "Field",
            "label": "Field",
            "required": false,
            "value": null,
            "key": "ca896174cb3b4779afe4dc268a3b4aa2",
            "visible": true
            },
            {
            "$type": "number",
            "type": "Number",
            "min": null,
            "max": null,
            "nodeType": "Control",
            "propertyName": "number",
            "logicalKey": "number",
            "pascalCasePropertyName": "Number",
            "label": "Number",
            "required": false,
            "value": null,
            "key": "068abc43c97342da87c697d2a62bf97b",
            "visible": true
            }
        ],
        "key": "94ee35647be042fcb6d17e37830b5214",
        "logicalKey": null,
        "visible": true
        } 
    ],
    "key": "1391b1457a284e8c94f62ce4b6cbdcf6",
    "logicalKey": null,
    "visible": true
  },
  "layoutConditions": [],
  "filtersLayout": {
    "containerType": "Root",
    "nodeType": "Container",
    "children": [
      {
        "$type": "row",
        "containerType": "Row",
        "rowIndex": 0,
        "columnsAmount": 3,
        "nodeType": "Container",
        "children": [
          {
            "$type": "text",
            "type": "Text",
            "minLength": null,
            "maxLength": null,
            "nodeType": "Control",
            "propertyName": "field",
            "logicalKey": "field",
            "pascalCasePropertyName": "Field",
            "label": "Field",
            "required": false,
            "value": null,
            "key": "c64709479b9f4936b17c019b510a6104",
            "visible": true
            },
            {
            "$type": "number",
            "type": "Number",
            "min": null,
            "max": null,
            "nodeType": "Control",
            "propertyName": "number",
            "logicalKey": "number",
            "pascalCasePropertyName": "Number",
            "label": "Number",
            "required": false,
            "value": null,
            "key": "2151a3fd61874f2ebf5b935109bdb455",
            "visible": true
            }          
        ],
        "key": "2151a3fd61874f2ebf5b935109bdb455",
        "logicalKey": null,
        "visible": true
      }
    ],
    "key": "f6abb8ac326642c1b09bcf7eeba25a5c",
    "logicalKey": null,
    "visible": true
  },
  "defaultFiltersData": {
    "field": null,
    "number": null
  },
  "filtersLayoutConditions": [],
  "filtersLayoutValidations": []
}
```

### getReadOnlyProcessData

Retrieves the data for a particular record by Id.

=== "React"

```ts
async getReadOnlyProcessData(serviceUrl: string, readOnlyProcessCode: string, id: any) {
    const getDataPath = id ? `${readOnlyProcessCode}/${id}` : `${readOnlyProcessCode}`;

    const res = await axiosApi.get(`${serviceUrl}/readonlyprocess/${getDataPath}`);

    return res.data;
}
```

Expected return:

=== "Json"

```json
{
    "id": 1,
    "field": 1,
    "number": 2
}
```
