# Installation

To get started with **@dnd-kit**, install the core library via `npm` or `yarn`:

```
npm install @dnd-kit/core
```

You'll also need to be make sure you have peer dependencies installed. Chances are you already have `react` and `react-dom` installed in your project, but if not, make sure to install them:

```bash
npm install react react-dom
```

## Packages

{% hint style="info" %}
**dnd kit** is a [monorepo](https://en.wikipedia.org/wiki/Monorepo). Depending on your needs, you may also want to install other sub-packages that are available under the `@dnd-kit` namespace.
{% endhint %}

### Core library

In order to keep the core of the library small, `@dnd-kit/core` only ships with the main building blocks that the majority of users will need most of the time for building drag and drop experiences:

- [Context provider](../api-documentation/context-provider/)
- Hooks for:
  - [Draggable](../api-documentation/draggable/)
  - [Droppable](../api-documentation/droppable/)
- [Drag Overlay](../api-documentation/draggable/drag-overlay.md)
- Sensors for:
  - [Pointer](../api-documentation/sensors/pointer.md)
  - [Mouse](../api-documentation/sensors/mouse.md)
  - [Touch](../api-documentation/sensors/touch.md)
  - [Keyboard](../api-documentation/sensors/keyboard.md)
- [Accessibility features](../guides/accessibility.md)

### Modifiers

Modifiers let you dynamically modify the movement coordinates that are detected by sensors. They can be used for a wide range of use cases, for example:

- Restricting motion to a single axis
- Restricting motion to the draggable node container's bounding rectangle
- Restricting motion to the draggable node's scroll container bounding rectangle
- Applying resistance or clamping the motion

The modifiers repository contains a number of useful modifiers that can be applied on [`DndContext`](../api-documentation/context-provider/) as well as [`DraggableClone`](../api-documentation/draggable/drag-overlay.md).

To start using modifiers, install the modifiers package via yarn or npm:

```
npm install @dnd-kit/modifiers
```

### Presets

#### [Sortable](../presets/sortable/)

The `@dnd-kit/core` package provides all the building blocks you would need to build a sortable interface from scratch should you choose to, but thankfully you don't need to.

If you plan on building a sortable interface, we highly recommend you try out `@dnd-kit/sortable`, which is a small layer built on top of `@dnd-kit/core` and optimized for building silky smooth, flexible, and accessible sortable interfaces.

```
npm install @dnd-kit/sortable
```
