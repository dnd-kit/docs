# useDraggable



## Hook API

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

### Properties

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

