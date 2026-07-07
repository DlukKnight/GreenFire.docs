# **ParametersMessage**

## **Description**

The **ParametersMessage** class allows for the creation of messages that can replace placeholders by values sent in parameters, enabling the definition of dynamic texts meant to display dynamic data or for localization purposes.

## **Definition**

=== "ASP.NET"

```csharp
public class ParametersMessage
{
    public required string Key { get; set; }
    public List<string> Params { get; set; } = [];
}
```

The class is really simple to use. The **Key** property will contain the text, formatted with placeholders, like this example:

`Name is {0} and value is {1}`

The **Params** collection contains the values that replace each placeholder in order.

## **Examples**

=== "ASP.NET"

```csharp
// This example generates a simple message without parameters, the equivalent to sending a string
ParametersMessage SimpleMessage = new() {
    Key = "This is a message without parameters"
};

// This example generates a message with a single placeholder, replacing the {0} for the value of item.Price
ParametersMessage ParameterMessage = new() {
    Key = "Value: {0}"
    Params =
    [
        item.Price
    ]
};

// This example generates a message that will be translated at the frontend, transforming the "Validation.FieldsCannotBeEqual"
// in "{0} and {1} cannot be equal" and then replacing the placeholders with the parameters after transforming each parameter
// into the wanted display name. This is one example of localization, where sending formatted messages and parameters allows
// for multiple languages to be possible.
ParametersMessage Description = new() {
    Key = "Validation.FieldsCannotBeEqual",
    Params =
    [
        $"{nameof(TestEntity)}.{nameof(TestEntity.Name)}",
        $"{nameof(TestEntity)}.{nameof(TestEntity.Code)}"
    ]
};
```
