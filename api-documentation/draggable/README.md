# Draggable

![](../../.gitbook/assets/draggable-large.svg)

Use the `useDraggable` hook turn DOM nodes into draggable sources that can be picked up, moved and dropped over [droppable](../droppable/) containers.

## Usage

The `useDraggable` hook isn't particularly opinionated about how your app should be structured.&#x20;

### Node ref

At minimum though, you need to pass the `setNodeRef` function that is returned by the `useDraggable` hook to a DOM element so that it can access the underlying DOM node and keep track of it to [detect collisions and intersections](../context-provider/collision-detection-algorithms.md) with other [droppable](../droppable/) elements.&#x20;

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
Always try to use the  DOM element that is most [semantic](https://developer.mozilla.org/en-US/docs/Glossary/Semantics) in the context of your app. \
Check out our [Accessibility guide](../../guides/accessibility.md) to learn more about how you can help provide a better experience for screen readers.
{% endhint %}

### Identifier

The `id` argument is a string that should be a unique identifier, meaning there should be no other **draggable** elements that share that same identifier within a given [`DndContext`](../context-provider/) provider.

### Listeners

The `useDraggable` hook requires that you attach `listeners` to the DOM node that you would like to become the activator to start dragging.&#x20;

While we could have attached these listeners manually to the node  provided to `setNodeRef`, there are actually a number of key advantages to forcing the consumer to manually attach the listeners.

#### Flexibility

While many drag and drop libraries need to expose the concept of "drag handles", creating a drag handle with the `useDraggable` hook is as simple as manually attaching the listeners to a different DOM element than the one that is set as the draggable source DOM node:

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

{% hint style="info" %}
When attaching the listeners to a different element than the node that is draggable, make sure you also attach the attributes to the same node that has the listeners attached so that it is still [accessible](../../guides/accessibility.md).&#x20;
{% endhint %}

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

This strategy also means that we're able to use [React synthetic events](https://reactjs.org/docs/events.html), which ultimately leads to improved performance over manually attaching event listeners to each individual node.\
\
Why? Because rather than having to attach individual event listeners for each draggable DOM node, React attaches a single event listener for every type of event we listen to on the `document`. Once click on one of the draggable nodes happens, React's listener on the document dispatches a SyntheticEvent back to the original handler.&#x20;

### Transforms&#x20;

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
`translate3d(${transform.x}px, ${transform.y}px, 0)`
```

### Attributes

The `useDraggable` hook **** provides a set of sensible default attributes for draggable items. We recommend you attach these to the HTML element you are attaching the draggable listeners to.

We encourage you to manually attach the attributes that you think make sense in the context of your application rather than using them all without considering whether it makes sense to do so.

For example, if the HTML element you are attaching the `useDraggable` `listeners` to is already a semantic `button`, although it's harmless to do so, there's no need to add the `role="button"` attribute, since that is already the default role.&#x20;

| Attribute              | Default value             | Description                                                                                                                                                                                                                                                                                         |
| ---------------------- | ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `role`                 | `"button"`                | <p>If possible, we recommend you use a semantic <code>&#x3C;button></code> element for the DOM element you plan on attaching draggable listeners to. </p><p></p><p>In case that's not possible, make sure you include the <code>role="button"</code>attribute, which is the default value.</p>      |
| `tabIndex`             | `"0"`                     | In order for your draggable elements to receive keyboard focus, they **need** to have the `tabindex` attribute set to `0` if they are not natively interactive elements (such as the HTML `button` element). For this reason, the `useDraggable` hook sets the `tabindex="0"` attribute by default. |
| `aria-roledescription` | `"draggable"`             | While `draggable` is a sensible default, we recommend you customize this value to something that is tailored to the use case you are building.                                                                                                                                                      |
| `aria-describedby`     | `"DndContext-[uniqueId]"` | Each draggable item is provided a unique `aria-describedby` ID that points to the [screen reader instructions](../context-provider/#screen-reader-instructions) to be read out when a draggable item receives focus.                                                                                |

To learn more about the best practices for making draggable interfaces accessible, read the full accessibility guide:

{% content-ref url="../../guides/accessibility.md" %}
[accessibility.md](../../guides/accessibility.md)
{% endcontent-ref %}

### Recommendations

#### `touch-action`

We highly recommend you specify the `touch-action` CSS property for all of your draggable elements.

> The **`touch-action`** CSS property sets how an element's region can be manipulated by a touchscreen user (for example, by zooming features built into the browser).\
> \
> Source: [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/touch-action)

In general, we recommend you set the `touch-action` property to `none` for draggable elements in order to prevent scrolling on mobile devices.&#x20;

{% hint style="info" %}
For [Pointer Events,](../sensors/pointer.md) there is no way to prevent the default behaviour of the browser on touch devices when interacting with a draggable element from the pointer event listeners. Using `touch-action: none;` is the only way to reliably prevent scrolling for pointer events.

Further,  using `touch-action: none;` is currently the only reliable way to prevent scrolling in iOS Safari for both Touch and Pointer events.&#x20;
{% endhint %}

If your draggable item is part of a scrollable list, we recommend you use a drag handle and set `touch-action` to `none` only for the drag handle, so that the contents of the list can still be scrolled, but that initiating a drag from the drag handle does not scroll the page.

Once a `pointerdown` or `touchstart` event has been initiated, any changes to the `touch-action` value will be ignored. Programmatically changing the `touch-action` value for an element from `auto` to `none` after a pointer or touch event has been initiated will not result in the user agent aborting or suppressing any default behavior for that event for as long as that pointer is active  (for more details, refer to the [Pointer Events Level 2 Spec](https://www.w3.org/TR/pointerevents2/#determining-supported-touch-behavior)).

## Drag Overlay

The `<DragOverlay>` component provides a way to render a draggable overlay that is removed from the normal document flow and is positioned relative to the viewport.

![](<../../.gitbook/assets/dragoverlay (1).png>)

To learn more about how to use drag overlays, read the in-depth guide:

{% content-ref url="drag-overlay.md" %}
[drag-overlay.md](drag-overlay.md)
{% endcontent-ref %}
