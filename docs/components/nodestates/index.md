# **Overview**

## **Description**

NodeStates reflect the runtime values that modify node behavior.

=== "React"

```ts
interface NodeState {
    visible: boolean;
    disabled: boolean;
    required: boolean;
    value: any;
    states?: Record<string, NodeState>;
}
```

## Building states

In case of wanting to manually build this states, for example, to use a [**RootLayout**](../containers/layout/rootlayoutcontainer.md) out of any of the [**Processes**](../processes/index.md), the best way to do it is by using the **nodeStateBuilder** helper, which contains the next function:

=== "React"

```ts
function buildNodeStates({ layout, formData, conditionsMap, viewMode, translate }: Parameters)
```

By using this function, the NodeStates returned will contain all properties grouped in the layout, and will apply conditions based on the formData. The viewMode should not matter outside of processes, so the recommendation there is to send any value of **ProcessViewMode** that you know won't interfere with your conditions.
