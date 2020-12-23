# useDroppable

## Hook API

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

### Properties

```typescript
{
  clientRect: ClientRect;
  isOver: boolean;
  node: React.RefObject<HTMLElement>;
  over: {id: UniqueIdentifier} | null;
  setNodeRef(element: HTMLElement | null): void;
}
```

#### Over

**`isOver`**

Use the `isOver` boolean returned by the `useDroppable` hook to change the appearance or content displayed when a `draggable` element is dragged over your droppable container. 

**`over`**

If you'd like to change the appearance of the droppable in response to a draggable being dragged over a different droppable container, check whether the `over` value is defined. Depending on your use-case, you can also read the `id` of the other droppable that the draggable item to make changes to the render output of your droppable component.

#### Node

A [ref](https://reactjs.org/docs/refs-and-the-dom.html) to the current node that is passed to `setNodeRef`

