# Installation

To get started with **dnd kit**, install the core library via `npm` or `yarn`:  

```
npm install @dnd-kit/core
```

You'll also need to be make sure you have **dnd kit**'s peer dependencies installed. Chances are you already have `react` and `react-dom` installed in your project, but if not, make sure to ins:

```bash
npm install react react-dom
```

## Sub-packages

{% hint style="info" %}
 **dnd kit** is a [monorepo](https://en.wikipedia.org/wiki/Monorepo). Depending on your needs, you may also want to install other  sub-packages that are available under the `@dnd-kit` namespace.
{% endhint %}

In order to keep the core of the library small, **dnd kit** only ships with the core building blocks that the majority of users will need most of the time for building drag and drop experiences:

* [Context provider](../api-documentation/context-provider/)
* Hooks for: 
  * [Draggable](../api-documentation/draggable/)
  * [Droppable](../api-documentation/droppable.md)
* Sensors for:
  *  [Mouse](../api-documentation/sensors/mouse.md)
  * [Touch](../api-documentation/sensors/touch.md)
  * [Keyboard](../api-documentation/sensors/keyboard.md)
* Accessibility features

If you don't need any other features, you can skip right ahead to the [Quick start](getting-started.md) guide. 

### [Sortable](../presets/sortable.md)

The `@dnd-kit/core` package provides all the building blocks you would need to build a sortable interface from scratch should you choose to, but thankfully you don't need to. 

If you plan on building a sortable interface, we highly recommend you try out `@dnd-kit/sortable`, which is a small layer built on top of `@dnd-kit/core` and optimized for building silky smooth, flexible, and accessible sortable interfaces.

```
npm install @dnd-kit/sortable
```

