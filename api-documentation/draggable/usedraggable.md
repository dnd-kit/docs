# useDraggable

![](../../.gitbook/assets/draggable.png)

## Arguments

```typescript
interface UseDraggableArguments {
  id: string | number;
  attributes?: {
    role?: string;
    roleDescription?: string;
    tabIndex?: number;
  };
  data?: Record<string, any>;
  disabled?: boolean;
}
```

### Identifier

The `id` argument is a `string` or `number` that should be a unique identifier, meaning there should be no other **draggable** elements that share that same identifier within a given [`DndContext`](../context-provider/) provider.

If you're building a component that uses both the `useDraggable` and `useDroppable` hooks, they can both share the same identifier since draggable elements are stored in a different key-value store than droppable elements.

### Data

The `data` argument is for advanced use-cases where you may need access to additional data about the draggable element in event handlers, modifiers or custom sensors.

For example, if you were building a sortable preset, you could use the `data` attribute to store the index of the draggable element within a sortable list to access it within a custom sensor.

```jsx
const { setNodeRef } = useDraggable({
  id: props.id,
  data: {
    index: props.index,
  },
});
```

Another more advanced example where the `data` argument can be useful is create relationships between draggable nodes and droppable areas, for example, to specify which types of droppable nodes your draggable node can be dropped on:

```jsx
import { DndContext, useDraggable, useDroppable } from '@dnd-kit/core';

function Droppable() {
  const { setNodeRef } = useDroppable({
    id: 'droppable',
    data: {
      type: 'type1',
    },
  });

  /* ... */
}

function Draggable() {
  const { attributes, listeners, setNodeRef, transform } = useDraggable({
    id: 'draggable',
    data: {
      supports: ['type1', 'type2'],
    },
  });

  /* ... */
}

function App() {
  return <DndContext onDragEnd={handleDragEnd}>/* ... */</DndContext>;

  function handleDragEnd(event) {
    const { active, over } = event;

    if (over && active.data.current.supports.includes(over.data.current.type)) {
      // do stuff
    }
  }
}
```

### Disabled

Since [hooks cannot be conditionally invoked](https://reactjs.org/docs/hooks-rules.html), use the `disabled` argument and set it to `true` if you need to temporarily disable a `draggable` element.

### Attributes

The default values for the `attributes` property are sensible defaults that should cover a wide range of use cases, but there is no one-size-fits-all solution.

You know your application best, and we encourage you to manually attach only the attributes that you think make sense in the context of your application rather than using them all without considering whether it makes sense to do so.&#x20;

For example, if the HTML element you are attaching the `useDraggable` `listeners` to is already a native HTML `button` element, although it's harmless to do so, there's no need to add the `role="button"` attribute, since that is already the default role of `button`.&#x20;

#### Role

The ARIA `"role"` attribute lets you explicitly define the role for an element, which communicates its purpose to assistive technologies.

The default value for the `"role"` attribute is `"button"`.&#x20;

{% hint style="info" %}
If it makes sense in the context of what you are building, we recommend that you leverage the native HTML `<button>` element for draggable elements.
{% endhint %}

#### Role description

The `roleDescription` argument can be used to tailor the screen reader experience to your application. For example, if you're building a sortable list of products you'd want to set the `roleDescription` value to something like `"sortable product"`.

**Tab index**

The `tabindex` attribute dictates the order in which focus moves throughout the document.

- Natively interactive elements such as [buttons](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button), [anchor tags](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/a) and[ form controls ](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormControlsCollection)have a default `tabindex` value of `0`.&#x20;
- Custom elements that are intended to be interactive and receive keyboard focus need to have an explicitly assigned `tabindex="0"`(for example, `div` and `li` elements)

In other words, in order for your draggable elements to receive keyboard focus, they **need** to have the `tabindex` attribute set to `0` if they are not natively interactive elements (such as the HTML `button` element).

For this reason, the `useDraggable` hook sets the `tabindex="0"` attribute by default.

## Properties

```typescript
{
  active: {
    id: UniqueIdentifier;
    node: React.MutableRefObject<HTMLElement>;
    rect: ViewRect;
  } | null;
  attributes: {
    role: string;
    tabIndex: number;
    'aria-disabled': boolean;
    'aria-roledescription': string;
    'aria-describedby': string;
  },
  isDragging: boolean;
  listeners: Record<SyntheticListenerName, Function> | undefined;
  node: React.MutableRefObject<HTMLElement | null>;
  over: {id: UniqueIdentifier} | null;
  setNodeRef(HTMLElement | null): void;
  setActivatorNodeRef(HTMLElement | null): void;
  transform: {x: number, y: number, scaleX: number, scaleY: number} | null;
}
```

### Active

#### `active`

If there is currently an active draggable element within the [`DndContext`](../context-provider/) provider where the `useDraggable` hook is used, the `active` property will be defined with the corresponding `id`, `node` and `rect` of that draggable element.&#x20;

Otherwise, the `active` property will be set to `null`.

#### `isDragging`

If the draggable element that is currently being dragged is the current one where `useDraggable` is used, the `isDragging` property will be `true`. Otherwise the `isDragging` property will be false.

Internally, the `isActive` property just checks if the `active.id === id`.

### Listeners

The `useDraggable` hook requires that you attach `listeners` to the DOM node that you would like to become the activator to start dragging.&#x20;

While we could have attached these listeners manually to the node provided to `setNodeRef`, there are actually a number of key advantages to forcing the consumer to manually attach the listeners.

#### Flexibility

While many drag and drop libraries need to expose the concept of "drag handles", creating a drag handle with the `useDraggable` hook is as simple as manually attaching the listeners to a different DOM element than the one that is set as the draggable source DOM node:

```jsx
import { useDraggable } from '@dnd-kit/core';

function Draggable() {
  const { attributes, listeners, setNodeRef } = useDraggable({
    id: 'unique-id',
  });

  return (
    <div ref={setNodeRef}>
      /* Some other content that does not activate dragging */
      <button {...listeners} {...attributes}>
        Drag handle
      </button>
    </div>
  );
}
```

{% hint style="info" %}
When attaching the listeners to a different element than the node that is draggable, make sure you also attach the attributes to the same node that has the listeners attached so that it is still [accessible](../../guides/accessibility.md).&#x20;
{% endhint %}

You can even have multiple drag handles if that makes sense in the context of your application:

```jsx
import { useDraggable } from '@dnd-kit/core';

function Draggable() {
  const { attributes, listeners, setNodeRef } = useDraggable({
    id: 'unique-id',
  });

  return (
    <div ref={setNodeRef}>
      <button {...listeners} {...attributes}>
        Drag handle 1
      </button>
      /* Some other content that does not activate dragging */
      <button {...listeners} {...attributes}>
        Drag handle 2
      </button>
    </div>
  );
}
```

#### Performance

This strategy also means that we're able to use [React synthetic events](https://reactjs.org/docs/events.html), which ultimately leads to improved performance over manually attaching event listeners to each individual node.\
\
Why? Because rather than having to attach individual event listeners for each draggable DOM node, React attaches a single event listener for every type of event we listen to on the `document`. Once click on one of the draggable nodes happens, React's listener on the document dispatches a SyntheticEvent back to the original handler.&#x20;

### Node

**`setNodeRef`**

In order for the `useDraggable` hook to function properly, it needs the `setNodeRef` property to be attached to the HTML element you intend on turning into a draggable element so that @dnd-kit can measure that element to compute collisions:

```jsx
function Draggable(props) {
  const { setNodeRef } = useDraggable({
    id: props.id,
  });

  return <button ref={setNodeRef}>{/* ... */}</button>;
}
```

Keep in mind that the `ref` should be assigned to the outer container that you want to become draggable, but this doesn't necessarily need to coincide with the container that the listeners are attached to.

#### **`node`**

A [ref](https://reactjs.org/docs/refs-and-the-dom.html) to the current node that is passed to `setNodeRef`

### Activator

**`setActivatorNodeRef`**

It's possible for the listeners to be attached to a different node than the one that `setNodeRef` is attached to.

A common example of this is when implementing a drag handle and attaching the listeners to the drag handle:

```jsx
function Draggable(props) {
  const { listeners, setNodeRef } = useDraggable({
    id: props.id,
  });

  return (
    <div ref={setNodeRef}>
      {/* ... */}
      <button {...listeners}>Drag handle</button>
    </div>
  );
}
```

When the activator node differs from the draggable node, we recommend setting the activator node ref on the activator node:

```jsx
function Draggable(props) {
  const { listeners, setNodeRef, setActivatorNodeRef } = useDraggable({
    id: props.id,
  });

  return (
    <div ref={setNodeRef}>
      {/* ... */}
      <button ref={setActivatorNodeRef} {...listeners}>
        Drag handle
      </button>
    </div>
  );
}
```

This helps @dnd-kit more accurately handle automatic focus management and can also be accessed by sensors for enhanced activation constraints.

{% hint style="info" %}
Focus management is automatically handled by [@dnd-kit](https://github.com/dnd-kit). When the activator event is a Keyboard event, focus will automatically be restored back to the first focusable node of the activator node.

If no activator node is set via `setActivatorNodeRef`, focus will automatically be restored on the first focusable node of the draggable node registered via `setNodeRef.`
{% endhint %}

### Over

#### **`over`**

If you'd like to change the appearance of the draggable element in response to it being dragged over a different droppable container, check whether the `over` value is defined.&#x20;

If a draggable element is moved over a droppable area, the `over` property will be defined for all draggable elements, regardless of whether or not those draggable elements are active or not.

If you'd like to make changes to only the active draggable element in response to it being moved over a droppable area, check whether the `isDragging` property is `true`.

### Transform

After a draggable item is picked up, the `transform` property will be populated with the `translate` coordinates you'll need to move the item on the screen. &#x20;

The `transform` object adheres to the following shape:&#x20;

```typescript
{
  x: number;
  y: number;
  scaleX: number;
  scaleY: number;
}
```

The `x` and `y` coordinates represent the delta from the point of origin of your draggable element since it started being dragged.

The `scaleX` and `scaleY` properties represent the difference in scale between the element that is currently being dragged and the droppable it is currently over, which can be useful if the draggable item needs to be dynamically resized to the size of the droppable it is over.
