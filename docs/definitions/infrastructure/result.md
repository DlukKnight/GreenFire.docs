# **Result**

## **Description**

GreenFire adopts the **Result pattern** as its primary mechanism for representing operation outcomes, applying it with its own implementation and using it whenever is possible instead of exceptions. While there are a few exceptions in some places, these are minimal, and all the time an operation is executed, a **Result** can be expected.

The **Result** class custom implementation, and everything related to it, is available to be used. While each component in Greenfire will use it, outside of it, there is free reign to implement other types of patterns.

**Result** should always be preferred over throwing exceptions for expected business outcomes.

## Result class

=== "ASP.NET"

```csharp
public class Result
{
    public bool IsSuccess { get; }

    public bool IsFailure => !IsSuccess;

    public Error? Error { get; }

    public static implicit operator Result(Error error) => new(error);

    public static Result Success() => new();

    public static Result Failure(Error error) => new(error);

    public static Result CombineErrors(params Result[] results);
}

public sealed class Result<TValue> : Result, IResult
{
    public TValue Value => IsSuccess ? _value! : throw new InvalidOperationException("Value can not be accessed when IsSuccess is false");
    
    public static Result<TValue> Success(TValue value) => new(value);

    public new static Result<TValue> Failure(Error error) => new(error);
}
```

This class is easy to use and implement, by just using the **Success** or **Failure** methods to return a new Result with the adequate format. The **IsSuccess** and **IsFailure** properties are later used to evaluate the Result.

The **CombineErrors** method is useful if several operations happen (like, for example, validations) to combine all Results into a single one that will carry one **Error** that will have all the codes and descriptions in one place.

If the Result is successful, and a value is expected, it can be accessed with the **Value** property.

## Error class

=== "ASP.NET"

```csharp
public class Error
{
    public string Code { get; }

    public IReadOnlyList<string> Descriptions { get; }

    public ErrorType ErrorType { get; }

    public static Error Failure(string code, IReadOnlyList<string> descriptions) =>
        new(code, descriptions, ErrorType.Failure);

    public static Error NotFound(string code, IReadOnlyList<string> descriptions) =>
        new(code, descriptions, ErrorType.NotFound);

    public static Error Validation(string code, IReadOnlyList<string> descriptions) =>
        new(code, descriptions, ErrorType.Validation);

    public static Error Conflict(string code, IReadOnlyList<string> descriptions) =>
        new(code, descriptions, ErrorType.Conflict);

    public static Error AccessUnAuthorized(string code, IReadOnlyList<string> descriptions) =>
        new(code, descriptions, ErrorType.AccessUnAuthorized);

    public static Error AccessForbidden(string code, IReadOnlyList<string> descriptions) =>
        new(code, descriptions, ErrorType.AccessForbidden);

    public static Error Combine(params List<Error> errors)
}
```

As with the **Result** class, the use is very similar, by just creating a new Error through all the available methods. The **Descriptions** is meant to be flexible enough to send several messages if needed.

Error can be combined with other Errors to generate a single one, but most of the times this will be done with the **CombineErrors** of **Result**.

**ErrorType** is primarily used by GreenFire to determine how the frontend should react to an error.

## **Example**

In the next example, the result is negative, because a duplicated entity was trying to be persisted, so an **Error** of type *Conflict* is returned. Later on the flow, this result is checked to see if it failed, and when that is the case, a console log is printed, showing the error code and the descriptions.

=== "ASP.NET"

```csharp
Result<MyEntity> result = Result<MyEntity>.Failure(Error.Conflict("Add_error", ["Duplicated entity"]));

...

if(result.IsFailure)
    Console.Write($"Error code: {result.Error.Code} - Message: {string.Join(", ",result.Error.Descriptions)}")
```
