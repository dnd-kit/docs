# DndContext

In order for your your [Droppable](droppable/) and [Draggable](draggable/) components to interact with each other, you'll need to make sure that the part of your React tree that uses them is nested within  a parent `<DndContext>` component, which makes use of the [React Context API](https://reactjs.org/docs/context.html) to share data between draggable and droppable components and hooks.

> React context provides a way to pass data through the component tree without having to pass props down manually at every level.

## Setup

```jsx
import React from 'react';
import {DndContext} from '@dnd-kit/core';

function App() {
  return (
    <DndContext>
      /* Your components that use `useDraggable`,
       * `useDroppable` or `DraggableClone` will need
       * to be nested within here.
       *
       * They don't need to be direct descendants, but
       * there does need to be a parent <DndContext>
       * somewhere higher up in the tree.
       * 
       * You can also nest <DndContext> providers within
       * other <DndContext> to achieve nested draggable
       * UIs that are independent of one another.
       */
    </DndContext>
  );
}
```

## Props

```typescript
interface Props {
  autoScroll?: boolean;
  announcements?: Announcements;
  children?: React.ReactNode;
  collisionDetection?: CollisionDetection;
  screenReaderInstructions?: ScreenReaderInstructions;
  sensors?: SensorDescriptor<any>[];
  translateModifiers?: Modifiers;
  onDragStart?(event: DragStartEvent): void;
  onDragMove?(event: DragMoveEvent): void;
  onDragOver?(event: DragOverEvent): void;
  onDragEnd?(event: DragEndEvent): void;
  onDragCancel?(): void;
}
```

## Usage

As you can see from the list of props above, there's a number of different events you can subscribe to and different customizations you can pass to `<DndContext>`.



