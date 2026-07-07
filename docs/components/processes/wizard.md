# **Wizard**

## **Description**

Renders a complete Wizard interface, including all its steps, toolbar, validation and layouts.

## **Features**

A Wizard automatically provides:

* Step flow with back and next navigation
* Dynamic layouts generated from metadata
* Validation
* Runtime conditions
* Localization
* Automatic toolbar generation

## **Parameters**

### wizardCode

Identifies the definition for the Wizard in the backend.

### initialData

Allows for setting an initial Wizard state, basically loading the Wizard with all its steps properties already with particular values. Useful if the execution data of the Wizard is being saved and the idea is later to have the Wizard rerun using the same data.

### onFinish

If this method is sent, the Wizard won't execute its normal Finish state, and will instead return its execution data as form data. Useful method to use the Wizard to create its execution parameters rather than doing the execution itself.

### onError

If any error happens during any backend call, after displaying errors, this method will be called, allowing for any type of action post-error.

## **Example**

In this example:

* The wizard code is obtained from the route.
* If a backend error occurs, the user is redirected to a custom error page.

=== "React"

```ts
import { Wizard } from "@greenfire/react";
import { useNavigate, useParams } from "react-router-dom";

function WizardPage() {
    const { code } = useParams();
    const navigate = useNavigate();

    return <Wizard
                wizardCode={code ?? ""}
                onError={() => navigate("/error")}
            />
}

export default WizardPage;
```

A second example is provided where:

* The Wizard code is obtained from the page/component rendering it.
* Initial data may be loaded with the **getInitialData** method.
* On finish, the Wizard won't execute its operations, but instead will return its execution data. The **handleResult** then persists it calling the service allocated in **serviceUrl**.

=== "React"

```ts
<Wizard
    wizardCode={code}
    initialData={getInitialData()}
    onFinish={async (data) => {
        await handleResult(serviceUrl, data)
    }}
/>
```
