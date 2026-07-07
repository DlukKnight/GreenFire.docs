# GreenFireDashboardBackend

## **Description**

This is the backend responsible for all [**Dashboard processes**](../../components/processes/dashboard.md) filters and widgets data interaction.

### getDashboardServiceUrl

Retrieves the base Url where the expected process will be hosted.

=== "React"

```ts
async getDashboardServiceUrl(dashboardCode: string) {
    let url = null;
    const serviceUrlRes = await axiosApi.get(
        `${import.meta.env.BASE_URL}/services/${dashboardCode}`
    );
    url = serviceUrlRes.status == 200 ? serviceUrlRes.data : import.meta.env.BASE_URL;

    return url;
}
```

### getDashboardDefinition

Retrieves dashboard title and default filters data.

=== "React"

```ts
async getDashboardDefinition(serviceUrl: string, dashboardCode: string) {
    const res = await axiosApi.get(`${serviceUrl}/dashboard/${dashboardCode}`);

    return res.data;
}
```

Expected return:

=== "Json"

```json
{
  "title": "Dashboard title",
  "filtersData": {
    "dateFrom": null,
    "dateTo": null
  }
}
```

### getDashboardLayout

Retrieves the rootLayout used for rendering the widgets.

=== "React"

```ts
async getDashboardLayout(serviceUrl: string, dashboardCode: string) {
    const res = await axiosApi.get(`${serviceUrl}/dashboard/${dashboardCode}/getWidgets`);

    return res.data;
}
```

Expected return:

=== "Json"

```json
{
  "containerType": "Root",
  "nodeType": "Container",
  "children": [    
    {
      "$type": "row",
      "containerType": "Row",
      "rowIndex": 1,
      "columnsAmount": 4,
      "nodeType": "Container",
      "children": [
        {
          "$type": "metric",
          "widgetType": "Metric",
          "title": "Metric widget",
          "width": 350,
          "height": 75,
          "iconColor": null,
          "icon": null,
          "nodeType": "Widget",
          "code": "metricWidget",
          "updateTimeInSeconds": 5,
          "key": "f3f7bc6f023949b785aaa4f4c08f9d89",
          "visible": true
        }        
      ],
      "key": "5e0cf0aa74f5474eafe353041bf45e28",
      "visible": true
    },
    {
      "$type": "row",
      "containerType": "Row",
      "rowIndex": 2,
      "columnsAmount": 4,
      "nodeType": "Container",
      "children": [
        {
          "$type": "chart",
          "widgetType": "Chart",
          "chartType": "Bar",
          "title": "Chart widget",
          "showLegend": true,
          "showGrid": true,
          "showTooltip": true,
          "stacked": false,
          "width": 350,
          "height": 380,
          "nodeType": "Widget",
          "code": "chartWidget",
          "updateTimeInSeconds": 5,
          "key": "826b330d5fd54683ac8bb6f3d04390d6",
          "visible": true
        }
      ],
      "key": "dcbc351d79c44331915be0cc6e094baf",
      "visible": true
    }
  ],
  "key": "cc2dc5b562bc498d911dbdab26e752df",
  "visible": true
}
```

### getWidgetData

Retrieves the data necessary to render information for a specific widget. The filters data is sent in the *dashboardState* property.

=== "React"

```ts
async getWidgetData(serviceUrl: string, widgetCode: string, dashboardState: any): Promise<any> {
    const res = await axiosApi.post(`${serviceUrl}/widget/${widgetCode}/get`, dashboardState);

    return res.data;
}
```

Expected return:

=== "Json"

```json
{
  "labels": [
    "31/05/2026",
    "05/06/2026",
    "06/06/2026"    
  ],
  "series": [
    {
      "name": "Value",
      "values": [
        103,
        95,
        71        
      ],
      "color": null
    }
  ]
}
```
