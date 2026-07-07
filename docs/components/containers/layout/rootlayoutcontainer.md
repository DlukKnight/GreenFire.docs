# **RootLayoutComponent**

## **Description**

Represents a form-like structure in the shape of nodes. It is the base for all layouts used through GreenFire.

RootLayouts require no configuration. They are created and then new nodes are defined and added to them.

Receives the standard GreenFire [**runtime context**](../../runtimecontext.md).

This control doesn't inherit from **ContainerNode** and instead has its own metadata type: **RootLayout**.

### Metadata

#### rootLayout

RootLayout specific property that carries the container unique properties.

## **RootLayout metadata**

=== "React"

```ts
interface RootLayout {
  children: ContainerNode[];
};
```

### children

The collection of nodes that the layout owns.

## Example

=== "ASP.NET"

```csharp
RootLayoutBuilder<MyEntity> rootLayout = new();
```
