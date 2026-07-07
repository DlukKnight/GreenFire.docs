# **GreenFireReportProcessBackend**

## **Description**

This is the backend responsible for [**Report processes**](../../components/processes/reportprocess.md) filter and output management.

### getReportProcessServiceUrl

Retrieves the base Url where the expected process will be hosted.

=== "React"

```ts
async getReportProcessServiceUrl(reportCode: string) {
    let url = null;
    const serviceUrlRes = await axiosApi.get(
        `${import.meta.env.BASE_URL}/services/${reportCode}`
    );
    url = serviceUrlRes.status == 200 ? serviceUrlRes.data : import.meta.env.BASE_URL;

    return url;
}
```

### getReportProcessDefinition

Return report title, supported output extensions and filters default data.

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
  "title": "My report",
  "supportedOutputs": [
    "Pdf",
    "Csv",
    "Excel"
  ],
  "defaultData": {
    "dateFrom": null,
    "dateTo": null
  }
}
```

### generateReport

Returns the processed output after applying the filters.

=== "React"

```ts
async generateReport(serviceUrl: string, reportCode: string, type: ReportOutputEnum, timezone: string, dateGeneration: string, language: string, formData: any) {
    const res = await axiosApi.post(
        `${serviceUrl}/reportprocess/${reportCode}/generate?outputType=${type.toString()}&timezone=${timezone}&generationDate=${dateGeneration}&language=${language}`,
        formData,
        {
            responseType: 'blob'
        }
    );

    return res;
}
```

Expected return: The report as blob data.
