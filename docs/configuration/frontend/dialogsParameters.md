# Parameters

## AlertPopupParameters

=== "React"

```ts
interface AlertPopupParameters {
    title?: string;
    text: string;
    alertType?: AlertPopupType;
}
```

## ConfirmationPopupParameter

=== "React"

```ts
interface ConfirmationPopupParameters {
    id: string;
    title: string;
    text: string;
    acceptButtonText: string;
    denyButtonText: string;
    cancelButtonText?: string;
}
```

## ConfirmationPopupResult

=== "React"

```ts
interface ConfirmationPopupResult {
    id: string;
    type: ConfirmationPopupResultType;
}
```

## ExecutionParametersRequest

=== "React"

```ts
interface ExecutionParametersRequest {
    processType: string;
    processCode: string;
    processCodePropertyName: string;
    formData: any;
    propertyValue: any;
    propertyName: string;
    onChange: any;
    nodeState: any;
    serviceUrl: string;
}
```

## ExecutionParameters

=== "React"

```ts
interface ExecutionParameters {
    parametersJson: any;
    language: string;
    timezone: string;
    reportOutputType?: string;
}
```
