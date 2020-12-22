---
description: >-
  Eager to get started? This quick start guide will help you familiarize
  yourself with the core concepts of dnd kit.
---

# Quick start

{% hint style="info" %}
Before getting started, make sure you have followed the installation steps outlined in the [Installation guide](installation.md).
{% endhint %}

### Context provider

First, we'll set up the general structure of the app. In order for the [`useDraggable`]() and [`useDroppable`]() hooks to function correctly, you'll need to ensure that the components where they are used are wrapped within a [`<DndContext />`](../api-documentation/context-provider/) component:

```jsx
import React from 'react';
import {DndContext} from '@dnd-kit/core';

function App() {
  return (
    <DndContext>
      <Draggable />
      <Droppable />
    </DndContext>
  )
}
```

### Droppable

![](../.gitbook/assets/droppable-large.svg)

Next, let's set up your first **Droppable** component.  To do so, we'll be using the `useDroppable` hook.  
  
The `useDroppable` hook isn't opinionated about how your app should be structured. At minimum, they requires you pass a ref to the DOM element that you would like to become droppable. You'll also need to provide a unique `id` attribute to all your droppable components. 

When a **Draggable** item is over your droppable element, the `isOver` property will become true.

```jsx
import React from 'react';
import {useDroppable} from '@dnd-kit/core';

function Droppable(props) {
  const {isOver, setNodeRef} = useDroppable({
    id: 'droppable',
  });
  const style = {
    color: isOver ? 'green' : undefined,
  };
  
  
  return (
    <div ref={setNodeRef} style={style}>
      {props.children}
    </div>
  );
}
```

As you can see, it really only takes just a few lines to transform your existing components into droppable containers.

### Draggable

![](../.gitbook/assets/draggable-large.svg)

Finally, let's take a look at implementing our first **Draggable** component. To do so, we'll be using the `useDraggable` hook.

The `useDraggable` ****hook isn't opinionated about how your app should be structured. At minimum, it requires you to be able to attach listeners and a ref to the DOM element that you would like to become draggable. You'll also need to provide a unique `id` attribute to all your draggable components. 

After an item starts being dragged, the `transform` property will be populated with the `translate` coordinates you'll need to move the item on the screen.  The `transform` object adheres to the following shape: `{x: number, y: number, scaleX: number, scaleY: number}`

```jsx
import React from 'react';
import {useDraggable} from '@dnd-kit/core';

function Draggable(props) {
  const {attributes, listeners, setNodeRef, transform} = useDraggable({
    id: 'draggable',
  });
  const style = transform ? {
    transform: `translate3d(${transform.x}px, ${transform.y}px, 0)`,
  } : undefined;

  
  return (
    <button ref={setNodeRef} style={style} {...listeners} {...attributes}>
      {props.children}
    </button>
  );
}
```

As you can see from the example above, it really only takes just a few lines to transform your existing components into draggable components.

{% hint style="success" %}
**Tips:** 

* For performance reasons, we recommend you use **`transform`** over other positional CSS properties to move the dragged element. 
* You'll likely want to alter the **`z-index`** of your Draggable component to ensure it appears on top of other elements.
* If your item needs to move from one container to another, we recommend you use the [`<DraggableClone>`](../api-documentation/draggable/clone.md) component so the item isn't constrained to its parent's stacking context and overflow constraints.
{% endhint %}

Converting the `transform` object to a string can feel tedious. Fear not, you can avoid having to do this by hand by importing the `CSS` utility from the `@dnd-kit/utilities` package: 

```jsx
import {CSS} from '@dnd-kit/utilities';

// Within your component that receives `transform` from `useDraggable`:
const style = {
  transform: CSS.Translate.toString(transform),
}
```

### Assembling all the pieces

Once you've set up your **Droppable** and **Draggable** components, you'll want to come back to where you set up your [`<DndContext>`](../api-documentation/context-provider/) component so you can add event listeners to be able to respond to the different events that are fired.

In this example, we'll assume you want to move your `<Draggable>` component from outside into your `<Droppable>` component:

![](../.gitbook/assets/example.png)

To do so, you'll want to listen to the `onDragEnd` event of  the `<DndContext>` to see if your draggable item was dropped over your droppable:

```jsx
import React from 'react';
import {DndContext} from '@dnd-kit/core';

function App() {
  const [isDropped, setIsDropped] = useState(false);
  const draggableMarkup = (
    <Draggable>Drag me</Draggable>
  );
  
  return (
    <DndContext onDragEnd={handleDragEnd}>
      {!isDropped ? draggableMarkup : null}
      <Droppable>
        {isDropped ? draggableMarkup : 'Drop here'}
      </Droppable>
    </DndContext>
  );
  
  function handleDragEnd(event) {
    if (event.over && event.over.id === 'droppable') {
      setIsDropped(true);
    }
  }
}
```

That's it! You've set up your first [**Droppable**](../api-documentation/droppable.md) ****and [**Draggable**](../api-documentation/draggable/) components.

### Pushing things a bit further

The example we've set up above is a bit simplistic. In the real world, you may have multiple droppable containers, and you may also want to be able to drag your items back out of the droppable containers once they've been dragged within them. 

Here's a slightly more complex example that contains multiple **Droppable** containers:

```jsx
import React from 'react';
import {DndContext} from '@dnd-kit/core';

function App() {
  const containers = ['A', 'B', 'C'];
  const [parent, setParent] = useState(null);
  const draggableMarkup = (
    <Draggable>Drag me</Draggable>
  );

  return (
    <DndContext onDragEnd={handleDragEnd}>
      {parent === null ? draggableMarkup : null}

      {containers.map((id) => (
        // We updated the Droppable component so it would accept an `id`
        // prop and pass it to `useDroppable`
        <Droppable key={id} id={id}>
          {parent === id ? draggableMarkup : 'Drop here'}
        </Droppable>
      ))}
    </DndContext>
  );

  function handleDragEnd(event) {
    const {over} = event;

    // If the item is dropped over a container, set it as the parent
    // otherwise reset the parent to `null`
    setParent(over ? over.id : null);
  }
};
```

We hope this quick start guide has given you a glimpse of the simplicity and power of **dnd kit**. There's much more to learn, and we encourage you to keep reading about all of the different options you can pass to the `<DndContext>` in the next section.

