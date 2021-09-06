# Sensors

## Concepts

Sensors are an abstraction to detect different input methods in order to initiate drag operations, respond to movement and end or cancel the operation.

### Activators

Sensors may define one or multiple **activator events**. Activator events use React [SyntheticEvent listeners](https://reactjs.org/docs/events.html), which leads to improved performance over manually adding event listeners to each individual draggable node.

Sensors are initialized once one of the activator events is detected.

### Built-in sensors

The built-in sensors are:

- [Pointer](pointer.md)
- [Mouse](mouse.md)
- [Touch](touch.md)
- [Keyboard](keyboard.md)

### Custom sensors

If necessary, you may also implement custom sensors to respond to other inputs or if the built-in sensors do not suit your needs. If you build a custom sensor and you think others could benefit, don't hesitate to open an RFC pull request.

## Lifecycle

The lifecycle of a sensor is as follows:

- Activator event detected, if the event is qualified, sensor class is initialized.
- Sensor manually attaches new listeners to input methods upon initialization.
- Sensor dispatches drag start event once constraints are met.
- Sensor dispatches drag move events in response to input.
- Sensor dispatches drag end or drag cancel event.
- Sensor is torn down and cleans up manually attached event listeners.

From an implementation perspective, Sensors are [classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes).

They are class-based rather than hooks because they need to be instantiated synchronously to respond to user interactions immediately, and it must be possible for them to be conditionally invoked.

## Hooks

### useSensor

By default, `DndContext` uses the [Pointer](pointer.md) and [Keyboard](keyboard.md) sensors.

If you'd like to use other sensors, such as the Mouse and Touch sensors instead, initialize those sensors separately with the options you'd like to use using the `useSensor` hook

```jsx
import { MouseSensor, TouchSensor, useSensor } from '@dnd-kit/core';

function App() {
  const mouseSensor = useSensor(MouseSensor, {
    // Require the mouse to move by 10 pixels before activating
    activationConstraint: {
      distance: 10,
    },
  });
  const touchSensor = useSensor(TouchSensor, {
    // Press delay of 250ms, with tolerance of 5px of movement
    activationConstraint: {
      delay: 250,
      tolerance: 5,
    },
  });
}
```

### useSensors

When initializing sensors with `useSensor`, make sure you pass the sensors to `useSensors` before passing them to `DndContext`:

```jsx
import {
  DndContext,
  KeyboardSensor,
  MouseSensor,
  TouchSensor,
  useSensor,
  useSensors,
} from '@dnd-kit/core';

function App() {
  const mouseSensor = useSensor(MouseSensor);
  const touchSensor = useSensor(TouchSensor);
  const keyboardSensor = useSensor(KeyboardSensor);

  const sensors = useSensors(mouseSensor, touchSensor, keyboardSensor);

  return <DndContext sensors={sensors}>{/* ... */}</DndContext>;
}
```

In other examples across the documentation, you may also see sensors initialized without intermediate variables, which is equivalent to the syntax above:

```jsx
import {
  DndContext,
  KeyboardSensor,
  MouseSensor,
  TouchSensor,
  useSensor,
  useSensors,
} from '@dnd-kit/core';

function App() {
  const sensors = useSensors(
    useSensor(MouseSensor),
    useSensor(TouchSensor),
    useSensor(KeyboardSensor)
  );

  return <DndContext sensors={sensors}>{/* ... */}</DndContext>;
}
```
