# Pointer

The Pointer sensor responds to [Pointer events](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events). It is one of the default sensors used by the [DndContext](../context-provider/) provider if none are defined.

> Pointer events are DOM events that are fired for a pointing device. They are designed to create a single DOM event model to handle pointing input devices such as a mouse, pen/stylus or touch \(such as one or more fingers\).
>
> The pointer is a hardware-agnostic device that can target a specific set of screen coordinates. Having a single event model for pointers can simplify creating Web sites and applications and provide a good user experience regardless of the user's hardware.
>
> – Source: [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events)

### Activator

The pointer activator is the `onPointerDown` event handler. The Pointer sensor is initialized if the pointer event was triggered by the [primary pointer](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events#Determining_the_Primary_Pointer).

For mouse there is only one pointer, so it will always be the primary pointer. For touch input, a pointer is considered primary if the user touched the screen when there were no other active touches. For pen and stylus input, a pointer is considered primary if the user's pen initially contacted the screen when there were no other active pens contacting the screen.

### Activation constraints

The Pointer sensor has two activation constraints:

- Distance constraint
- Delay constraint

These activation constraints are mutually exclusive and may not be used simultaneously.

#### Distance

The distance constraint subscribes to the following interface:

```typescript
interface DistanceConstraint {
  distance: number;
}
```

The `distance` property represents the distance, in _pixels_, by which the pointer needs to be moved before a drag start event is emitted.

#### Delay

The delay constraint subscribe to the following interface:

```typescript
interface DelayConstraint {
  delay: number;
  tolerance: number;
}
```

The `delay` property represents the duration, in _milliseconds_, that a draggable item needs to be held by the primary pointer for before a drag start event is emitted.

The `tolerance` property represents the distance, in _pixels_, of motion that is tolerated before the drag operation is aborted. If the pointer is moved during the delay duration and the tolerance is set to zero, the drag operation will be immediately aborted. If a higher tolerance is set, for example, a tolerance of `5` pixels, the operation will only be aborted if the pointer is moved by more than 5 pixels during the delay.

This property is particularly useful for touch input, where some tolerance should be accounted for when using a delay constraint, as touch input is less precise than mouse input.

### Recommendations

#### `touch-action`

We highly recommend you specify the `touch-action` CSS property for all of your draggable elements.

> The **`touch-action`** CSS property sets how an element's region can be manipulated by a touchscreen user \(for example, by zooming features built into the browser\).
>
> Source: [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/touch-action)

In general, we recommend you set the `touch-action` property to `none` for draggable elements in order to prevent scrolling on mobile devices.

{% hint style="info" %}
For [Pointer Events,](pointer.md) there is no way to prevent the default behaviour of the browser on touch devices when interacting with a draggable element from the pointer event listeners. Using `touch-action: none;` is the only way to reliably prevent scrolling for pointer events.

Further, using `touch-action: none;` is currently the only reliable way to prevent scrolling in iOS Safari for both Touch and Pointer events.
{% endhint %}

If your draggable item is part of a scrollable list, we recommend you use a drag handle and set `touch-action` to `none` only for the drag handle, so that the contents of the list can still be scrolled, but that initiating a drag from the drag handle does not scroll the page.

Once a `pointerdown` or `touchstart` event has been initiated, any changes to the `touch-action` value will be ignored. Programmatically changing the `touch-action` value for an element from `auto` to `none` after a pointer or touch event has been initiated will not result in the user agent aborting or suppressing any default behavior for that event for as long as that pointer is active \(for more details, refer to the [Pointer Events Level 2 Spec](https://www.w3.org/TR/pointerevents2/#determining-supported-touch-behavior)\).
