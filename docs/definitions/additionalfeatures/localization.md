# **Localization**

## **Description**

GreenFire offers a minimal, but effective optional localization feature, composed with a controller and a service, which can retrieve and return Json dictionaries.

The controller and service are automatically registered by GreenFire. No additional configuration is required.

## **Endpoint**

### Get

The controller exposes a single endpoint:

`api/localization/get/{language}`

With the parameter in the Url signaling the language used. An example of use would be:

`http://localhost:3000/api/localization/get/en`

Used to retrieve all the english dictionaries.

Keep in mind that this method will return a Json composed of all the terms described in each dictionary for the language. See the **GreenFireDictionaryService** for more information.

## **GreenFireDictionaryService**

=== "ASP.NET"

```csharp
public class GreenFireDictionaryService(GreenFireOptions options) : IGreenFireDictionaryService
```

This service, meant to be used by injection, is able to retrieve any dictionaries saved as Json format in a specific folder recursively.

### Methods

#### GetLocalization

=== "ASP.NET"

```csharp
public LocalizationData GetLocalization(string language)
```

This method will return either the dictionaries or a cache of them. The first time a language is requested, its dictionaries are loaded and cached for subsequent requests, so, if any dictionary gets modified, the API will require a restart to proper load the dictionaries again.

The default folder where this method will look for the dictionaries is a `Localization` folder inside the root of the application. If desired, this path could be customized. See the [**configuration page**](../../configuration/backend/index.md) for more information.

The dictionaries get loaded recursively, meaning that you are free to use any type of folder structure and divide the terms into as many files as you'd like. The only rule is that each language should be the first folder defined inside the `Localization` folder, and inside it, your folder structure. For example:

```treeview
* Localization/
    ** en/
        *** Validation.json
        *** System.json
        *** Components/
            **** Grid.json
        *** Processes/
            **** Wizard.json
    ** es/
        *** Validation.json
        *** System.json
        *** Components/
            **** Grid.json
        *** Processes/
            **** Wizard.json
```

!!! Note
    All `.json` files are recursively discovered and merged into a single dictionary. If two files contain the same key, the last loaded value overrides the previous one.

Then the method return a simple object, with the requested dictionary.

=== "ASP.NET"

```csharp
public class LocalizationData
{
    public required Dictionary<string, string> Dictionary { get; init; }
}
```

## Example

By using both the controller and the service provided, a custom service made for translating terms could be implemented easily, resulting in something like this:

=== "ASP.NET"

```csharp
public class LocalizationService(IGreenFireDictionaryService greenFireDictionaryService) : ILocalizationService
{
    public string Translate(string language, string key)
    {
        Dictionary<string, string> dictionary = greenFireDictionaryService.GetLocalization(language).Dictionary;

        return dictionary.GetValueOrDefault(key, key);
    }

    public string Format(string language, string key, params object?[] parameters)
    {
        string template = Translate(language, key);

        string result = template;

        for (int i = 0; i < parameters.Length; i++)
        {
            result = result.Replace(
                $"{{{i}}}",
                TranslateTermOrValue(language, parameters[i])
            );
        }

        return result;
    }

    public string TranslateValue(string language, object? value)
    {
        if (value is null)
            return string.Empty;

        if (value is not string text)
            return value.ToString()!;

        if (!text.StartsWith("@@"))
            return text;

        string[] split = text.Split('|');

        return Format(language, split[0], [.. split.Skip(1).Cast<object?>()]);
    }

    public string TranslateTermOrValue(string language, object? value)
    {
        if (value is null)
            return string.Empty;

        if (value is not string text)
            return value.ToString()!;

        if (!text.StartsWith("@@"))
            return Translate(language, text);

        return TranslateValue(language, text);
    }
}
```
