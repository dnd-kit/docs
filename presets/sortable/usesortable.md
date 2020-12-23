# useSortable

The `useSortable` hook is an abstraction that composes the [`useDroppable`](../../api-documentation/droppable.md) and [`useDraggable`](../../api-documentation/draggable/) hooks. 

![](../../.gitbook/assets/usesortable-2-.png)

{% hint style="info" %}
To function properly, the `useSortable` hook needs to be used within a descendant of a [`SortableContext`](sortable-context.md) provider higher up in the tree.
{% endhint %}

## Usage

If you're already familiar with the [`useDraggable`](../../api-documentation/draggable/) hook, the `useSortable` hook should look very familiar, since, it is an abstraction on top of it. 

In addition to the `attributes`, `listeners`,`transform`  and `setNodeRef` arguments, which you should already be familiar with if you've used the `useDraggable` hook before, you'll notice that the `useSortable` hook also provides a `transition` argument.

```jsx
import React from 'react';
import {useSortable} from '@dnd-kit/sortable';
import {CSS} from '@dnd-kit/utilities';

function SortableItem(props) {
  const {
    attributes,
    listeners,
    setNodeRef,
    transform,
    transition,
  } = useSortable({id: props.id});
  
  const style = {
    transform: CSS.Transform.toString(transform),
    transition,
  };
  
  return (
    <div ref={setNodeRef} style={style} {...attributes} {...listeners}>
      {/* ... */}
    </div>
  );
}
```

## Properties

### Listeners

The `listeners` property contains the a[ctivator event handlers](../../api-documentation/sensors/#activators) for each [Sensor](../../api-documentation/sensors/) that is defined on the parent [`DndContext`](../../api-documentation/context-provider/#props) provider. 

It should be attached to the node\(s\) that you wish to use as the activator to begin a sort event. In most cases, that will be the same node as the one passed to `setNodeRef`, though not necessarily. For instance, when implementing a sortable element with a "drag handle", the ref should be attached to the parent node that should be sortable, but the listeners can be attached to the handle node instead.

For additional details on the [`listeners`](../../api-documentation/draggable/#listeners) property, refer to the [`useDraggable`](../../api-documentation/draggable/) documentation.

### Attributes

**dnd kit** provides a set of sensible default attributes for draggable items. We recommend you attach these to your draggable elements, though nothing will break if you don't. 

For additional details on the [`attributes`](../../api-documentation/draggable/#attributes) property, refer to the [`useDraggable`](../../api-documentation/draggable/) documentation.

### Transform

The `transform` property 

For additional details on the [`transform`](../../api-documentation/draggable/#transforms) property, refer to the [`useDraggable`](../../api-documentation/draggable/) documentation.

### Node ref

For additional details on the `setNodeRef` property, refer to the [`useDraggable`](../../api-documentation/draggable/) documentation.

### Transition

## Arguments

### Identifier

The `id` argument is a string that should be a unique identifier.

Since the `useSortable` is an abstraction on top of the `useDroppable` and `useDraggable` hooks, which both require a unique identifier, the `useSortable` hook also requires a unique identifier.

The argument passed to the `id` argument of `useSortable` should match the `id` passed in the `items` array of the [`SortableContext`](sortable-context.md) provider.

### Transition

The default transition is `250` milliseconds, with an easing function set to `ease`, but you can customize this and pass any valid [CSS transition timing function](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-timing-function).

```javascript
const {
  transition,
} = useSortable({
  transition: {
    duration: 150, // milliseconds
    easing: 'cubic-bezier(0.25, 1, 0.5, 1)',
  },
});
```

To disable transitions entirely, pass `null` to the `transition` argument:

```javascript
const {
  transition,
} = useSortable({
  transition: null,
});
```

### Sorting strategy

Optionally, you can pass a local sorting strategy that differs from the [global sorting strategy](sortable-context.md#strategy) passed to the parent `SortableContext` provider.

