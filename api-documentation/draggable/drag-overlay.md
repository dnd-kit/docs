# Drag Overlay

The `<DragOverlay>` component provides a way to render a draggable overlay that is removed from the normal document flow and is positioned relative to the viewport.

![](../../.gitbook/assets/dragoverlay.png)

## When should I use a drag overlay?

Depending on your use-case, you may want to use a drag overlay rather than transforming the original draggable source element that is connected to the [`useDraggable`](usedraggable.md) hook:

* If you'd like to **show a preview** of where the draggable source will be when dropped, you can update the position of the draggable source while dragging without affecting the drag overlay.
* If your item needs to **move from one container to another while dragging**, we strongly recommend you use the `<DragOverlay>` component so the draggable item can unmount from its original container while dragging and mount back into a different container without affecting the drag overlay.
* If your draggable item is within a **scrollable container,** we also recommend you use a `<DragOverlay>`, otherwise you'll need to set the draggable element to `position: fixed` yourself so the item isn't restricted to the overflow and stacking context of its scroll container, and can move without being affected by the scroll position of its container.
* If your `useDraggable` items are within a **virtualized list**, you will absolutely want to use a drag overlay, since the original drag source can unmount while dragging as the virtualized container is scrolled.
* If you want **smooth drop animations** without the effort of building them yourself.

## Usage

You may render any valid JSX within the children of the `<DragOverlay>`.&#x20;

The `<DragOverlay>` component should **remain mounted at all times** so that it can perform the drop animation. If you conditionally render the `<DragOverlay>` component, drop animations will not work.

As a rule of thumb, try to render the `<DragOverlay>` outside of your draggable components, and follow the [presentational component pattern ](drag-overlay.md#presentational-components)to maintain a good separation of concerns.

Instead, you should conditionally render the children passed to the `<DragOverlay>`:

{% tabs %}
{% tab title="App.jsx" %}
```jsx
import React, {useState} from 'react';
import {DndContext, DragOverlay} from '@dnd-kit/core';

import {Draggable} from './Draggable';

/* The implementation details of <Item> and <ScrollableList> are not
 * relevant for this example and are therefore omitted. */

function App() {
  const [items] = useState(['1', '2', '3', '4', '5']);
  const [activeId, setActiveId] = useState(null);
  
  return (
    <DndContext onDragStart={handleDragStart} onDragEnd={handleDragEnd}>
      <ScrollableList>
        {items.map(id =>
          <Draggable key={id} id={id}>
            <Item value={`Item ${id}`} />
          </Draggable>
        )}
      </ScrollableList>
      
      <DragOverlay>
        {activeId ? (
          <Item value={`Item ${activeId}`} /> 
        ): null}
      </DragOverlay>
    </DndContext>
  );
  
  function handleDragStart(event) {
    setActiveId(event.active.id);
  }
  
  function handleDragEnd() {
    setActiveId(null);
  }
}
```
{% endtab %}

{% tab title="Draggable.jsx" %}
```jsx
import React from 'react';
import {useDraggable} from '@dnd-kit/core';

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
```
{% endtab %}
{% endtabs %}

## Patterns

### Presentational components

While this is an optional pattern, we recommend that the components you intend to make draggable be [presentational components ](https://medium.com/@dan\_abramov/smart-and-dumb-components-7ca2f9a7c7d0)that are decoupled from `@dnd-kit`.

Using this pattern, create a presentational version of your component that you intend on rendering within the drag overlay, and another version that is draggable and renders the presentational component.

#### Wrapper nodes

As you may have noticed from the example above, we can create small abstract components that render a wrapper node and make any children rendered within draggable:

{% tabs %}
{% tab title="Draggable.jsx" %}
```jsx
import React from 'react';
import {useDraggable} from '@dnd-kit/core';

function Draggable(props) {
  const Element = props.element || 'div';
  const {attributes, listeners, setNodeRef} = useDraggable({
    id: props.id,
  });
  
  return (
    <Element ref={setNodeRef} {...listeners} {...attributes}>
      {props.children}
    </Element>
  );
}
```
{% endtab %}
{% endtabs %}

Using this pattern, we can then render our presentational components within `<Draggable>` and within `<DragOverlay>`:

{% tabs %}
{% tab title="App.jsx" %}
```jsx
import React, {useState} from 'react';
import {DndContext, DragOverlay} from '@dnd-kit/core';

import {Draggable} from './Draggable';

/* The implementation details of <Item> is not
 * relevant for this example and therefore omitted. */

function App() {
  const [isDragging, setIsDragging] = useState(false);
  
  return (
    <DndContext onDragStart={handleDragStart} onDragEnd={handleDragEnd}>
      <Draggable id="my-draggable-element">
        <Item />
      </Draggable>
      
      <DragOverlay>
        {isDragging ? (
          <Item />
        ): null}
      </DragOverlay>
    </DndContext>
  );
  
  function handleDragStart() {
    setIsDragging(true);
  }
  
  function handleDragEnd() {
    setIsDragging(false);
  }
}
```
{% endtab %}
{% endtabs %}

#### Ref forwarding

Use the[ ref forwarding pattern](https://reactjs.org/docs/forwarding-refs.html) to connect your presentational components to the `useDraggable` hook:

```jsx
import React, {forwardRef} from 'react';

const Item = forwardRef(({children, ...props}, ref) => {
  return (
    <li {...props} ref={ref}>{children}</li>
  )
});
```

This way, you can create two versions of your component, one that is presentational, and one that is draggable and renders the presentational component **without the need for additional wrapper elements**:

```jsx
import React from 'react';
import {useDraggable} from '@dnd-kit/core';

function DraggableItem(props) {
  const {attributes, listeners, setNodeRef} = useDraggable({
    id: props.id,
  });
  
  return (
    <Item ref={setNodeRef} {...attributes} {...listeners}>
      {value}
    </Item>
  )
});
```

### Portals

The drag overlay is not rendered in a portal by default. Rather, it is rendered in the container where it is rendered.&#x20;

If you would like to render the `<DragOverlay>` in a different container than where it is rendered, import the [`createPortal`](https://reactjs.org/docs/portals.html) helper from `react-dom`:

```jsx
import React, {useState} from 'react';
import {createPortal} from 'react-dom';
import {DndContext, DragOverlay} from '@dnd-kit/core';

function App() {
  return (
    <DndContext>
      {createPortal(
        <DragOverlay>{/* ... */}</DragOverlay>,
        document.body,
      )}
    </DndContext>
  );
}
```

### Click events

With default settings, a `DragOverlay` will prevent `click` events from firing on your draggable element because the `DragOverlay` is displayed over top of the draggable. To fix this, add a delay or distance-based [activation constraint](../sensors/pointer.md#activation-constraints) or use a drag handle.

## Props

```typescript
{
  adjustScale?: boolean;
  children?: React.ReactNode;
  className?: string;
  dropAnimation?: DropAnimation | null;
  style?: React.CSSProperties;
  transition?: string | TransitionGetter;
  modifiers?: Modifiers;
  wrapperElement?: keyof JSX.IntrinsicElements;
  zIndex?: number;
}
```

### Children

You may render any valid JSX within the children of the `<DragOverlay>`. However, **make sure that the components rendered within the drag overlay do not use the `useDraggable` hook**.

Prefer conditionally rendering the `children` of `<DragOverlay>` rather than conditionally rendering `<DragOverlay>`, otherwise drop animations will not work.

### Class name and inline styles

If you'd like to customize the[ wrapper element](drag-overlay.md#wrapper-element) that the `DragOverlay`'s children are rendered into, use the `className` and `style` props:

```jsx
<DragOverlay
  className="my-drag-overlay"
  style={{
    width: 500,
  }}
>
  {/* ... */}
</DragOverlay>
```

### Drop animation

Use the `dropAnimation` prop to configure the drop animation.

```typescript
interface DropAnimation {
  duration: number;
  easing: string;
}
```

The `duration` option should be a number, in `milliseconds`. The default value is `250` milliseconds. The `easing` option should be a string that represents a valid [CSS easing function](https://developer.mozilla.org/en-US/docs/Web/CSS/easing-function). The default easing is `ease`.

```jsx
<DragOverlay dropAnimation={{
  duration: 500,
  easing: 'cubic-bezier(0.18, 0.67, 0.6, 1.22)',
}}>
  {/* ... */}
</DragOverlay>
```

To disable drop animations, set the `dropAnimation` prop to `null`.

```jsx
<DragOverlay dropAnimation={null}>
  {/* ... */}
</DragOverlay>
```

{% hint style="warning" %}
The `<DragOverlay>` component should **remain mounted at all times** so that it can perform the drop animation. If you conditionally render the `<DragOverlay>` component, drop animations will not work.
{% endhint %}

### Modifiers

Modifiers let you dynamically modify the movement coordinates that are detected by sensors. They can be used for a wide range of use-cases, which you can learn more about by reading the [Modifiers](../modifiers.md) documentation.

For example, you can use modifiers to restrict the movement of the `<DragOverlay>` to the bounds of the window:

```jsx
import {DndContext, DragOverlay} from '@dnd-kit';
import {
  restrictToWindowEdges,
} from '@dnd-kit/modifiers';

function App() {
  return (
    <DndContext>
      {/* ... */}
      <DragOverlay modifiers={[restrictToWindowEdges]}>
        {/* ... */}
      </DragOverlay>
    </DndContext>
  )
}
```

### Transition

By default, the `<DragOverlay>` component does not have any transitions, unless activated by the [`Keyboard` sensor](../sensors/keyboard.md). Use the `transition` prop to create a function that returns the transition based on the [activator event](../sensors/#activators). The default implementation is:

```javascript
function defaultTransition(activatorEvent) {
  const isKeyboardActivator = activatorEvent instanceof KeyboardEvent;

  return isKeyboardActivator ? 'transform 250ms ease' : undefined;
};
```

### Wrapper element

By default, the `<DragOverlay>` component renders your elements within a `div` element. If your draggable elements are list items, you'll want to update the `<DragOverlay>` component to render a `ul` wrapper instead, since wrapping a `li` item without a parent `ul` is invalid HTML:

```jsx
<DragOverlay wrapperElement="ul">
  {/* ... */}
</DragOverlay>
```

### `z-index`

The `zIndex` prop sets the [z-order](https://developer.mozilla.org/en-US/docs/Web/CSS/z-index) of the drag overlay. The default value is `999` for compatibility reasons, but we highly recommend you use a lower value.&#x20;
