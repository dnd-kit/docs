# Droppable

![](../.gitbook/assets/droppable-large.svg)

Use the `useDroppable` hook to set up DOM nodes as droppable targets that draggable elements can be dropped over. 

**dnd kit** isn't opinionated about how you should structure your app. At minimum though, you need to pass the `setNodeRef` function that is returned by the `useDroppable` hook to a DOM element so that **dnd kit** can access the underlying DOM node and keep track of it to detect collisions and intersections with other draggable elements. 

{% hint style="info" %}
 If the concept of `ref` is new to you, we recommend you first check out the [Refs and the DOM article](https://reactjs.org/docs/refs-and-the-dom.html#adding-a-ref-to-a-dom-element) on the React documentation website.
{% endhint %}

```jsx
import {useDroppable} from '@dnd-kit/core';


function Droppable() {
  const {setNodeRef} = useDroppable({
    id: 'unique-id',
  });
  
  return (
    <div ref={setNodeRef}>
      /* Render whatever you like within */
    </div>
  );
}
```

You can set up as many droppable containers as you want, just make sure they all have a unique `id` so that they can be differentiated. Each droppable needs to have its own unique node though, so make sure you don't try to connect a single droppable to multiple refs.

To set up multiple droppable targets, simply use the `useDroppable` hook as many times as needed.

```jsx
function MultipleDroppables() {
  const {setNodeRef: setFirstDroppableRef} = useDroppable({
    id: 'droppable-1',
  });
  const {setNodeRef: setsecondDroppableRef} = useDroppable({
    id: 'droppable-2',
  });
  
  return (
    <section>
      <div ref={setFirstDroppableRef}>
        /* Render whatever you like within */
      </div>
      <div ref={setsecondDroppableRef}>
        /* Render whatever you like within */
      </div>
    </section>
  );
}
```

If you need to dynamically render a list of droppable containers, we recommend you create a re-usable Droppable component and render that component as many times as needed:

```jsx
function Droppable(props) {
  const {setNodeRef} = useDroppable({
    id: props.id,
  });
  
  return (
    <div ref={setNodeRef}>
      {props.children}
    </div>
  );
}

function MultipleDroppables() {
  const droppables = ['1', '2', '3', '4'];
  
  return (
    <section>
      {droppables.map((id) => (
        <Droppable id={id} key={id}>
          Droppable container id: ${id}
        </Droppable>
      ))}
    </section>
  );
}
```

## Hook API – `useDroppable`

### Arguments

```typescript
interface UseDroppableArguments {
  id: string;
  disabled?: boolean;
  data?: Record<string, any>;
}
```

Set the `disabled` argument to `true` if you'd like to temporarily disable a `droppable` 

The `data` argument is for advanced use-cases where you may need access to additional data about the droppable element in custom sensors or presets. For example, if you were building a sortable preset, you could use the `data` attribute to store the index of the droppable element within a sortable list.  

```jsx
  const {setNodeRef} = useDroppable<Data>({
    id: props.id,
    data: {
      index: props.index,
    }
  });
```

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

Use the `isOver` boolean returned by the `useDroppable` hook to change the appearance or content displayed when a `draggable` element is dragged over your droppable container. 

If you'd like to change the appearance of the droppable in response to a draggable being dragged over a different droppable container, check whether the `over` value is defined. Depending on your use-case, you can also read the `id` of the other droppable that the draggable item to make changes to the render output of your droppable component.

