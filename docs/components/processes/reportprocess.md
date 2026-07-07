# **ReportProcess**

## **Description**

Renders a complete Report generation interface, including toolbar, validation and layouts, with filters.

## **Features**

A ReportProcess automatically provides:

* Filtering options through controls layout
* Dynamic layouts generated from metadata
* Validation
* Runtime conditions
* Localization
* Automatic toolbar generation

## **Parameters**

### reportCode

Identifies the definition for the Report process in the backend.

### initialData

Allows for setting an initial Report process state, basically loading the process with all its filter's data already with particular values. Useful if the filter's data of the process is being saved and the idea is later to have the process reload with the same data.

### onGenerate

If this method is sent, the Report process won't generate the file, and will instead return its generation data as form data, as well as the file type that was selected. Useful method to use the process to create its generation parameters rather than doing the generation itself.

### onError

If any error happens during any backend call, after displaying errors, this method will be called, allowing for any type of action post-error.

## **Example**

In this example:

* The report code is obtained from the route.
* If a backend error occurs, the user is redirected to a custom error page.

=== "React"

```ts
import { ReportProcess } from "@greenfire/react";
import { useNavigate, useParams } from "react-router-dom";

function ReportProcessPage() {
    const { code } = useParams();
    const navigate = useNavigate();

    return <ReportProcess
                reportCode={code ?? ""}
                onError={() => navigate("/error")}
            />    
}

export default ReportProcessPage;
```

A second example is provided where:

* The report code is obtained from the page/component rendering it.
* Initial data may be loaded with the **getInitialData** method.
* On generation, the process won't generate the file, but instead will return its filter's data and selected file type. The **handleResult** then persists it calling the service allocated in **serviceUrl**.

=== "React"

```ts
<ReportProcess
    reportCode={code}
    initialData={getInitialData()}
    onGenerate={async (data, type) => {
        await handleResult(data, type)
    }}
/>
```
