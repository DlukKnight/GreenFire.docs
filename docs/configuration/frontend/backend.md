# **Backend**

## **Description**

The backend collection reflects the calls that GreenFire does in each of its processes and controls for the needed data and/or operations. By default, each method in each backend property will return empty values. While not all the backends need to be defined for use, there is no point in using a control or a process that needs a data flow without defining them. This means that if you want to use Crud Processes, you need to define the backend for them, but you are not forced to do it for Dashboards if you are not interested in them.
Examples will be provided with axios being used for communication. The reason why all this methods need to be defined is because GreenFire does not enforce how the data communication happens, nor does it enforce the use of any particular implementation. While this is all created to work with a GreenFire's backend implementation, it is possible to define a custom one, or even static data if needed.

=== "React"

```ts
getLayoutMetadata(serviceUrl: string, urlDomain: string, processCode: string): any;
crudProcess: GreenFireCrudProcessBackend;
wizard: GreenFireWizardBackend;
dashboard: GreenFireDashboardBackend;
reportProcess: GreenFireReportProcessBackend;
readOnlyProcess: GreenFireReadOnlyProcessBackend;
datagrid: GreenFireDatagridBackend;
selectControls: GreenFireSelectControlsBackend;
```

### getLayoutMetadata

Retrieves the RootLayout, conditions and validations necessary to correctly render nodes.

=== "React"

```ts
async getLayoutMetadata(serviceUrl: string, urlDomain: string, processCode: string): Promise<any> {
    const res = await axiosApi.get(`${serviceUrl}/${urlDomain}/${processCode}/getlayoutmetadata`);

    return res.data;
}
```

Expected return:

=== "Json"

```json
{
  "rootLayout": {
    "containerType": "Root",
    "nodeType": "Container",
    "children": [
      {
        "$type": "row",
        "containerType": "Row",
        "rowIndex": 0,
        "columnsAmount": 2,
        "nodeType": "Container",
        "children": [
          {
            "$type": "text",
            "type": "Text",
            "minLength": null,
            "maxLength": 50,
            "nodeType": "Control",
            "propertyName": "field",
            "logicalKey": "field",
            "pascalCasePropertyName": "Field",
            "label": "Field",
            "required": true,
            "value": null,
            "key": "b93aa778c1284b17b3e92f25168f2098",
            "visible": true
          },
          {
            "$type": "number",
            "type": "Number",
            "nodeType": "Control",
            "propertyName": "number",
            "logicalKey": "number",
            "pascalCasePropertyName": "Number",
            "label": "Number",
            "required": false,
            "value": null,
            "key": "a5993348932c4929b4b52632ab64404e",
            "visible": true
          }
        ],
        "key": "fd36f2de20e24f01b6c2284465496fff",
        "logicalKey": null,
        "visible": true
      }
    ],
    "key": "85e3a662cf524be1b6ed817619678dbf",
    "logicalKey": null,
    "visible": true
  },
  "layoutConditions": [
    {
      "target": "field",
      "type": "Required",
      "scope": "All",
      "expression": null,
      "value": null
    }
  ],
  "validationRules": [
    {
      "target": "field",
      "expression": {
        "field": "field",
        "operator": "==",
        "value": null,
        "compareField": "number",
        "logicalOperator": null,
        "children": null
      },
      "message": {
        "key": "Fields {0} and {1} cannot have the same value",
        "params": [
          "field",
          "number"
        ]
      }
    }
  ]
}
```
