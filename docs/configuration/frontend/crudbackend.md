# **GreenFireCrudProcessBackend**

## **Description**

This is the backend responsible for all [**Crud processes**](../../components/processes/crudprocess.md) data management.

### getCrudProcessServiceUrl

Retrieves the base Url where the expected process will be hosted.

=== "React"

```ts
async getCrudProcessServiceUrl(processCode: string) {
    let url = null;
    const serviceUrlRes = await axiosApi.get(
        `${import.meta.env.BASE_URL}/services/${processCode}`
    );
    url = serviceUrlRes.status == 200 ? serviceUrlRes.data : import.meta.env.BASE_URL;

    return url;
}
```

### createCrudProcessMainGridDataSource

Retrieves the data shown in the main datagrid of Crud processes.

=== "React"

```ts
createCrudProcessMainGridDataSource(serviceUrl: string, processCode: string) {
    const url = `${serviceUrl}/crudengine/${processCode}/getgriddata`

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

### getCrudProcessDefinition

Retrieves the process title, execution mode, columns and main buttons.

=== "React"

```ts
async getCrudProcessDefinition(serviceUrl: string, processCode: string) {
    const definition = await axiosApi.get(`${serviceUrl}/crudengine/${processCode}/getdefinition`);

    return definition.data;
}
```

Expected return:

=== "Json"

```json
{
  "title": "Crud Process Title",
  "executionMode": "Standard",
  "columns": [
    {
      "pascalCaseField": "Field",
      "field": "field",
      "headerName": "Field",
      "visible": true,
      "dataType": null,
      "autoAdjust": true,
      "minWidth": null,
      "options": null
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
  "mainButtons": [
    {
      "label": null,
      "style": "primary",
      "action": "new"
    },
    {
      "label": null,
      "style": "secondary",
      "action": "edit"
    },
    {
      "label": null,
      "style": "primary",
      "action": "duplicate"
    },
    {
      "label": null,
      "style": "danger",
      "action": "delete"
    }
  ]
}
```

### getCrudProcessEntity

Retrieves the data for a particular record by Id, used in Crud processes when editing. Keep in mind that if no Id is sent, the default GreenFire backend implementation will try to get the first Id available (used in **SingleRegistry** execution mode).

=== "React"

```ts
async getCrudProcessEntity(serviceUrl: string, processCode: string, id: any) {
    const getDataPath = id ? `${processCode}/${id}` : `${processCode}`;

    const res = await axiosApi.get(`${serviceUrl}/crudengine/${getDataPath}`);

    return res.data;
}
```

Expected return:

=== "Json"

```json
{
  "id": 1,
  "field": "Field 1",
  "number": 1
}
```

### getCrudProcessDuplicatedData

Retrieves the data with default values when using the *Duplicate* option.

=== "React"

```ts
async getCrudProcessDuplicatedData(serviceUrl: string, processCode: string, id: any) {
    const res = await axiosApi.get(`${serviceUrl}/crudengine/${processCode}/getduplicateddata/${id}`);

    return normalizeData(res.data);
}
```

Expected return:

=== "Json"

```json
{
  "id": 1,
  "field": "Copy of Field 1",
  "number": 1
}
```

### getCrudProcessDefaultData

Retrieves the data with default values used when a *New* record is created.

=== "React"

```ts
async getCrudProcessDefaultData(serviceUrl: string, processCode: string) {
    const res = await axiosApi.get(`${serviceUrl}/crudengine/${processCode}/getdefaultdata`);

    return normalizeData(res.data);
}
```

Expected return:

=== "Json"

```json
{
  "id": null,
  "field": "Default field name",
  "number": 0
}
```

### addItemCrudProcess

Called when a *New* item is saved. The payload represents the new item.

=== "React"

```ts
async addItemCrudProcess(serviceUrl: string, processCode: string, payload: Record<string, any>) {
    await axiosApi.post(`${serviceUrl}/crudengine/${processCode}/add`, payload);
}
```

### modifyItemCrudProcess

Called when an *Edited* item is saved. The payload represents the modified item.

=== "React"

```ts
async modifyItemCrudProcess(serviceUrl: string, processCode: string, payload: Record<string, any>) {
    await axiosApi.put(`${serviceUrl}/crudengine/${processCode}/save`, payload);
}
```

### deleteItemsCrudProcess

Called when the *Delete* option is used, sending a list of the selected Ids for deletion.

=== "React"

```ts
async deleteItemsCrudProcess(serviceUrl: string, processCode: string, selectedIds: any[] | undefined) {
    await axiosApi.delete(`${serviceUrl}/crudengine/${processCode}/delete`, {
        params: { ids: selectedIds },
        paramsSerializer: params => qs.stringify(params, { arrayFormat: "repeat" })
    })
}
```
