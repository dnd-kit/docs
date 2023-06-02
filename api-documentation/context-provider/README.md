# DndContext

## Application structure

### Context provider

In order for your [Droppable](../droppable/) and [Draggable](../draggable/) components to interact with each other, you'll need to make sure that the part of your React tree that uses them is nested within  a parent `<DndContext>` component. The `<DndContext>` provider makes use of the [React Context API](https://reactjs.org/docs/context.html) to share data between draggable and droppable components and hooks.

> React context provides a way to pass data through the component tree without having to pass props down manually at every level.

Therefore, components that use [`useDraggable`](../draggable/usedraggable.md), [`useDroppable`](../droppable/usedroppable.md)  or [`DragOverlay`](../draggable/drag-overlay.md) will need to be nested within a `DndContext` provider.

 They don't need to be direct descendants, but, there does need to be a parent `<DndContext>` provider somewhere higher up in the tree.

```jsx
import React from 'react';
import {DndContext} from '@dnd-kit/core';

function App() {
  return (
    <DndContext>
      {/* Components that use `useDraggable`, `useDroppable` */}
    </DndContext>
  );
}
```

### Nesting

You may also nest `<DndContext>` providers within other `<DndContext>` providers to achieve nested draggable/droppable interfaces that are independent of one another.

```jsx
import React from 'react';
import {DndContext} from '@dnd-kit/core';

function App() {
  return (
    <DndContext>
      {/* Components that use `useDraggable`, `useDroppable` */}
      <DndContext>
        {/* ... */}
        <DndContext>
          {/* ... */}
        </DndContext>
      </DndContext>
    </DndContext>
  );
}
```

When nesting `DndContext` providers, keep in mind that the `useDroppable` and `useDraggable` hooks will only have access to the other draggable and droppable nodes within that context.

If multiple `DndContext` providers are listening for the same event, events will be captured by the first `DndContext` that contains a [Sensor](../sensors/) that is activated by that event, similar to how [events bubble in the DOM](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events#Event_bubbling_and_capture).

## Props

```typescript
interface Props {
  announcements?: Announcements;
  autoScroll?: boolean;
  cancelDrop?: CancelDrop;
  children?: React.ReactNode;
  collisionDetection?: CollisionDetection;
  layoutMeasuring?: Partial<LayoutMeasuring>;
  modifiers?: Modifiers;
  screenReaderInstructions?: ScreenReaderInstructions;
  sensors?: SensorDescriptor<any>[];
  onDragStart?(event: DragStartEvent): void;
  onDragMove?(event: DragMoveEvent): void;
  onDragOver?(event: DragOverEvent): void;
  onDragEnd?(event: DragEndEvent): void;
  onDragCancel?(): void;
}
```

### Event handlers

As you can see from the list of props above, there are a number of different events emitted by `<DndContext>` that you can listen to and decide how to handle.

The main events you can listen to are:

#### `onDragStart`

Fires when a drag event that meets the [activation constraints](../sensors/#concepts) for that [sensor ](../sensors/)happens, along with the unique identifier of the draggable element that was picked up.

#### `onDragMove`

Fires anytime as the [draggable](../draggable/) item is moved. Depending on the activated [sensor](../sensors/#activators), this could for example be as the [Pointer](../sensors/pointer.md) is moved or the [Keyboard](../sensors/keyboard.md) movement keys are pressed.

#### `onDragOver` 

Fires when a [draggable](../draggable/) item is moved over a [droppable](../droppable/) container, along with the unique identifier of that droppable container.

#### `onDragEnd` 

Fires after a draggable item is dropped. 

This event contains information about the active draggable `id` along with information on whether the draggable item was dropped `over`. 

If there are no [collisions detected](collision-detection-algorithms.md) when the draggable item is dropped, the `over` property will be `null`. If a collision is detected, the `over` property will contain the `id` of the droppable over which it was dropped.

{% hint style="info" %}
It's important to understand that the `onDragEnd` event **does not move** [**draggable**](../draggable/) **items into** [**droppable**](../droppable/) **containers.** 

Rather, it provides **information** about which draggable item was dropped and whether it was over a droppable container when it was dropped.

It is up to the **consumer** of `DndContext` to decide what to do with that information and how to react to it, for example, by updating \(or not\) its internal state in response to the event so that the items are declaratively rendered in a different parent droppable.
{% endhint %}

#### `onDragCancel`

Fires if a drag operation is cancelled, for example, if the user presses `escape` while dragging a draggable item.

### Accessibility

For more details and best practices around accessibility of draggable and droppable components, read the accessibility section:

{% page-ref page="../../guides/accessibility.md" %}

#### Announcements

Use the `announcements` prop to customize the screen reader announcements that are announced in the live region when draggable items are picked up, moved over droppable regions, and dropped.

The default announcements are:

```javascript
const defaultAnnouncements = {
  onDragStart(id) {
    return `Picked up draggable item ${id}.`;
  },
  onDragOver(id, overId) {
    if (overId) {
      return `Draggable item ${id} was moved over droppable area ${overId}.`;
    }

    return `Draggable item ${id} is no longer over a droppable area.`;
  },
  onDragEnd(id, overId) {
    if (overId) {
      return `Draggable item was dropped over droppable area ${overId}`;
    }

    return `Draggable item ${id} was dropped.`;
  },
  onDragCancel(id) {
    return `Dragging was cancelled. Draggable item ${id} was dropped.`;
  },
}
```

While these default announcements are sensible defaults that should cover most simple use cases, you know your application best, and we highly recommend that you customize these to provide a screen reader experience that is more tailored to the use case you are building.

#### Screen reader instructions

Use the `screenReaderInstructions` prop to customize the instructions that are read to screen readers when the focus is moved 

### Autoscroll

Use the optional `autoScroll` boolean prop to temporarily or permanently disable auto-scrolling for all sensors used within this `DndContext`.

Auto-scrolling may also be disabled on an individual sensor basis using the static property `autoScrollEnabled` of the sensor. For example, the [Keyboard sensor](../sensors/keyboard.md) manages scrolling internally, and therefore has the static property `autoScrollEnabled` set to `false`.

### Collision detection

Use the `collisionDetection` prop to customize the collision detection algorithm used to detect collisions between draggable nodes and droppable areas within the`DndContext` provider. 

The default collision detection algorithm is the [rectangle intersection](collision-detection-algorithms.md#rectangle-intersection) algorithm.

The built-in collision detection algorithms are:

* [Rectangle intersection](collision-detection-algorithms.md#rectangle-intersection)
* [Closest center](collision-detection-algorithms.md#closest-center)
* [Closest corners](collision-detection-algorithms.md#closest-corners)

You may also build custom collision detection algorithms or compose existing ones.

To learn more, read the collision detection guide:

{% page-ref page="collision-detection-algorithms.md" %}

### Sensors

Sensors are an abstraction to detect different input methods in order to initiate drag operations, respond to movement and end or cancel the operation. 

The default sensors used by `DndContext` are the [Pointer](../sensors/pointer.md) and [Keyboard](../sensors/keyboard.md) sensors.

To learn how to customize sensors or how to pass different sensors to `DndContext`, read the Sensors guide:

{% page-ref page="../sensors/" %}

### Modifiers

Modifiers let you dynamically modify the movement coordinates that are detected by sensors. They can be used for a wide range of use cases, for example:

* Restricting motion to a single axis
* Restricting motion to the draggable node container's bounding rectangle 
* Restricting motion to the draggable node's scroll container bounding rectangle
* Applying resistance or clamping the motion

To learn more about how to use Modifiers, read the Modifiers guide:

{% page-ref page="../modifiers.md" %}

### Layout measuring

You can configure when and how often `DndContext`  should measure its droppable elements by using the `layoutMeasuring` prop. 

The `frequency` argument controls how frequently layouts should be measured. By default, layout measuring is set to `optimized`, which only measures layouts based on the `strategy`.

Specify one of the following strategies:

* `LayoutMeasuringStrategy.WhileDragging`: Default behavior, only measure droppable elements right after dragging has begun.

  `LayoutMeasuringStrategy.BeforeDragging`:  Measure droppable elements before dragging begins and right after it ends. 

* `LayoutMeasuringStrategy.Always`: Measure droppable elements before dragging begins, right after dragging has begun, and after it ends.

Example usage:

```jsx
import {DndContext, LayoutMeasuringStrategy} from '@dnd-kit/core';

<DndContext layoutMeasuring={{strategy: LayoutMeasuringStrategy.Always}} />
```

