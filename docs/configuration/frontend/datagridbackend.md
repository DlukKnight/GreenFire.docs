# GreenFireDatagridBackend

## **Description**

This is the backend responsible for datagrid controls data retrieval.

### getDatagridColumns

Retrieves the columns definition.

=== "React"

```ts
async getDatagridColumns(serviceUrl: string, datagridCode: string): Promise<any[]> {
    const columns = await axiosApi.get(`${serviceUrl}/datagrid/${datagridCode}/getcolumns`);

    return columns.data;
}
```

Expected return:

=== "Json"

```json
[
  {
    "pascalCaseField": "Name",
    "field": "name",
    "headerName": "Name",
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
]
```

### createDatagridDataSource

Retrieves the data shown in the datagrid.

=== "React"

```ts
createDatagridDataSource(serviceUrl: string, processCode: string, datagridCode: string): GridDataSource<any> {
    const url = `${serviceUrl}/datagrid/${datagridCode}/getdata?processCode=${processCode}`

    return {
        fetch: async (request: GridDataRequest<any>): Promise<GridDataResult<any>> => {
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
      "name": "Name 1",
      "number": 1,
      "id": 1
    },
    {
      "name": "Name 2",
      "number": 2,
      "id": 2
    }
  ],
  "totalCount": 2
}
```
