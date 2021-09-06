# Sortable Context

The `SortableContext` provides information via context that is consumed by the [`useSortable`](usesortable.md) hook.

## Props

### Items

It requires that you pass it a sorted array of the unique identifiers associated with the elements that use the `useSortable` hook within it.

```jsx
import React, { useState } from 'react';
import { DndContext } from '@dnd-kit/core';
import { SortableContext } from '@dnd-kit/sortable';

function App() {
  const [items] = useState(['1', '2', '3']);

  return (
    <DndContext>
      <SortableContext items={items}>{/* ... */}</SortableContext>
    </DndContext>
  );
}
```

{% hint style="info" %}
It's important that the `items` prop passed to `SortableContext` be sorted in the same order in which the items are rendered, otherwise you may see unexpected results.
{% endhint %}

### Strategy

The `SortableContext` component also accepts different [sorting strategies](./#sorting-strategies) to compute transforms for the `useSortable` hook. The built in strategies include:

- `rectSortingStrategy`: This is the default value, and is suitable for most use cases. This strategy does not support virtualized lists.
- `verticalListSortingStrategy`: This strategy is optimized for vertical lists, and supports virtualized lists.
- `horizontalListSortingStrategy`: This strategy is optimized for horizontal lists, and supports virtualized lists.
- `rectSwappingStrategy`: Use this strategy to achieve swappable functionality.

Make sure to use the sorting strategy that is the most adapted to the use case you are building for.

For advanced use cases, you may also build custom sorting strategies. To do so, make sure that the custom strategy you are building accepts the arguments that are passed to a sorting strategy and adheres to the return values that are expected. For more details on this, refer to the implementation of the built-in sorting strategies.

### Identifier

The `SortableContext` component also optionally accepts an `id` prop. If an `id` is not provided, one will be auto-generated for you. The `id` prop is for advanced use cases. If you're building custom sensors, you'll have access to each sortable element's `data` prop, which will contain the `containerId` associated to that sortable context.

## Usage

{% hint style="info" %}
In order for the `SortableContext` component to function properly, make sure it is a descendant of a `DndContext` provider.
{% endhint %}

You may nest multiple `SortableContext` providers within the same parent `DndContext` provider.

You may also nest `SortableContext` providers within other `SortableContext` providers, either all under the same `DndContext` provider or each with their own individual `DndContext` providers if you would like to configure them with different options:

```jsx
// Bad, missing parent <DndContext>
<SortableContext>
  {/* ... */}
</SortableContext>

// Good, basic setup
<DndContext>
  <SortableContext>
    {/* ... */}
  </SortableContext>
</DndContext>

// Good, multiple sibling Sortable contexts
<DndContext>
  <SortableContext>
    {/* ... */}
  </SortableContext>
  <SortableContext>
    {/* ... */}
  </SortableContext>
</DndContext>

// Good, nested DndContexts
<DndContext>
  <SortableContext items={["A, "B", "C"]}>
    <DndContext>
      <SortableContext items={["A, "B", "C"]}>
        {/* ... */}
      </SortableContext>
    </DndContext>
  </SortableContext>
</DndContext>

// Bad, nested Sortable contexts with `id` collisions
<DndContext>
  <SortableContext items={["A, "B", "C"]}>
    <SortableContext items={["A, "B", "C"]}>
      {/* ... */}
    </SortableContext>
  </SortableContext>
</DndContext>

// Good, nested Sortable contexts with unique `id`s
<DndContext>
  <SortableContext items={["A, "B", "C"]}>
    <SortableContext items={["1, "2", "3"]}>
      {/* ... */}
    </SortableContext>
  </SortableContext>
</DndContext>

```

##
