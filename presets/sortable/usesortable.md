# useSortable

The `useSortable` hook is an abstraction that composes the [`useDroppable`](../../api-documentation/droppable/) and [`useDraggable`](../../api-documentation/draggable/) hooks.

![](../../.gitbook/assets/usesortable-3-.png)

{% hint style="info" %}
To function properly, the `useSortable` hook needs to be used within a descendant of a [`SortableContext`](sortable-context.md) provider higher up in the tree.
{% endhint %}

## Usage

If you're already familiar with the [`useDraggable`](../../api-documentation/draggable/) hook, the `useSortable` hook should look very familiar, since, it is an abstraction on top of it.

In addition to the `attributes`, `listeners`,`transform` and `setNodeRef` arguments, which you should already be familiar with if you've used the `useDraggable` hook before, you'll notice that the `useSortable` hook also provides a [`transition`](usesortable.md#transform) argument.

```jsx
import React from 'react';
import { useSortable } from '@dnd-kit/sortable';
import { CSS } from '@dnd-kit/utilities';

function SortableItem(props) {
  const { attributes, listeners, setNodeRef, transform, transition } =
    useSortable({ id: props.id });

  const style = {
    transform: CSS.Transform.toString(transform),
    transition,
  };

  return (
    <li ref={setNodeRef} style={style} {...attributes} {...listeners}>
      {/* ... */}
    </li>
  );
}
```

## Properties

### Listeners

The `listeners` property contains the a[ctivator event handlers](../../api-documentation/sensors/#activators) for each [Sensor](../../api-documentation/sensors/) that is defined on the parent [`DndContext`](../../api-documentation/context-provider/#props) provider.

It should be attached to the node\(s\) that you wish to use as the activator to begin a sort event. In most cases, that will be the same node as the one passed to `setNodeRef`, though not necessarily. For instance, when implementing a sortable element with a "drag handle", the ref should be attached to the parent node that should be sortable, but the listeners can be attached to the handle node instead.

For additional details on the [`listeners`](../../api-documentation/draggable/#listeners) property, refer to the [`useDraggable`](../../api-documentation/draggable/) documentation.

### Attributes

The `useSortable` hook provides a set of sensible default attributes for draggable items. We recommend you attach these to your draggable elements, though nothing will break if you don't.

For additional details on the [`attributes`](../../api-documentation/draggable/#attributes) property, refer to the [`useDraggable`](../../api-documentation/draggable/) documentation.

### Transform

The `transform` property represents the displacement and change of scale transformation that a sortable item needs to apply to transition to its new position without needing to update the DOM order.

The `transform` property for the `useSortable` hook behaves similarly to the [`transform`](../../api-documentation/draggable/#transforms) property of the [`useDraggable`](../../api-documentation/draggable/) hook for the active sortable item, when there is no [`DragOverlay`](../../api-documentation/draggable/drag-overlay.md) being used.

### Node ref

In order for the `useSortable` hook to function properly, it needs the `setNodeRef` property to be attached to the HTML element you intend on turning into a sortable element:

```jsx
function SortableItem(props) {
  const { setNodeRef } = useDraggable({
    id: props.id,
  });

  return <li ref={setNodeRef}>{/* ... */}</li>;
}
```

Keep in mind that the `ref` should be assigned to the outer container that you want to become draggable, but this doesn't necessarily need to coincide with the container that the listeners are attached to:

```jsx
function SortableItem(props) {
  const {arguments, listeners, setNodeRef} = useDraggable({
    id: props.id,
  });

  return (
    <li ref={setNodeRef}>
      {/* ... */}
      <button {...listeners} {...arguments}>Drag handle</button>
    </li>
  );
}
```

Since the `useSortable` hook is simply an abstraction on top of the [`useDraggable`](../../api-documentation/draggable/usedraggable.md) and [`useDroppable`](../../api-documentation/droppable/usedroppable.md) hooks, in some advanced use cases, you may also use the `setDroppableNodeRef` and `setDraggableNodeRef` properties to connect them to different nodes. For example, if you want the draggable element to have a different dimension than the droppable element that will be sortable:

```jsx
function SortableItem(props) {
  const { setDraggableNodeRef, setDroppableNodeRef } = useDraggable({
    id: props.id,
  });

  return (
    <li ref={setDroppableNodeRef}>
      {/* ... */}
      <button ref={setDraggableNodeRef}>Drag me</button>
    </li>
  );
}
```

### Transition

Refer to the [`transition` argument](usesortable.md#transition-1) documentation below.

## Arguments

### Identifier

The `id` argument is a string that should be a unique identifier.

Since the `useSortable` is an abstraction on top of the `useDroppable` and `useDraggable` hooks, which both require a unique identifier, the `useSortable` hook also requires a unique identifier.

The argument passed to the `id` argument of `useSortable` should match the `id` passed in the `items` array of the [`SortableContext`](sortable-context.md) provider.

### Disabled

If you'd like to temporarily disable a sortable item from being interactive, set the `disabled` argument to `true`.

### Transition

The transition argument controls the value of the `transition` property for you. It conveniently disables transform transitions while not dragging, but ensures that items transition back to their final positions when the drag operation is ended or cancelled.

It also disables transitions for the active sortable element that is being dragged, unless there is a [`DragOverlay`](../../api-documentation/draggable/drag-overlay.md) being used.

The default transition is `250` milliseconds, with an easing function set to `ease`, but you can customize this and pass any valid [CSS transition timing function](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-timing-function).

```javascript
const { transition } = useSortable({
  transition: {
    duration: 150, // milliseconds
    easing: 'cubic-bezier(0.25, 1, 0.5, 1)',
  },
});
```

Make sure you pass the `transition` style property to the same node that has the `transform` property applied:

```jsx
import React from 'react';
import { useSortable } from '@dnd-kit/sortable';
import { CSS } from '@dnd-kit/utilities';

function SortableItem(props) {
  const { transform, transition } = useSortable({ id: props.id });

  const style = {
    transform: CSS.Transform.toString(transform),
    transition,
  };

  return <li style={style}>{/* ... */}</li>;
}
```

If you prefer, you may also use CSS variables to manage the `transform` and `transition` properties:

```jsx
import React from 'react';
import { useSortable } from '@dnd-kit/sortable';
import { CSS } from '@dnd-kit/utilities';

function SortableItem(props) {
  const { transform, transition } = useSortable({ id: props.id });

  const style = {
    '--translate-x': transform ? transform.x : 0,
    '--translate-y': transform ? transform.y : 0,
    '--transition': transition,
  };

  return <li style={style}>{/* ... */}</li>;
}
```

To disable transitions entirely, set the `transition` argument to `null`:

```javascript
const { transition } = useSortable({
  transition: null,
});
```

If you prefer to manage transitions yourself, you may also choose to do so, but this isn't something we recommend.

### Sorting strategy

Optionally, you can pass a local sorting strategy that differs from the [global sorting strategy](sortable-context.md#strategy) passed to the parent `SortableContext` provider.
