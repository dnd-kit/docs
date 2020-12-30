---
description: >-
  The sortable preset provides the building blocks to build  sortable
  interfaces.
---

# Sortable

## Installation

To get started, install the sortable preset via `npm` or `yarn`:  

```bash
npm install @dnd-kit/sortable
```

## Overview

The sortable preset builds on top of the primitives exposed by `@dnd-kit/core` to help building sortable interfaces. 

The sortable preset exposes two main concepts: [~~`SortableContext`~~](./#sortable-context), and the [`useSortable`](./#usesortable) hook:

* The `SortableContext` provides information via context that is consumed by the `useSortable` hook.
* The `useSortable` hook is an abstraction that composes the [`useDroppable`](../../api-documentation/droppable/) and [`useDraggable`](../../api-documentation/draggable/) hooks:

![](../../.gitbook/assets/usesortable-1-.png)

### Single container

At a high level, the application structure to implement a **sortable list with a single container** looks as follows:

![](../../.gitbook/assets/sortable-1-.png)

### Multiple containers

To implement sortable list with items that can be dropped within **multiple containers**, the application structure is the same, but we add as many `SortableContext` providers as we have containers:

![](../../.gitbook/assets/sortable-multiple-containers-example.png)

In this example, we would use the `onDragOver` callback of `DndContext` to detect when a draggable element is moved over a different container to insert it in that new container while dragging. 

If you paid close attention to the illustration above, you may also have noticed that we added a droppable zone around each sortable context. This isn't required, but will likely be the behaviour most people want. If you move all sortable items from one column into the other, you will need a droppable zone for the empty column so that you may drag sortable items back into that empty column:

![](../../.gitbook/assets/sortable-multiple-containers-empty-column-1-.png)

## Concepts

### Sortable Context

In addition to the [drag and drop context provider](../../introduction/getting-started.md#context-provider),  the Sortable preset requires its own context provider that contains the **sorted** array of the unique identifiers associated to each sortable item:

```jsx
import React, {useState} from 'react';
import {DndContext} from '@dnd-kit/core';
import {SortableContext} from '@dnd-kit/sortable';

function App() {
  const [items] = useState(['1', '2', '3']);

  return (
    <DndContext>
      <SortableContext items={items}>
        {/* ... */}
      </SortableContext>
    </DndContext>
  );
}
```

The `SortableContext` provides information via context that is consumed by the `useSortable` hook, which is covered in greater detail in the next section.  

It does not expose any callback props. To know when a sortable \(draggable\) item is being picked or moved over another sortable \(droppable\) item, use the callback props of `DndContext`:

```jsx
import React, {useState} from 'react';
import {DndContext} from '@dnd-kit/core';
import {SortableContext} from '@dnd-kit/sortable';

function App() {
  const [items] = useState(['1', '2', '3']);

  return (
    <DndContext onDragEnd={handleDragEnd}>
      <SortableContext items={items}>
        {/* ... */}
      </SortableContext>
    </DndContext>
  );
  
  function handleDragEnd() {}
}
```

{% hint style="warning" %}
In order for the `SortableContext` component to function properly, make sure it is a descendant of a `DndContext` provider. You may nest multiple `SortableContext` components within the same parent `DndContext`.
{% endhint %}

### useSortable

As outlined above, the `useSortable` hook combines both the [`useDraggable`](../../api-documentation/draggable/) and [`useDroppable`](../../api-documentation/droppable/) hooks to connect elements as both draggable sources and drop targets:

![](../../.gitbook/assets/usesortable-3-.png)

{% hint style="info" %}
In most cases, the draggable and droppable hooks will be attached to the same node, and therefore be identical in size. They are represented as different nodes for illustration purposes above.
{% endhint %}

If you're already familiar with the [`useDraggable`](../../api-documentation/draggable/) hook, the `useSortable` hook should look very familiar, since, it is an abstraction on top of it. 

In addition to the `attributes`, `listeners`,`transform`  and `setNodeRef` arguments, which you should already be familiar with if you've used the `useDraggable` hook before, you'll notice that the `useSortable` hook also provides a `transition` argument.

{% tabs %}
{% tab title="SortableItem.jsx" %}
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
{% endtab %}
{% endtabs %}

The default transition is `250` milliseconds, with an easing function set to `ease`, but you can customize this and pass any valid [CSS transition timing function](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-timing-function), or set the transition argument to `null` to disable transitions entirely:

```javascript
const {
  transition,
} = useSortable({
  id: props.id,
  transition: {
    duration: 150, // milliseconds
    easing: 'cubic-bezier(0.25, 1, 0.5, 1)',
  },
});
```

For more details on the `useSortable` hook, read the full [API documentation](usesortable.md).

### Sensors

Sensors are an abstraction to manage and listen to different input methods. If you're unfamiliar with the concept of sensors, we recommend you read the [introduction to sensors](../../api-documentation/sensors/) first. 

By default, the [Keyboard](../../api-documentation/sensors/keyboard.md) sensor moves the active draggable item by `25` pixels in the direction of the arrow key that was pressed. This is an arbitrary default, and can be customized using the `coordinateGetter` option of the keyboard sensor. 

The sortable preset ships with a custom coordinate getter function for the keyboard sensor that moves the active draggable to the closest sortable element in a given direction within the same `DndContext`.

To use it, import the `useSortableSensors` coordinate getter provided by `@dnd-kit/sortable`, and pass it to the `coordiniateGetter` option of the Keyboard sensor.

In this example, we'll also be setting up the [Pointer](../../api-documentation/sensors/pointer.md) sensor, which is the other sensor that is enabled by default on `DndContext` if none are defined. We use the `useSensor` and `useCombineSensor` hooks to initialize the sensors:

```jsx
import {
  DndContext,
  KeyboardSensor,
  PointerSensor,
  useSensor,
  useCombineSensors,
} from '@dnd-kit/core';
import {
  SortableContext,
  sortableKeyboardCoordinates,
  useSortableSensors,
} from '@dnd-kit/sortable';

function App() {
  const [items] = useState(['1', '2', '3']);
  const sensors = useCombineSensors(
    useSensor(PointerSensor),
    useSensor(KeyboardSensor, {
      coordinateGetter: sortableKeyboardCoordinates,
    })
  );

  return (
    <DndContext sensors={sensors}>
      <SortableContext items={items}>
        {/* ... */}
      </SortableContext>
    </DndContext>
  );
}
```

If you'd like to use the [Mouse](../../api-documentation/sensors/mouse.md) and [Touch](../../api-documentation/sensors/touch.md) sensors instead of the [Pointer](../../api-documentation/sensors/pointer.md) sensor, simply initialize those sensors instead: 

```jsx
import {MouseSensor, TouchSensor, useSensor} from '@dnd-kit/core';
import {SortableContext, useSortableSensors} from '@dnd-kit/sortable';

function App() {
  const [items] = useState(['1', '2', '3']);
  const sensors = useCombineSensors(
    useSensor(MouseSensor, {
      // Require the mouse to move by 10 pixels before activating
      activationConstraint: {
        distance: 10,
      },
    }),
    useSensor(TouchSensor, {
      // Press delay of 250ms, with tolerance of 5px of movement
      activationConstraint: {
        delay: 250,
        tolerance: 5,
      },
    }),
    useSensor(KeyboardSensor, {
      coordinateGetter: sortableKeyboardCoordinates,
    })
  );

  return (
    <DndContext sensors={sensors}>
      <SortableContext items={items}>
        {/* ... */}
      </SortableContext>
    </DndContext>
  );
}
```

To learn more about sensors, read the in-depth documentation on sensors:

{% page-ref page="../../api-documentation/sensors/" %}

### Sorting strategies

The supported use cases of the Sortable preset include vertical lists, horizontal lists, grids, and virtualized lists. Because of the wide variety of use cases supported, it would be difficult to write a single strategy to cover all of these different use cases. Instead, the sortable preset exposes a number of different strategies you can use, that are tailored to these various use cases:

* `rectSortingStrategy`:  This is the default value, and is suitable for most use cases. This strategy does not support virtualized lists.
* `verticalListSortingStrategy`: This strategy is optimized for vertical lists, and supports virtualized lists.
* `horizontalListSortingStrategy`: This strategy is optimized for horizontal lists, and supports virtualized lists.
* `rectSwappingStrategy`: Use this strategy to achieve swappable functionality.

Make sure to use the sorting strategy that is the most adapted to the use case you are building for.

### Collision detection algorithm

The default collision detection algorithm of `DndContext` is the [rectangle intersection](../../api-documentation/context-provider/collision-detection-algorithms.md#rectangle-intersection) algorithm. While the rectangle intersection strategy is well suited for many use cases, it can be unforgiving, since it requires both the draggable and droppable bounding rectangles to come into direct contact and intersect.

For sortable lists, we recommend using a more forgiving collision detection strategy such as the [closest center](../../api-documentation/context-provider/collision-detection-algorithms.md#closest-center) or [closest corners](../../api-documentation/context-provider/collision-detection-algorithms.md#closest-corners) algorithms. 

In this example, we'll be using the closest center algorithm:

```jsx
import {
  closestCenter,
  SortableContext, 
  useSortableSensors
} from '@dnd-kit/sortable';

function App() {
  const [items] = useState(['1', '2', '3']);
  const sensors = useSortableSensors();

  return (
    <DndContext sensors={sensors} collisionDetection={closestCenter}>
      <SortableContext items={items}>
        {/* ... */}
      </SortableContext>
    </DndContext>
  );
}
```

To learn more about collision detection algorithms and when to use one over the other, read our guide on collision detection algorithms:

{% page-ref page="../../api-documentation/context-provider/collision-detection-algorithms.md" %}

## Connecting all the pieces

First, let's go ahead and render all of our sortable items:

{% tabs %}
{% tab title="App.jsx" %}
```jsx
import {
  closestCenter,
  SortableContext,
  useSortableSensors,
} from '@dnd-kit/sortable';


import {SortableItem} from './SortableItem.jsx';

function App() {
  const [items] = useState(['1', '2', '3']);
  const sensors = useSortableSensors();

  return (
    <DndContext sensors={sensors} collisionDetection={closestCenter}>
      <SortableContext items={items}>
        {items.map(id => <SortableItem key={id} id={id} />)}
      </SortableContext>
    </DndContext>
  );
}
```
{% endtab %}

{% tab title="Sortable.jsx" %}
```jsx
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
{% endtab %}
{% endtabs %}

In this example, we'll be building a vertical sortable list, so we will be using the `verticalListSortingStrategy`. Make sure to pass the strategy both to `SortableContext` and to `useSortableSensors`:

```jsx
import {
  closestCenter,
  SortableContext,
  useSortableSensors,
  verticalListSortingStrategy,
} from '@dnd-kit/sortable';

import {SortableItem} from './SortableItem.jsx';

function App() {
  const [items] = useState(['1', '2', '3']);
  const sensors = useSortableSensors({
    strategy: verticalListSortingStrategy,
  });

  return (
    <DndContext sensors={sensors} collisionDetection={closestCenter}>
      <SortableContext 
        items={items}
        strategy={verticalListSortingStrategy}
      >
        {items.map(id => <SortableItem key={id} id={id} />)}
      </SortableContext>
    </DndContext>
  );
}
```

Finally, we'll need to set up event handlers on the `DndContext` provider in order to update the order of the items on drag end.

```jsx
import {
  arrayMove,
  closestCenter,
  SortableContext,
  useSortableSensors,
  verticalListSortingStrategy,
} from '@dnd-kit/sortable';

import {SortableItem} from './SortableItem.jsx';

function App() {
  const [items, setItems] = useState(['1', '2', '3']);
  const sensors = useSortableSensors({
    strategy: verticalListSortingStrategy,
  });

  return (
    <DndContext 
      sensors={sensors}
      collisionDetection={closestCenter}
      onDragEnd={handleDragEnd}
    >
      <SortableContext 
        items={items}
        strategy={verticalListSortingStrategy}
      >
        {items.map(id => <SortableItem key={id} id={id} />)}
      </SortableContext>
    </DndContext>
  );
  
  function handleDragEnd(event) {
    const {active, over} = event;
    
    if (active.id !== over.id) {
      setItems((items) => {
        const oldIndex = items.indexOf(active.id);
        const newIndex = items.indexOf(over.id);
        
        return arrayMove(items, oldIndex, newIndex);
      });
    }
  }
}
```

