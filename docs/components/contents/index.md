# **Overview**

## **Description**

Contents are nodes meant to provide visual elements that are independent from any entity property.

Contents can rely static information, or just aid in making the navigation visually better.

Similar to controls, contents also can have conditions, in concrete visibility conditions, which will allow them to be shown under controlled circumstances. To achieve this, all contents need to initialize a **LogicalKey**, which will be used by conditions to correctly point to the correct content.

Receives the standard GreenFire [**runtime context**](../runtimecontext.md).

## Metadata

### node

Content specific property that carries the content unique properties. Refer to each content for more information.

## **ContentNode metadata**

This is the basic type for the **node** property that has the common basic properties shared by all contents.

=== "React"

```ts
interface ContentNode extends RenderNode {
  contentType: ContentType;
}
```

### contentType

The type of the content.
