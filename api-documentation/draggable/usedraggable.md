# useDraggable

![](../../.gitbook/assets/draggable.png)

## Arguments

```typescript
interface UseDraggableArguments {
  id: string;
  disabled?: boolean;
  aria?: {
    role?: string;
    roleDescription?: string;
    tabIndex?: number;
  },
}
```

### Identifier

The `id` argument is a string that should be a unique identifier, meaning there should be no other **draggable** elements that share that same identifier within a given [`DndContext`](../context-provider/) provider.

If you're building a component that uses both the `useDraggable` and `useDroppable` hooks, they can both share the same identifier since draggable elements are stored in a different key-value store than droppable elements.

### Disabled

Since hooks cannot be conditionally invoked, use the `disabled` argument and set it to `true` if you need to temporarily disable a `draggable` element.

### ARIA

#### Role

#### Role description

The `ariaRoleDescription` argument can be used to tailor the screen reader experience to your application. For example, if you're building a sortable list of products you'd want to set the `ariaRoleDescription` value to something like `"sortable product"`.

**Tab index**

## Properties

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

