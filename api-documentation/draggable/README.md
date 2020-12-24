# Draggable

![](../../.gitbook/assets/draggable-large.svg)

Use the `useDraggable` hook turn DOM nodes into draggable sources that can be picked up, moved and dropped over [Droppable](../droppable/) containers.

## Usage

The `useDraggable` hook isn't opinionated about how you should structure your app. 

At minimum though, you need to pass the `setNodeRef` function that is returned by the `useDraggable` hook to a DOM element so that it can access the underlying DOM node and keep track of it to [detect collisions and intersections](../context-provider/collision-detection-algorithms.md) with other [droppable](../droppable/) elements. 

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

{% hint style="info" %}
Always try to use the  DOM element that is most [semantic](https://developer.mozilla.org/en-US/docs/Glossary/Semantics) in the context of your app.   
Check out our [Accessibility guide](../../guides/accessibility.md) to learn more about how you can help provide a better experience for screen readers.
{% endhint %}

### Listeners

If you've been paying close attention, you'll notice that unlike the [`useDroppable`](../droppable/) hook,  the `useDraggable` hook also requires that you attach `listeners` to the DOM node that you would like to make draggable. 

While we could have attached these listeners manually to the node  provided to `setNodeRef`, there are actually a number of key advantages to forcing the consumer to manually attach the listeners.

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

This strategy also means that we're able to use [React synthetic events](https://reactjs.org/docs/events.html), which ultimately leads to improved performance over manually attaching event listeners to each individual node.  
  
Why? Because rather than having to attach individual event listeners for each draggable DOM node, React attaches a single event listener for every type of event we listen to on the `document`. Once click on one of the draggable nodes happens, React's listener on the document dispatches a SyntheticEvent back to the original handler. 

### Transforms 

In order to actually see your draggable items move on screen, you'll need to move the item using CSS. You can use inline styles, CSS variables, or even CSS-in-JS libraries to pass the `transform` property as CSS to your draggable element.

{% hint style="success" %}
For performance reasons, we strongly recommend you use the **`transform`** CSS property to move your draggable item on the screen, as other positional properties such as **`top`**, **`left`** or **`margin`** can cause expensive repaints.  Learn more about [CSS transforms](https://developer.mozilla.org/en-US/docs/Web/CSS/transform).
{% endhint %}

After an item starts being dragged, the `transform` property will be populated with the `translate` coordinates you'll need to move the item on the screen.  The `transform` object adheres to the following shape: `{x: number, y: number, scaleX: number, scaleY: number}`

The `x` and `y` coordinates represent the delta from the point of origin of your draggable element since it started being dragged.

The `scaleX` and `scaleY` properties represent the difference in scale between the item that is dragged and the droppable container it is currently over. This is useful for building interfaces where the draggable item needs to adapt to the size of the droppable container it is currently over.

The `CSS` helper is entirely optional; it's a convenient helper for generating [CSS transform ](https://developer.mozilla.org/en-US/docs/Web/CSS/transform)strings, and is equivalent to manually constructing the string as such:

```javascript
CSS.Translate.toString(transform) ===
`translate3d(${translate.x}, ${translate.y}, 0)`
```



### Attributes

The `useDraggable` hook ****provides a set of sensible default attributes for draggable items. We recommend you attach these to the HTML element you are attaching the draggable listeners to.

We encourage you to manually attach the attributes that you think make sense in the context of your application rather than using them all without considering whether it makes sense to do so.

For example, if the HTML element you are attaching the `useDraggable` `listeners` to is already a semantic `button`, although it's harmless to do so, there's no need to add the `role="button"` attribute, since that is already the default role. 

<table>
  <thead>
    <tr>
      <th style="text-align:left">Attribute</th>
      <th style="text-align:left">Default value</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>role</code>
      </td>
      <td style="text-align:left"><code>&quot;button&quot;</code>
      </td>
      <td style="text-align:left">
        <p>If possible, we recommend you use a semantic <code>&lt;button&gt;</code> element
          for the DOM element you plan on attaching draggable listeners to.</p>
        <p></p>
        <p>In case that&apos;s not possible, make sure you include the <code>role=&quot;button&quot;</code>attribute,
          which is the default value.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>aria-roledescription</code>
      </td>
      <td style="text-align:left"><code>&quot;draggable&quot;</code>
      </td>
      <td style="text-align:left">While <code>draggable</code> is a sensible default, we recommend you customize
        this value to something that is tailored to the use case you are building.</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>aria-describedby</code>
      </td>
      <td style="text-align:left"><code>&quot;DndContext-[uniqueId]&quot;</code>
      </td>
      <td style="text-align:left">Each draggable item is provided a unique <code>aria-describedby</code> ID
        that points to the <a href="../context-provider/#screen-reader-instructions">screen reader instructions</a> to
        be read out when a draggable item receives focus.</td>
    </tr>
  </tbody>
</table>

To learn more about the best practices for making draggable interfaces accessible, read the full accessibility guide:

{% page-ref page="../../guides/accessibility.md" %}

## Draggable clone

In many cases, you'll probably want to use a [Draggable clone ](clone.md)rather than transforming the original draggable source. This will largely depend on your use-case.

If your item needs to move from one container to another, or if your draggable item is within a scrollable container, we recommend you use the [`<DraggableClone>`](clone.md) component so the item can move more freely from one container to another.

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

In this example, whenever a draggable item is picked up, we render a clone that can move freely outside of the `<ScrollableList>` and isn't constrained to it's overflow or stacking context.

We hope this has given you a taste of what Draggable clones are used for. There's a lot more you can do with Draggable clones. 

We recommend you read about all of the possibilities that it provides in the in-depth guide:

{% page-ref page="clone.md" %}

