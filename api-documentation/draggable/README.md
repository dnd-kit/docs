# Draggable

![](../../.gitbook/assets/draggable-large.svg)

Use the `useDraggable` hook turn DOM nodes into draggable sources that can be moved and dropped over [Droppable](../droppable.md) containers.

**dnd kit** isn't opinionated about how you should structure your app. At minimum though, you need to pass the `setNodeRef` function that is returned by the `useDraggable` hook to a DOM element so that **dnd kit** can access the underlying DOM node and keep track of it to detect collisions and intersections with other droppable elements. 

{% hint style="info" %}
Always try to use the  DOM element that is most [semantic](https://developer.mozilla.org/en-US/docs/Glossary/Semantics) in the context of your app.   
Check out our [Accessibility guide](../../guides/accessibility.md) to learn more about how you can help provide a better experience for screen readers.
{% endhint %}

```jsx
import {useDraggable} from '@dnd-kit/core';
import {CSS} from '@dnd-kit/utilities';


function Draggable() {
  const {attributes, listeners, setNodeRef, transform} = useDraggable({
    id: 'unique-id',
  });
  const style = {
    transform: CSS.Translate.toString(transform),
  };
  
  return (
    <button ref={setNodeRef} style={style} {...listeners} {...attributes}>
      /* Render whatever you like within */
    </button>
  );
}
```

### Transform 

In order to actually see your draggable items move on screen, you'll need to move the item using CSS. You can use inline styles, CSS variables, or even CSS-in-JS libraries to pass the `transform` property as CSS to your draggable element.

{% hint style="success" %}
For performance reasons, we strongly recommend you use the **`transform`** CSS property to move your draggable item on the screen, as other positional properties such as **`top`**, **`left`** or **`margin`** can cause expensive repaints.  Learn more about [CSS transforms](https://developer.mozilla.org/en-US/docs/Web/CSS/transform).
{% endhint %}

After an item starts being dragged, the `transform` property will be populated with the `translate` coordinates you'll need to move the item on the screen.  The `transform` object adheres to the following shape: `{x: number, y: number, scaleX: number, scaleY: number}`

The `x` and `y` coordinates represent the delta from the point of origin of your draggable element since it started being dragged.

The `scaleX` and `scaleY` properties represent the difference in scale between the item that is dragged and the droppable container it is dropped over. This is useful for building interfaces where the draggable item needs to adapt to the size of the droppable container it is currently over.

The `CSS` utility is entirely optional. It's a convenient helper for generating CSS transform strings, and is equivalent to:

```javascript
CSS.Translate.toString(transform) == `translate3d(${translate.x}, ${translate.y}, 0)
```

### Draggable clone

In most cases, you'll probably want to use a [Draggable clone ](clone.md)rather than transforming the original draggable source. This will largely depend on your use-case.

If your item needs to move from one container to another, or if your draggable item is within a scrollable container, we recommend you use the [`<DraggableClone>`](clone.md) component so the item isn't constrained to its parent's stacking context and overflow constraints.

```jsx
import React, {useState} from 'react';
import {DndContext, DraggableClone} from '@dnd-kit/core';

/* The implementation details of <Item> and <ScrollableList> are not
 * relevant for this example and are therefore omitted. */

function Draggable(props) {
  const {attributes, listeners, setNodeRef} = useDraggable({
    id: props.id,
  });
  
  return (
    <li ref={setNodeRef} {...listeners} {...attributes}>
      {props.children}
    </li>
  );
}

function App() {
  const [activeId, setActiveId] = useState(null);
  
  return (
    <DndContext onDragStart={handleDragStart} onDragEnd={handleDragEnd}>
      <ScrollableList>
        <Draggable id="1"><Item value="Item 1" /></Draggable>
        <Draggable id="2"><Item value="Item 2" /></Draggable>
        <Draggable id="3"><Item value="Item 3" /></Draggable>
      </ScrollableList>
      
      <DraggableClone>
        {activeId ? (
          <Item value={`Item ${activeId}`} /> 
        ): null}
      </DraggableClone>
    </DndContext>
  );
  
  function handleDragStart({active}) {
    setActiveId(active.id);
  }
  
  function handleDragEnd() {
    setActiveId(null);
  }
}
```

In this example, whenever a draggable item is picked up, we will render a clone that can move freely outside of the `<ScrollableList>` and isn't constrained to it's overflow or stacking context.

{% hint style="info" %}
We hope this has given you a taste of what Draggable clones are used for. There's a lot more you can do with Draggable clones. We recommend you read about all of the possibilities that it provides in the in-depth guide on [Draggable clone](clone.md).
{% endhint %}

### Listeners

If you've been paying close attention, you'll notice that unlike the [`useDroppable`](../droppable.md) hook,  the `useDraggable` hook also requires that you attach `listeners` to the DOM node that you would like to make draggable. 

While we could have attached these listeners manually to the node  provided to `setNodeRef`, there are actually a number of key advantages to forcing the consumer to manually attach the listeners:

#### Flexibility

While many drag and drop libraries need to expose the concept of "drag handles", creating a drag handle with **dnd kit** is as simple as manually attaching the listeners to a different DOM element than the one that is set as the draggable source DOM node:

```jsx
import {useDraggable} from '@dnd-kit/core';


function Draggable() {
  const {attributes, listeners, setNodeRef} = useDraggable({
    id: 'unique-id',
  });
  
  return (
    <div ref={setNodeRef}>
      /* Some other content that does not activate dragging */
      <button {...listeners} {...attributes}>Drag handle</button>
    </div>
  );
}
```

You can even have multiple drag handles if that makes sense in the context of your application:

```jsx
import {useDraggable} from '@dnd-kit/core';


function Draggable() {
  const {attributes, listeners, setNodeRef} = useDraggable({
    id: 'unique-id',
  });
  
  return (
    <div ref={setNodeRef}>
      <button {...listeners} {...attributes}>Drag handle 1</button>
      /* Some other content that does not activate dragging */
      <button {...listeners} {...attributes}>Drag handle 2</button>
    </div>
  );
}
```

#### Performance

This strategy also means that we're able to use [React synthetic events](https://reactjs.org/docs/events.html), which ultimately leads to improved performance over manually adding event listeners to each individual node.  
  
Why? Because rather than having to attach individual event listeners for each draggable DOM node, React attaches a single event listener for every type of event we listen to on the `document`. Once click on one of the draggable nodes happens, React's listener on the document dispatches a SyntheticEvent back to the original handler. 

### Attributes

**dnd kit** provides a set of sensible default attributes for draggable items. We recommend you attach these to your draggable elements, though nothing will break if you don't. You can also manually attach the attributes that you think make sense in the context of your application.

| Attribute | Default value | Description |
| :--- | :--- | :--- |
| `role` | `"button"` | If possible, we recommend you use a semantic `<button>` element for the DOM element you plan on attaching draggable listeners to. In case that's not possible, make sure you include the `role="button"`attribute, which is the default value. |
| `aria-roledescription` | `"draggable"` | While `draggable` is a sensible default, we recommend you customize this value to something that is  |
| `aria-describedby` | `"DndContext-[uniqueId]"` | Each draggable item is provided a unique `aria-describedby` ID that points to the voiceover instructions to be read out when a draggable item receives focus. |



## Hook API – `useDraggable`

### Arguments

```typescript
interface UseDraggableArguments {
  id: string;
  disabled?: boolean;
  ariaRoleDescription?: string;
}
```

Set the `disabled` argument to `true` if you'd like to temporarily disable a `draggable` element.

The `ariaRoleDescription` argument can be used to tailor the screen reader experience to your application. For example, if you're building a sortable list of products you'd want to set the `ariaRoleDescription` value to something along the lines of `"sortable product"`.

### Return value

```typescript
{
  active: {
    id: UniqueIdentifier;
    node: React.MutableRefObject<HTMLElement>;
  } | null;
  activeRect: PositionalClientRect | null;
  attributes: {
    role: string;
    'aria-pressed': boolean;
    'aria-roledescription': string;
    'aria-describedby': string;
  },
  clientRects: PositionalClientRectMap;
  isDragging: boolean;
  listeners: Record<SyntheticListenerName, Function> | undefined;
  node: React.MutableRefObject<HTMLElement | null>;
  over: {id: UniqueIdentifier} | null;
  setNodeRef(HTMLElement | null): void;
  transform: {x: number, y: number, scaleX: number, scaleY: number} | null;
}
```

