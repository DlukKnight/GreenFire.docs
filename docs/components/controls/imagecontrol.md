# **ImageControlComponent**

## **Description**

Renders an image file picker control with preview.

This control inherits all properties from **BaseControl**. The metadata below only documents the properties specific to **ImageControl**.

## **ImageControl metadata**

=== "React"

```ts
interface ImageControl extends BaseControl {
  maxWidth?: number;
  maxHeight?: number;
  maxFileSizeMb?: number;
  allowDelete: boolean;
  acceptedFormats?: string[];
}
```

### maxWidth

Maximum width accepted for the image.

It will be shown as error after validations.

### maxHeight

Maximum height accepted for the image.

It will be shown as error after validations.

### maxFileSizeMb

Maximum size in MegaBytes accepted for the image.

It will be shown as error after validations.

### allowDelete

Shows the control delete button, allowing to empty the file selected and set the value of the control to deleted, signaling the backend that the image should be removed.

### acceptedFormats

Limits the selectable image format types. If empty, all formats will be available.

## Example

=== "ASP.NET"

```csharp
row.AddControlToRow(e => 
    e.ImageUrlDataModel, 
    new ImageControl
    {
        Required = true,
        MaxWidth = 120
        MaxHeight = 120, 
        MaxFileSizeMb = 2,
        AllowDelete = true,
        AcceptedFormats = ["bmp", "png"]
    });
```
