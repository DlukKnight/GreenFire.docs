# **ReadOnlyProcess**

## **Description**

Renders an entity Read-only interface, including the main data grid, toolbar, validation, layouts, with filters.

## **Features**

A ReadOnlyProcess automatically provides:

* Entity browsing with server-side paging
* Dynamic layouts generated from metadata
* Validation
* Runtime conditions
* Localization
* Automatic toolbar generation

## **Parameters**

### readOnlyProcessCode

Identifies the definition for the process in the backend.

### onError

If any error happens during any backend call, after displaying errors, this method will be called, allowing for any type of action post-error.

## **Example**

In this example:

* The process code is obtained from the route.
* If a backend error occurs, the user is redirected to a custom error page.

=== "React"

```ts
import { ReadOnlyProcess } from "@greenfire/react";
import { useNavigate, useParams } from "react-router-dom";

function ReadOnlyProcessPage() {
    const { code } = useParams<{ code: string }>();
    const navigate = useNavigate();

    return (<>
        <ReadOnlyProcess
            readOnlyProcessCode={code ?? ""}
            onError={() => navigate("/error")}
        />
    </>
    );
}

export default ReadOnlyProcessPage;
```
