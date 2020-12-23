# Keyboard

The Keyboard sensor responds to [Keyboard events](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent). It is one of the default sensors used by the [DndContext](../context-provider/) provider if none are defined.

### Activator

The keyboard activator is the `onKeyDown` event handler. The Keyboard sensor is initialized if the [`event.code`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/code) property matches one of the `start` keys passed to `keyboardCodes` option of the Keyboard sensor.

By default, the `start` keys are `Space` and `Enter`.

### Options



