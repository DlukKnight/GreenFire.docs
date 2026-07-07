# **Select options builders**

## **Description**

Generates collections of SelectOption objects that can be assigned to a [**SelectControl**](../../components/controls/selectcontrol.md). While options can always be created manually, these helpers simplify common scenarios such as enum-based lists and numeric ranges.

Every helper returns a `List<SelectOption>`, where each option contains the value submitted by the control together with the text displayed to the user.

## **FromEnum**

=== "ASP.NET"

```csharp
public static List<SelectOption> FromEnum<TEnum>() where TEnum : Enum
```

Builds options directly from an **enum**. If you are using the **DisplayAttribute** provided by .NET, it uses the value of the *DisplayAttribute.Name* property, otherwise, it will use the option's name directly.

For example, an enum like this one:

=== "ASP.NET"

```csharp
public enum Test
{
    [Display(Name = "First option")]
    Option1,

    Option2
};

List<SelectOption> options = SelectOptionsHelper.FromEnum<Test>();
```

Will generate two options, the first one using the **DisplayAttribute**'s name:

- First option
- Option2

## **FromEnumFullPath**

=== "ASP.NET"

```csharp
public static List<SelectOption> FromEnumFullPath<TEnum>() where TEnum : Enum
```

Builds options directly from an **enum**, returning the enum's name as prefix. This is helpful for localization purposes, since in that scenario, it will probably be necessary to identify the enum by a special formatted value to apply a dictionary translation.

Each option text is generated in the format `EnumName.MemberName`.

For example, an enum like this one:

=== "ASP.NET"

```csharp
public enum Test
{
    Option1,
    Option2
};

List<SelectOption> options = SelectOptionsHelper.FromEnumFullPath<Test>();
```

Will generate two options:

- Test.Option1
- Test.Option2

!!! Note
    While a translation could be generated from the option's name without a prefix, this will future proof cases where two enums could have the same options, but in another language they may have different translations. We strongly recommend using this method if localization is present.

## **FromNumberRange**

=== "ASP.NET"

```csharp
public static List<SelectOption> FromNumberRange(int minValue, int maxValue)
```

Builds an option list that contains all numbers between the range.

For example:

=== "ASP.NET"

```csharp
List<SelectOption> options = SelectOptionsHelper.FromNumberRange(1, 5);
```

This will generate:

- 1
- 2
- 3
- 4
- 5
