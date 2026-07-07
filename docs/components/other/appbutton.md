# **AppButton**

## **Description**

Renders a configurable button, used in many other GreenFire components.

## **Properties**

### variant

The visual style of the button. The possible options are as follows:

* "primary": A green colored button.
* "secondary": A blue colored button.
* "danger": A red colored button.
* "colorless": A colorless gray colored button.

### Icon

Icon to be shown at the side of the button's text. It supports any Lucide icon.

### iconPosition

The location of the icon in relation to the text. The possible options are as follows:

* "left": Icon will be located at the left of the text.
* "right": Icon will be located at the right of the text.

### minWidth

The minimum width of the button.

### maxWidth

The maximum width of the button.

### loading

It indicates the button that it should show the loading circle animation, communicating to the user that the button is waiting for some operation to finish. The button will be disabled under this condition.

### disabled

When true, the button is disabled and cannot be interacted. It also changes the visual style to reflect it.

### onClick

Method that gets executed when the button gets clicked.

## Example

In this example, the button navigates to the [**Crud process**](../processes/crudprocess.md) that owns the **"test"** code.

=== "ASP.NET"

```csharp
<AppButton variant="primary"
    Icon={ArrowRightCircle}
    iconPosition="left"
    maxWidth={20}
    loading={false}
    disabled={false}
    onClick={() => {
        navigate("/crudprocess/test");
    }}
>
    Go to test CRUD
</AppButton >
```
