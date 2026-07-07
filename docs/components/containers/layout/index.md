# **Overview**

## **Description**

Containers are elements that group nodes to achieve a certain purpose.

The key star of GreenFire is the [**RootLayout**](rootlayoutcontainer.md) container, which is the starting point for building the more complex layouts that get used through all the processes and controls.

Containers are meant to give a grouping context to other nodes. Think of it as a place to introduce your nodes under a certain configuration. For example, if you want to show your nodes inside a tab, this means that you have to group your controls under a Tab container.

Since containers are also nodes, they can contain other containers, which allows a lot of flexibility when creating your layouts.

## **ContainerNode metadata**

This is the basic type for the **containerType** property that has the common basic properties shared by all containers.

=== "React"

```ts
interface ContainerNode extends RenderNode {
  children: RenderNode[];
  containerType: ContainerType;
}
```

### children

The collection of nodes that the container owns.

### containerType

The type of the container.
