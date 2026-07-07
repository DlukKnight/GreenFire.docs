# **Validations builders**

## **Description**

Validations builders builders are static helper classes used to create validation metadata in a concise and readable way. They are typically used in methods such as [**GetValidationRules**](../processes/crudprocess.md#getvalidationrules).

## **PropertyValidationBuilder**

=== "ASP.NET"

```csharp
public static class PropertyValidationBuilder<TEntity>
```

Builds validation rules for a property.

There is only one method with two overloads:

=== "ASP.NET"

```csharp
public static void AddPropertyValidation<TPropertyTarget>(List<ValidationRule> list, Expression<Func<TEntity, TPropertyTarget>> propertyExpression, Expression<Func<TEntity, bool>>? validationExpression, string message)

public static void AddPropertyValidation<TPropertyTarget>(List<ValidationRule> list, Expression<Func<TEntity, TPropertyTarget>> propertyExpression, Expression<Func<TEntity, bool>>? validationExpression, ParametersMessage message)
```

The parameters used are as follows:

- `list`: This is the list that will hold all validations related to its container (normally a [**RootLayout**](../../components/containers/layout/rootlayoutcontainer.md))
- `propertyExpression`: Points to a property of **TEntity** through a lambda expression. This allows for an easy definition on which property to bind to the validation. If the desired field to apply an expression is inside a detail, then this property expression has to be applied using the **First()** method, to be able to correctly point to the property inside the detail. The **First()** method will be discarded internally, it's just a way to write the expression. for example:

    `p => p.Details.First().NumericValue`

- `validationExpression`: The expression that must evaluate to true for the validation to be triggered, sent through a lambda expression. Validations support equals, greater and lower than comparisons, as well as OR and AND logical operators.
- `message`: This parameter could be sent as a plain **string** or using the [**ParametersMessage**](../coreconcepts/parametersmessage.md) class, which can render the error message while replacing placeholders for parameters, making it very flexible.

!!! Note
    **Multiple validations** may target the same property. GreenFire evaluates each validation independently.

The next examples show the same validation for both entity and detail, signaling that the Name and Code in each should be different:

=== "ASP.NET"

```csharp
protected override async Task<List<ValidationRule>> GetValidationRules()
{

    // This example does impede the same value sent to Name and Code fields. A ParametersMessage is sent contemplating the case where these fields change name.

    List<ValidationRule> validationRules = [];

    PropertyValidationBuilder<TestEntity>.AddPropertyValidation(validationRules, p => p.Name, p => p.Name == p.Code, new ParametersMessage()
    {
        Key = "{0} and {1} cannot be equal",
        Params =
        [
            $"{nameof(TestEntity.Name)}",
            $"{nameof(TestEntity.Code)}"
        ]
    });

    // This example is the same as the one above, but for a detail inside the same entity.

    PropertyValidationBuilder<TestEntity>.AddPropertyValidation(validationRules, p => p.Details.First().Name, p => p.Details.First().Name == p.Details.First().Code, new ParametersMessage()
    {
        Key = "{0} and {1} cannot be equal",
        Params =
        [
            $"{nameof(TestDetail.Name)}",
            $"{nameof(TestDetail.Code)}"
        ]
    });

    // This example checks if the Price field exceeded the MaxPrice field. Since these property names are unlikely to change, a simple string message is sufficient.

    PropertyValidationBuilder<TestEntity>.AddPropertyValidation(validationRules, p => p.Price, p => p.Price > p.MaxPrice, "Price value cannot exceed max price");

    return validationRules;
}
```
