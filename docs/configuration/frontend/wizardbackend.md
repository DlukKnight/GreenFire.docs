# GreenFireWizardBackend

## **Description**

This is the backend responsible for all [**Wizard processes**](../../components/processes/wizard.md) flow definition and execution.

### getWizardServiceUrl

Retrieves the base Url where the expected process will be hosted.

=== "React"

```ts
async getWizardServiceUrl(wizardCode: string) {
    let url = null;
    const serviceUrlRes = await axiosApi.get(
        `${import.meta.env.BASE_URL}/services/${wizardCode}`
    );
    url = serviceUrlRes.status == 200 ? serviceUrlRes.data : import.meta.env.BASE_URL;

    return url;
}
```

### getWizardDefinition

Retrieves the wizard title, steps, and default data.

=== "React"

```ts
async getWizardDefinition(serviceUrl: string, wizardCode: string) {
    const res = await axiosApi.get(`${serviceUrl}/wizard/${wizardCode}`);

    return res.data;
}
```

Expected return:

=== "Json"

```json
{
  "title": "Wizard title",
  "steps": [
    {
      "stepIndex": 0,
      "name": "First step",
      "layoutMetadata": null,
      "data": null,
      "finished": false
    },
    {
      "stepIndex": 1,
      "name": "Second step",
      "layoutMetadata": null,
      "data": null,
      "finished": false
    }
  ],
  "data": {
    "number1": null,
    "number2": null
  }
}
```

### getWizardStepData

Retrieves the wizard specific step data, including step name, RootLayout, conditions, validations. It will also return the current wizard data.

=== "React"

```ts
async getWizardStepData(serviceUrl: string, wizardCode: string, stepIndex: number, formData: any) {
    const res = await axiosApi.post(`${serviceUrl}/wizard/${wizardCode}/step/${stepIndex}`, formData)

    return res.data;
}
```

Expected return:

=== "Json"

```json
{
  "stepIndex": 0,
  "name": "First step",
  "layoutMetadata": {
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
              "$type": "number",
              "type": "Number",
              "nodeType": "Control",
              "propertyName": "number1",
              "logicalKey": "number1",
              "pascalCasePropertyName": "Number1",
              "label": "Number 1",
              "required": true,
              "value": null,
              "key": "2a5f7f5eaf4d46b289793adaa771f507",
              "visible": true
            }
          ],
          "key": "f2fa208b301a4748851c722dc15a38bc",
          "logicalKey": null,
          "visible": true
        }
      ],
      "key": "15228168dd9b42aeb07ac6645174f01c",
      "logicalKey": null,
      "visible": true
    },
    "layoutConditions": [],
    "validationRules": []    
  },
  "data": {
    "number1": null,
    "number2": null
  },
  "finished": false
}
```

### getWizardNextStep

Retrieves the wizard next step data, including step name, RootLayout, conditions, validations. It will also return the current wizard data. If the current step when consulting this endpoint is the last, it will instead return the same step, but with the *layout metadata* null and *finished* with **true** value.

=== "React"

```ts
    async getWizardNextStep(serviceUrl: string, wizardCode: string, stepIndex: number, formData: any) {
        const res = await axiosApi.post(
            `${serviceUrl}/wizard/${wizardCode}/step/${stepIndex}/next`,
            formData
        );

        return res.data;
    }
```

Expected return:

=== "Json"

```json
{
  "stepIndex": 1,
  "name": "Second step",
  "layoutMetadata": {
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
              "$type": "number",
              "type": "Number",
              "nodeType": "Control",
              "propertyName": "number2",
              "logicalKey": "number2",
              "pascalCasePropertyName": "Number2",
              "label": "Number 2",
              "required": true,
              "value": null,
              "key": "2a5f7f5eaf4d46b289793adaa771f507",
              "visible": true
            }
          ],
          "key": "f2fa208b301a4748851c722dc15a38bc",
          "logicalKey": null,
          "visible": true
        }
      ],
      "key": "15228168dd9b42aeb07ac6645174f01c",
      "logicalKey": null,
      "visible": true
    },
    "layoutConditions": [],
    "validationRules": []    
  },
  "data": {
    "number1": 5,
    "number2": null
  },
  "finished": false
}
```

### getWizardPreviousStep

Retrieves the wizard previous step data, including step name, RootLayout, conditions, validations. It will also return the current wizard data. If the current step when consulting this endpoint is the last, it will instead return the same step, but with the *layout metadata* null and *finished* with **true** value.

=== "React"

```ts
async getWizardPreviousStep(serviceUrl: string, wizardCode: string, stepIndex: number, formData: any) {
    const res = await axiosApi.post(
        `${serviceUrl}/wizard/${wizardCode}/step/${stepIndex}/back`,
        formData
    );

    return res.data;
}
```

Expected return:

=== "Json"

```json
{
  "stepIndex": 0,
  "name": "First step",
  "layoutMetadata": {
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
              "$type": "number",
              "type": "Number",
              "nodeType": "Control",
              "propertyName": "number1",
              "logicalKey": "number1",
              "pascalCasePropertyName": "Number1",
              "label": "Number 1",
              "required": true,
              "value": null,
              "key": "2a5f7f5eaf4d46b289793adaa771f507",
              "visible": true
            }
          ],
          "key": "f2fa208b301a4748851c722dc15a38bc",
          "logicalKey": null,
          "visible": true
        }
      ],
      "key": "15228168dd9b42aeb07ac6645174f01c",
      "logicalKey": null,
      "visible": true
    },
    "layoutConditions": [],
    "validationRules": []    
  },
  "data": {
    "number1": 5,
    "number2": null
  },
  "finished": false
}
```

### executeWizardFinish

Executes the wizard finish method, completing the operation and getting the data sent, the result status with messages and optionally, messages for [**scheduler**](../../definitions/integrations/scheduledtasks.md) report and a [**RootLayout**](../../components/containers/layout/rootlayoutcontainer.md) for representing the result with controls.

=== "React"

```ts
async executeWizardFinish(serviceUrl: string, wizardCode: string, formData: any) {
    const res = await axiosApi.post(
        `${serviceUrl}/wizard/${wizardCode}/finish`,
        formData
    );

    return res.data;
}
```

Expected return:

=== "Json"

```json
{
  "data": {
    "number1": 5,
    "number2": 1
  },
  "success": true,
  "messages": [
    "Success"
  ],
  "schedulerReport": [
    "Number 1 is 5",
    "Number 2 is 1",
    "",
    "Result is 4"
  ],
  "layout": {
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
            "$type": "number1",
            "type": "Number1",
            "nodeType": "Control",
            "propertyName": "number1",
            "logicalKey": "number1",
            "pascalCasePropertyName": "Number1",
            "label": "Number 1",
            "required": false,
            "value": null,
            "key": "c5c4c12c93f54736b0b3abe1617c2af3",
            "visible": true
          },
          {
            "$type": "number2",
            "type": "Number2",
            "nodeType": "Control",
            "propertyName": "number2",
            "logicalKey": "number2",
            "pascalCasePropertyName": "Number2",
            "label": "Number 2",
            "required": false,
            "value": null,
            "key": "c5c4c12c93f54736b0b3abe1617c2af3",
            "visible": true
          }
        ],
        "key": "ce18f9d2bdb447c8a1bfd9c752a0a2c2",
        "logicalKey": null,
        "visible": true
      },
      {
        "$type": "row",
        "containerType": "Row",
        "rowIndex": 2,
        "columnsAmount": 3,
        "nodeType": "Container",
        "children": [
          {
            "$type": "label",
            "contentType": "Label",
            "text": "Result is 4",
            "logicalKey": "infoLabel",
            "nodeType": "Content",
            "key": "6995cabb802c48d395251a53c40a5f74",
            "visible": true
          }
        ],
        "key": "18b7913fd83d412d929467de05d43c40",
        "logicalKey": null,
        "visible": true
      }
    ],
    "key": "c1ddb70017864718bf5fcc09514631e4",
    "logicalKey": null,
    "visible": true
  }
}
```
