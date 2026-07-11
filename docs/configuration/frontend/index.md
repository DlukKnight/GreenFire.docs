# **Overview**

## **Properties**

GreenFire supports several configurations through the GreenFireProvider context. These are as follows:

=== "React"

```ts
localization?: Partial<GreenFireLocalization>,
themeMode?: "light" | "dark" | "system";
backend?: Partial<GreenFireBackend>;
dialogs?: Partial<GreenFireDialogs>;
```

!!! Warning
    Some themes variables won't work unless the correct theme class is added to the root document. Refer to [**applying a theme**](./themedefinitions.md#applying-a-theme) for more information.
