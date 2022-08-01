---
description: >-
  @dnd-kit – A lightweight, modular, performant, accessible and  extensible drag
  & drop toolkit for React.
---

# Overview

* **Feature packed:** customizable collision detection algorithms, multiple activators, draggable overlay, drag handles, auto-scrolling, constraints, and so much more.
* **Built for React:** exposes hooks such as [`useDraggable`](api-documentation/draggable/usedraggable.md) and [`useDroppable`](api-documentation/droppable/usedroppable.md), and  won't require you to re-architect your app or create additional wrapper DOM nodes.
* **Supports a wide range of use cases:** lists, grids, multiple containers, nested contexts, variable sized items, virtualized lists, 2D Games, and more.
* **Zero dependencies and modular:** the core of the library weighs around 10kb minified and has no external dependencies. It's built around built-in React state management and context, which keeps the library lean.
* **Built-in support for multiple input methods:** Pointer, mouse, touch and keyboard sensors.
* **Fully customizable & extensible:** Customize every detail – animations, transitions, behaviours, styles. Build your own [sensors](api-documentation/sensors/), [collision detection algorithms](api-documentation/context-provider/collision-detection-algorithms.md), customize key bindings and so much more.
* **Accessibility:** Keyboard support, sensible default aria attributes, customizable screen reader instructions and live regions built-in.
* **Performance:** It was built with performance in mind in order to support silky smooth animations.
* **Presets:** Need to build a sortable interface? Check out [`@dnd-kit/sortable`](presets/sortable/), which is a thin layer built on top of `@dnd-kit/core`. More presets coming in the future.

![](.gitbook/assets/concepts-illustration-large.svg)

The core library of **dnd kit** exposes two main concepts:

* [Draggable elements](api-documentation/draggable/)
* [Droppable areas](api-documentation/droppable/)

Augment your existing components using the [`useDraggable`](api-documentation/draggable/usedraggable.md) and [`useDroppable`](api-documentation/droppable/usedroppable.md) hooks, or combine both to create components that can both be dragged and dropped over.

Handle events and customize the behaviour of your draggable elements and droppable areas using the [`<DndContext>`](api-documentation/context-provider/)  provider.  Configure [sensors](api-documentation/sensors/) to handle different input methods.

Use the [`<DragOverlay>`](api-documentation/draggable/drag-overlay.md) component to render a draggable overlay that is removed from the normal document flow and is positioned relative to the viewport.

Check out our quick start guide to learn how get started:

{% content-ref url="introduction/getting-started.md" %}
[getting-started.md](introduction/getting-started.md)
{% endcontent-ref %}

### Extensibility

Extensibility is at the core of **dnd kit**. It was built to be lean and extensible. It ships with the features we believe most people will want most of the time, and provides extension points to build the rest on top of `@dnd-kit/core`.

A prime example of the level of extensibility of **dnd kit** is the[ Sortable preset](presets/sortable/), which is built using the extension points that are exposed by `@dnd-kit/core`.

The primary extension points are:

* [Sensors](api-documentation/sensors/)
* [Modifiers](api-documentation/modifiers.md)
* [Custom collision detection algorithms](api-documentation/context-provider/collision-detection-algorithms.md#custom-collision-detection-strategies)

### Accessibility

Building drag and drop interfaces that are accessible to everyone isn't easy, and requires thoughtful consideration.

The `@dnd-kit/core` library provides a number of starting points to help you make your drag and drop interfaces accessible:

* [Keyboard support ](api-documentation/sensors/keyboard.md)out of the box
* [Customizable screen reader instructions](guides/accessibility.md#screen-reader-instructions) for how to interact with draggable items
* [Customizable live region updates](guides/accessibility.md#screen-reader-announcements-using-live-regions) to provide screen reader announcements in real-time of what is currently happening with draggable and droppable elements.
* [Sensible defaults for attributes](api-documentation/draggable/usedraggable.md#attributes) that should be passed to draggable elements

Check out our Accessibility guide to learn more about how you can help make your drag and drop interface accessible for everyone:

{% content-ref url="guides/accessibility.md" %}
[accessibility.md](guides/accessibility.md)
{% endcontent-ref %}

### Architecture

Unlike many drag and drop libraries, **dnd kit** is **** intentionally **not** built on top of the [HTML5 Drag and drop API](https://developer.mozilla.org/en-US/docs/Web/API/HTML\_Drag\_and\_Drop\_API). This was a deliberate architectural decision, that does come with tradeoffs that you should be aware of before deciding to use it. For most web applications, we believe the benefits outweigh the tradeoffs.&#x20;

The HTML5 Drag and drop API has some severe **limitations**. It does not support touch devices, which means that the libraries that are built on top of it need to expose an entirely different implementation to support touch devices. This typically increases the complexity of the codebase and the overall bundle size of the library. Further, it requires workarounds to implement common use cases such as customizing the drag preview, locking dragging to a specific axis or to the bounds of a container, or animating the dragged item as it is picked up.&#x20;

The main **tradeoff** with not using the HTML5 Drag and drop API is that you won't be able to drag from the desktop or between windows. If the drag and drop use-case you have in mind involves this kind of functionality, you'll definitely want to use a library that's built on top of the HTML 5 Drag and drop API. We highly recommend you check out [react-dnd](https://github.com/react-dnd/react-dnd/) for a React library that's has a native HTML 5 Drag and drop backend. Keep in mind that dnd kit plays nicely with HTML5 Drag and Drop API libraries. For the sake of performance, accessibility, or enjoying our API, it's entirely possible to use something like react-dnd for just dnd from desktop or between windows, while using this library for all other features.

### Performance

#### **Minimizing DOM mutations**

**dnd kit** lets you build drag and drop interfaces without having to mutate the DOM every time an item needs to shift position.&#x20;

This is possible because **dnd kit** lazily calculates and stores the initial positions and client rects of your droppable containers when a drag operation is initiated. These positions are passed down to your components that use `useDraggable` and `useDroppable` so that you can compute the new positions of your items while a drag operation is underway, and move them to their new positions using performant CSS properties that do not trigger a repaint such as `translate3d` and `scale`. For an example of how this can be achieved, check out the implementation of the sorting strategies that are exposed by the [`@dnd-kit/sortable`](presets/sortable/) library.

This isn't to say that you can't shift the position of the items in the DOM while dragging, this is something that **is supported** and sometimes inevitable. In some cases, it won't be possible to know in advance what the new position and layout of the item until you move it in the DOM. Just know that these kind of mutations to the DOM while dragging are much more expensive and will cause a repaint, so if possible, prefer computing the new positions using `translate3d` and `scale`.

#### Synthetic events

Sensors use [SyntheticEvent listeners](https://reactjs.org/docs/events.html) for the activator events of all sensors, which leads to improved performance over manually adding event listeners to each individual draggable node.
