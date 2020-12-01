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


function Draggable() {
  const {attributes, listeners, setNodeRef} = useDraggable({
    id: 'unique-id',
  });
  
  return (
    <button ref={setNodeRef} {...listeners} {...attributes}>
      /* Render whatever you like within */
    </button>
  );
}
```

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



## Hook API – `useDraggable`

### Arguments

```typescript
interface UseDraggableArguments {
  id: string;
  disabled?: boolean;
  data?: Record<string, any>;
}
```

Set the `disabled` argument to `true` if you'd like to temporarily disable a `droppable` 

The `data` argument is for advanced use-cases where you may need access to additional data about the droppable element in custom sensors or presets. For example, if you were building a sortable preset, you could use the `data` attribute to store the index of the droppable element within a sortable list.  

### Return value

```typescript
{
  clientRect: ClientRect;
  isOver: boolean;
  node: React.RefObject<HTMLElement>;
  over: {id: UniqueIdentifier} | null;
  setNodeRef(element: HTMLElement | null): void;
}
```

Use the 

