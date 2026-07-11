# **Theme definitions**

## **Description**

GreenFire exposes its visual styling through a collection of **CSS variables** (custom properties). All GreenFire components consume these variables internally, allowing applications to override them and create custom themes without modifying the framework's styles. They are imported automatically and are used by all the components that compose the framework.

By adding this variables to any global theme, it is possible to override GreenFire's default definitions and use custom ones, creating any custom theme version. Keep in mind that most of this variables are divided by what type of theme is applied, meaning that they are present with two values, one for **light** and one for **dark**.

!!! Note
    All variables and classes are prefixed with **`greenfire`** to avoid collisions with your application's own styles.

## **Applying a theme**

Most of the variables will work without needing to do anything, but there are groups of them that depend on the theme mode selected. For this to work, the **root** document should have the correct class applied to it. Fortunately, this is really easy to do in any type of implementation, for example:

=== "React"

```ts
if (selectedTheme === ThemeMode.Light) {
    document.documentElement.classList.add("theme-light");
}

if (selectedTheme === ThemeMode.Dark) {
    document.documentElement.classList.add("theme-dark");
}
```

Once this class is added, the variables will get applied.

If you don't want to use any theme in particular, our recommendation is to just chose one and add it to the **root** at your applications entry point. For example, choosing **light**:

=== "React"

```ts
document.documentElement.classList.add("theme-light");
```

## **Variables**

For ease of recognition and use, all GreenFire variables start with the prefix **`--greenfire`**.

GreenFire defines a default set of variables. Any variable can be overridden later in your application's global stylesheet.

For example:

```css
:root.theme-light {
    --greenfire-color-accent: 220 38 38;
}
```

!!! Note
    Variables defined under **:root** are shared across all themes, while variables inside **:root.theme-light** and **:root.theme-dark** change depending on the selected theme.

This is the list of all the available theme variables:

```css
:root {
  --greenfire-color-stripe-green: 16 185 129;
  --greenfire-color-stripe-red: 239 68 68;
  --greenfire-color-stripe-blue: 59 130 246;
  --greenfire-color-stripe-violet: 139 92 246;
  --greenfire-color-stripe-yellow: 245 158 11;

  --greenfire-color-widget-table-header-font: 140 140 140;
  --greenfire-color-widget-table-header-bg: 249 249 249;
  --greenfire-color-widget-table-header-bg-odd: 246 247 249;

  --greenfire-color-widget-badge-success: 81 143 95;
  --greenfire-color-widget-badge-success-bg: 200 255 197;

  --greenfire-color-widget-badge-danger: 181 70 86;
  --greenfire-color-widget-badge-danger-bg: 254 203 200;

  --greenfire-color-button-disabled: 136 136 136;
  --greenfire-color-button-font: 255 255 255;

  --greenfire-color-remove: 192 57 43;
  --greenfire-color-remove-hover: 231 76 60;
  --greenfire-color-remove-disabled: 128 128 128;
  --greenfire-color-remove-focus: 231 76 60;

  --greenfire-color-detail-add-button: 46 204 113;
  --greenfire-color-detail-add-button-hover: 39 174 96;
  --greenfire-color-detail-add-button-text: 255 255 255;

  --greenfire-color-accent-text: 255 255 255;

  --greenfire-focus-ring-color: 0 123 255;
  --greenfire-focus-ring-opacity: 0.25;
  --greenfire-focus-ring-size: 0.2rem;

  --greenfire-focus-outline-width: 2px;

  --greenfire-shadow-button-hover: 0 4px 12px rgba(0, 0, 0, 0.15);
  --greenfire-shadow-button-active: 0 2px 6px rgba(0, 0, 0, 0.1);
  --greenfire-shadow-dropdown: 0 2px 8px rgba(0, 0, 0, 0.1);
  --greenfire-shadow-popup: 0 6px 20px rgba(0 0 0 / 12%);
  --greenfire-shadow-checkbox-list: 0 1px 4px rgb(0 0 0 / 11%), 0 0 4px rgb(18 25 97 / 4%);

  --greenfire-radius-small: 4px;
  --greenfire-radius-medium: 6px;
  --greenfire-radius-large: 8px;
  --greenfire-radius-xl: 12px;

  --greenfire-control-padding: 10px;
  --greenfire-control-font-size: 16px;
  --greenfire-control-border-width: 1px;

  --greenfire-image-preview-size: 120px;

  --greenfire-button-icon-size: 16px;

  --greenfire-checkbox-size: 16px;

  --greenfire-transition-fast: 0.1s;
  --greenfire-transition-normal: 0.2s;

  --greenfire-font-weight-semibold: 600;
  --greenfire-font-weight-bold: 700;
}

:root.theme-light {
  --greenfire-color-bg-main: 246 247 249;

  --greenfire-color-bg: 255 255 255;
  --greenfire-color-control-bg: 255 255 255;

  --greenfire-color-text-primary: 34 34 34;
  --greenfire-color-control-text: 85 85 85;

  --greenfire-color-border: 204 204 204;

  --greenfire-color-accent: 0 123 255;

  --greenfire-color-disabled-bg: 224 224 224;
  --greenfire-color-disabled-tab: 180 180 180;
  --greenfire-color-disabled-text: 117 117 117;

  --greenfire-color-error-bg: 242 222 222;
  --greenfire-color-error-border: 238 211 215;
  --greenfire-color-error-text: 185 74 72;

  --greenfire-color-dropdown-button-hover-bg: 28 15 15;

  --greenfire-color-detail-container-bg: 249 249 249;
  --greenfire-color-detail-row-bg: 255 255 255;
  --greenfire-color-detail-row-border: 221 221 221;

  --greenfire-color-grid-border: 211 211 211;

  --greenfire-color-process-icon: 0 0 0;

  --greenfire-color-primary-green: 5 107 58;
  --greenfire-color-primary-green-hover: 4 122 66;

  --greenfire-color-primary-blue: 29 78 216;
  --greenfire-color-primary-blue-hover: 37 99 235;

  --greenfire-color-primary-red: 180 35 44;
  --greenfire-color-primary-red-hover: 217 45 58;

  --greenfire-color-text-primary-white: 245 245 245;

  --greenfire-color-text-primary-grey: 119 126 136;

  --greenfire-color-secondary-bg-hover: 5 107 58;
  --greenfire-color-secondary-bg-active: 5 107 58;

  --greenfire-color-card-light-green: 240 255 244;
  --greenfire-color-card-light-red: 255 245 245;
  --greenfire-color-card-light-yellow: 255 255 240;

  --greenfire-shadow-container-general:
    0 4px 6px -1px rgba(0 0 0 / 0.1),
    0 2px 4px -1px rgba(0 0 0 / 0.06);
}

:root.theme-dark {
  --greenfire-color-bg-main: 8 12 22;

  --greenfire-color-bg: 15 23 41;
  --greenfire-color-control-bg: 31 33 37;

  --greenfire-color-text-primary: 235 235 235;
  --greenfire-color-control-text: 220 220 220;

  --greenfire-color-border: 58 61 67;

  --greenfire-color-accent: 59 130 246;

  --greenfire-color-disabled-bg: 55 58 63;
  --greenfire-color-disabled-tab: 80 80 80;
  --greenfire-color-disabled-text: 130 130 130;

  --greenfire-color-error-bg: 70 25 25;
  --greenfire-color-error-border: 120 40 40;
  --greenfire-color-error-text: 255 160 160;

  --greenfire-color-dropdown-button-hover-bg: 12 13 15;

  --greenfire-color-detail-container-bg: 26 28 31;
  --greenfire-color-detail-row-bg: 31 33 37;
  --greenfire-color-detail-row-border: 55 58 63;

  --greenfire-color-grid-border: 55 58 63;

  --greenfire-color-process-icon: 230 230 230;

  --greenfire-color-primary-green: 16 185 129;
  --greenfire-color-primary-green-hover: 20 204 142;

  --greenfire-color-primary-blue: 59 130 246;
  --greenfire-color-primary-blue-hover: 96 165 250;

  --greenfire-color-primary-red: 239 68 68;
  --greenfire-color-primary-red-hover: 248 113 113;

  --greenfire-color-text-primary-white: 250 250 250;

  --greenfire-color-text-primary-grey: 156 163 175;

  --greenfire-color-secondary-bg-hover: 45 55 72;
  --greenfire-color-secondary-bg-active: 59 130 246;

  --greenfire-color-card-light-green: 18 33 22;
  --greenfire-color-card-light-red: 32 19 19;
  --greenfire-color-card-light-yellow: 32 32 18;

  --greenfire-shadow-container-general:
    0 8px 24px rgba(0 0 0 / 0.35);
}
```

## **Classes**

GreenFire is designed to be customized primarily through **CSS variables**. However, every component also exposes predictable **CSS classes** prefixed with **`greenfire__`**, allowing applications to provide additional styling when necessary.

## **Customization example**

The next example changes GreenFire's accent color, rounds component corners, and increases the padding of all form controls without modifying any component styles.

```cs
:root.theme-light {

    --greenfire-color-accent: 255 99 71;

    --greenfire-color-primary-green: 255 99 71;

    --greenfire-radius-large: 16px;

    --greenfire-control-padding: 14px;
}
```
