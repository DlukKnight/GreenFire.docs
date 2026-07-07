# **Dashboard**

## **Description**

Renders a complete Dashboard interface, including toolbar, validation and layouts, with both filters and widgets.

## **Features**

A Dashboard automatically provides:

* Filtering options through controls layout
* Data shown by several widget types which refresh over time.
* Dynamic layouts generated from metadata
* Validation
* Runtime conditions
* Localization
* Automatic toolbar generation

## **Parameters**

### dashboardCode

Identifies the definition for the Dashboard in the backend.

### onError

If any error happens during any backend call, after displaying errors, this method will be called, allowing for any type of action post-error.

## **Example**

In this example:

* The dashboard code is obtained from the route.
* If a backend error occurs, the user is redirected to a custom error page.

=== "React"

```ts
import { useNavigate, useParams } from "react-router-dom";
import { Dashboard } from "@greenfire/react";

export function DashboardPage() {
    const { code } = useParams<{ code: string }>();
    const navigate = useNavigate();

    return <Dashboard
                dashboardCode={code ?? ""}
                onError={() => navigate("/error")}
            />
}
```
