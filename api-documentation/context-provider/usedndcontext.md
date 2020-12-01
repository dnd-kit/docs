# useDndContext

For advanced use-cases, for example, if you're building your own presets on top of `@dnd-kit/core`, you may want to have access to the internal context of `<DndContext>` that the `useDraggable` and `useDroppable` have access to.

The `useDndContext` hook was created for those kind of situations.

```jsx
import {useDndContext} from '@dnd-kit/core`;

function MyCustomPreset() {
  const dndContext = useDndContext();
  
  /* do stuff */
}
```

If you think the preset you're building could be useful to others, feel free to open up a PR for discussion in the `dnd-kit` repository.



