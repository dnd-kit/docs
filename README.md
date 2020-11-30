---
description: >-
  dnd kit – A lightweight, modular, performant, accessible and  extensible drag
  & drop toolkit for React.
---

# Overview

* **Built for React:** exposes hooks such as ****`useDraggable` and `useDroppable`
* **Feature packed:** customizable collision detection algorithms, multiple activators, draggable clone, drag handles, auto-scrolling, constraints, and so much more.
* **Zero dependencies and modular:** the core of the library weighs only 10kb. It's built around built-in React state management and context, which keeps it lean.
* **Built-in support for multiple input methods:** Mouse, touch and keyboard sensors.
* **Fully customizable & extensible:** Customize every little detail, animations, transitions, behaviours, styles. Build your own sensors, collision detection algorithms, customize key bindings and so much more.
* **Supports a wide range of use cases:** vertical lists, horizontal lists, grids, multiple containers, variable sized list and grids, transformed items, virtualized lists.
* **Performance:** It was built with performance and silky smooth animations in mind.
* **Accessibility:** keyboard support, sensible default aria attributes, customizable voiceover instructions and live regions built-in.
* **Presets:** Need to build a sortable interface? Check out `@dnd-kit/sortable`, which is a thin layer built on top of `@dnd-kit/core`. Preset for multiple container sortable lists \(Kanban\) coming soon.

### Powerfully simple

**dnd kit** is powerful and packed with features, and built with the hope of being the last drag and drop library you'll ever need, whether you're building a simple draggable interface element or a complex application built around drag and drop interactions .

 At its core, **dnd kit** exposes two main concepts: [Draggable](concepts/draggable/) items and [Droppable](concepts/droppable/) containers. Transform your existing components using the `useDraggable` and `useDroppable` hooks. Manage events and customize the behaviour of your application using the [`<DndContext>`](concepts/context-provider.md)  provider. Check out our [quick start guide](guides/getting-started.md) to learn how get started with **dnd kit**.

![](.gitbook/assets/robot-illustration-concepts.svg)

### Performance

**dnd kit** lets you build drag and drop interfaces without having to mutate the DOM every time an item needs to shift position. 

This is possible because **dnd kit** lazily calculates and stores the initial positions and client rects of your droppable containers when a drag operation is initiated. These positions are passed down to your components that use `useDraggable` and `useDroppable` so that you can compute the new positions of your items while a drag operation is underway, and move them to their new positions using performant CSS properties that do not trigger a repaint such as `translate3d` and `scale`. For an example of how this can be achieved, check out the implementation of the sorting strategies that are exposed by the [`@dnd-kit/sortable`](presets/sortable.md) library.

This isn't to say that you can't shift the position of the items in the DOM while dragging, this is something that **is supported** and sometimes inevitable. In some cases, it won't be possible to know in advance what the new position and layout of the item until you move it in the DOM. Just know that these kind of mutations to the DOM while dragging are much more expensive and will cause a repaint, so if possible, prefer computing the new positions using `translate3d` and `scale`.

### Extensibility

Extensibility is at the core of **dnd kit**. It was built is built to be lean and extensible. It ships with the features we believe most people will want most of the time, and provides extension points to build the rest on top of `@dnd-kit/core`.

A prime example of the level of extensibility of **dnd kit** is the[ Sortable preset](presets/sortable.md), which is built using the extension points that are exposed by `@dnd-kit/core`.

The primary extension points of **dnd kit** are:

* [Sensors](concepts/sensors/)
* [Modifiers](api-documentation/modifiers.md)
* [Constraints](api-documentation/constraints.md)
* Custom collision detection algorithms

### Accessibility

Building accessible drag and drop interfaces is hard; **dnd kit** aims to help with this with a number of sensible defaults and starting points. In most cases though, you'll want to customize and personalize these defaults so that they are tailored to the context of your application.

#### Voiceover instructions



#### Live region updates



#### Aria attributes

 `aria` attributes:

| Attribute | Default value | Description |
| :--- | :--- | :--- |
| `role` | `"button"` | If possible, we recommend you use a semantic `<button>` element for the DOM element you plan on attaching draggable listeners to. In case that's not possible, make sure you include the `aria-role="button"`attribute, which is the default value. |
| `aria-roledescription` | `"draggable"` | While `draggable` is a sensible default, we recommend you customize this value to something that is  |
| `aria-describedby` | `"DndContext-[uniqueId]"` | Each draggable item is provided a unique `aria-describedby` ID that points to the voiceover instructions to be read out when a draggable item receives focus. |



### Architecture

Unlike most drag and drop libraries, **dnd kit** intentionally ****is **not** built on top of the [HTML5 Drag and drop API](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API). This was a deliberate architectural decision, that does come with tradeoffs that you should be aware of before deciding to use it, but for most applications, we believe the benefits outweigh the tradeoffs. 

The HTML5 Drag and drop API has some severe **limitations**. It does not support touch devices, which means that the libraries that are built on top of it need to expose an entirely different implementation to support touch devices. This typically increases the complexity of the codebase and the overall bundle size of the library. Further, it requires workarounds to implement common use cases such as customizing the drag preview, locking dragging to a specific axis or to the bounds of a container, or animating the dragged item as it is picked up. 

The main **tradeoff** with not using the HTML5 Drag and drop API is that you won't be able to drag from the desktop or between windows. If the drag and drop use-case you have in mind involves this kind of functionality, you'll definitely want to use a library that's built on top of the HTML 5 Drag and drop API. We highly recommend you check out [react-dnd](https://github.com/react-dnd/react-dnd/) for a React library that's has a native HTML 5 Drag and drop backend.









