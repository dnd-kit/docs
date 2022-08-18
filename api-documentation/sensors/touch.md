# Touch

The Touch sensor responds to [Touch events](https://developer.mozilla.org/en-US/docs/Web/API/Touch\_events). Touch events offer the ability to interpret finger or stylus activity on touch screens or trackpads.

### Activator

The touch activator is the `onTouchStart` event handler. The Touch sensor is initialized if there is no more than a single touch on the [`event.touches`](https://developer.mozilla.org/en-US/docs/Web/API/TouchEvent/touches) property.

### Activation constraints

Like the [Pointer](pointer.md) sensor, the Touch sensor has two activation constraints:

* Distance constraint
* Delay constraint

These activation constraints are mutually exclusive and may not be used simultaneously.&#x20;

#### Distance

The distance constraint subscribes to the following interface:

```typescript
interface DistanceConstraint {
  distance: number;
}
```

The `distance` property represents the distance, in _pixels_, by which the touch input needs to be moved before a drag start event is emitted.

#### Delay

The delay constraint subscribe to the following interface:

```typescript
interface DelayConstraint {
  delay: number;
  tolerance: number;
}
```

The `delay` property represents the duration, in _milliseconds_, that a draggable item needs to be held by the touch input before a drag start event is emitted.&#x20;

The `tolerance` property represents the distance, in _pixels_, of motion that is tolerated before the drag operation is aborted. If the finger or stylus is moved during the delay duration and the tolerance is set to zero, the drag operation will be immediately aborted. If a higher tolerance is set, for example, a tolerance of `5` pixels, the operation will only be aborted if the finger is moved by more than 5 pixels during the delay.

This property is particularly useful for touch input, where some tolerance should be accounted for when using a delay constraint, as touch input is less precise than mouse input.

### Recommendations

#### `touch-action`

We highly recommend you specify the `touch-action` CSS property for all of your draggable elements.

> The **`touch-action`** CSS property sets how an element's region can be manipulated by a touchscreen user (for example, by zooming features built into the browser).\
> \
> Source: [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/touch-action)

In general, we recommend you set the `touch-action` property to `manipulation` for draggable elements when using the Touch sensor.&#x20;

{% hint style="info" %}
Touch events do not suffer the same limitations as Pointer events, and it is possible to prevent the page from scrolling in `touchmove` events.
{% endhint %}
