# Modifiers

Modifiers let you dynamically modify the movement coordinates that are detected by sensors. They can be used for a wide range of use cases, for example:

* Restricting motion to a single axis
* Restricting motion to the draggable node container's bounding rectangle 
* Restricting motion to the draggable node's scroll container bounding rectangle
* Applying resistance or clamping the motion

## Installation

To start using modifiers, install the modifiers package via yarn or npm:

```
npm install @dnd-kit/modifiers
```

## Usage

The modifiers repository contains a number of useful modifiers that can be applied on [`DndContext`](context-provider/) as well as [`DraggableClone`](draggable/clone.md).

```jsx
import {DndContext, DraggableClone} from '@dnd-kit';
import {
  restrictToVerticalAxis,
  restrictToWindowEdges,
} from '@dnd-kit/modifiers';

function App() {
  return (
    <DndContext modifiers={[restrictToVerticalAxis]}>
      {/* ... */}
      <DraggableClone modifiers={[restrictToWindowEdges]}>
        {/* ... */}
      </DraggableClone>
    </DndContext>
  )
}
```

As you can see from the example above, `DndContext` and `DraggableClone` can both have different modifiers.

## Built-in modifiers

### Restricting motion to an axis

#### `restrictToHorizontalAxis`

Restrict movement to only the horizontal axis.

#### `restrictToVerticalAxis`

Restrict movement to only the vertical axis.

### Restrict motion to a container's bounding rectangle

#### `restrictToWindowEdges`

Restrict movement to the edges of the window. This modifier can be useful to prevent the `DraggableClone` from being moved outside of the bounds of the window.

#### `restrictToContainerEdges`

Restrict motion to the parent container of the draggable item that is picked up.

#### `restrictToScrollContainerEdges`

Restrict motion to the first scrollable parent of the draggable item that is picked up.

### Snap to grid

#### `createSnapModifier`

Function to create modifiers to snap to a given grid size. 

```javascript
import {createSnapModifier} from '@dnd-kit/modifiers';

const gridSize = 20; // pixels
const snapToGridModifier = createSnapModifier(gridSize);
```

## Building custom modifiers



