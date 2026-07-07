# **PasswordControlComponent**

## **Description**

Renders a standard single-line text input with obfuscated characters.

This control inherits all properties from **BaseControl**. The metadata below only documents the properties specific to **PasswordControl**.

## **PasswordControl metadata**

=== "React"

```ts
interface PasswordControl extends BaseControl {
  autoComplete: string;
  confirmsOtherPasswordName: string;
};
```

### autoComplete

Disables the browser's password autocomplete feature, stopping it from auto-filling the control when a password value has been stored in the browser. This is specially useful for when the control is expecting a fresh new password.

### confirmsOtherPasswordName

Points to another property name that will be used to check if passwords match. It's meant to be used in conjunction with another password control.

Once this property is set, the control will show a validation error if the passwords are different.

## Example

In the following example, two password controls are created, with the first one being mandatory to confirm the first one by introducing the same password in both.

=== "ASP.NET"

```csharp
row.AddControlToRow(
    e => e.NewPassword, 
    new PasswordControl()
    { 
        AutoComplete = false 
    });

row.AddControlToRow(e => 
    e.NewPasswordConfirm, 
    new PasswordControl() 
    { 
        AutoComplete = false, 
        ConfirmsOtherPasswordName = "NewPassword"
    });
```
