# **CrudProcess**

## **Description**

Renders a complete Crud interface, including the main data grid, toolbar, validation, layouts and entity operations

## **Features**

A CrudProcess automatically provides:

* Entity browsing with server-side paging
* Dynamic layouts generated from metadata
* Validation
* Runtime conditions
* Create, edit, duplicate and delete operations
* Localization
* [**Audit**](../../definitions/integrations/audit.md) support (when enabled)
* Automatic toolbar generation

## **Parameters**

### processCode

Identifies the definition for the process in the backend.

### id

If this parameter is sent with a value, the Crud Process will immediately go to *Edit* mode, positioned in the record that owns said Id.

### onNormalizeRoute

Allows the application to normalize the browser URL after the process changes state (for example, removing an entity identifier after opening a record).

### onError

If any error happens during any backend call, after displaying errors, this method will be called, allowing for any type of action post-error.

## **Example**

In this example:

* The process code and optional entity identifier are obtained from the route.
* If the process opens directly in edit mode, the entity identifier is removed from the URL once the record is loaded.
* If a backend error occurs, the user is redirected to a custom error page.

=== "React"

```ts
import { useNavigate, useParams } from "react-router-dom";
import { CrudProcess } from "@greenfire/react";

export function CrudProcessPage() {
    const { code, id } = useParams<{ code: string, id: string }>();
    const navigate = useNavigate();

    return <CrudProcess
                processCode={code ?? ""}
                id={id ?? undefined}
                onNormalizeRoute={() => navigate(`/process/${code}`, { replace: true })}
                onError={() => navigate("/error")}
            />
}
```
