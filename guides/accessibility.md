# Accessibility

## Introduction

If you're new to digital accessibility for the web, the Web Almanac by HTTP Archive has an excellent primer on the subject and the state of web accessibility that you should read before diving into this guide: [https://almanac.httparchive.org/en/2020/accessibility](https://almanac.httparchive.org/en/2020/accessibility)

> Web accessibility is about achieving feature and information parity and giving complete access to all aspects of an interface to disabled people. 
>
> A digital product or website is simply not complete if it is not usable by everyone. If it excludes certain disabled populations, this is discrimination and potentially grounds for fines and/or lawsuits.
>
> – Source: [Web Almanac by HTTP Archive](https://almanac.httparchive.org/en/2020/accessibility#screen-reader-only-text)

People with varying disabilities use different assistive technologies to help them experience the web. The [Tools and Techniques](https://www.w3.org/WAI/people-use-web/tools-techniques/) article from the Web Accessibility Initiative \(WAI\) of the W3C covers how users can perceive, understand and interact with the web using different assistive technologies.

Some assistive technologies for the web include:

* Screen readers
* Voice control
* Screen magnifiers
* Input devices \(such as the keyboard, pointers and switch devices\)

In this guide, we'll focus on how to make drag and drop interfaces that are keyboard accessible and provide an experience that is optimized for screen readers.

## Building accessible drag and drop interfaces

Building drag and drop interfaces that are accessible to everyone isn't easy, and requires thoughtful consideration.

The `@dnd-kit/core` library provides a number of sensible defaults to help you make your drag and drop interfaces accessible.

These sensible defaults should be seen as starting points rather than something you can set and forget; there is no one-size-fits-all solution to web accessibility.

You know your application best, and while these sensible defaults will go a long way to help making your application accessible, in most cases you'll want to customize these  so that they are tailored to the context of your application.

The three main areas of focus for this guide to help you make your drag and drop interface more accessible are:

* Keyboard support
* Screen reader instructions
* Live regions to provide screen reader announcements

### Keyboard support



### Screen reader instructions

In order to let screen reader users know how to interact with draggable items using only the keyboard, it's important to provide clear instructions on how to pick up a a draggable item, how to move it, how to drop it and how to cancel the operation.

By default, each  [`<DndContext>`](../api-documentation/context-provider/) component renders a unique HTML element that is rendered off-screen to be used to provide these instructions to screen readers. The default instructions are:

> To pick up a draggable item, press the space bar.   
> While dragging, use the arrow keys to move the item around.  
> Press space again to drop the item in its new position, or press escape to cancel.

We recommend you customize these instructions to your application and use-case using the `screenReaderInstructions` prop of [`<DndContext>`](../api-documentation/context-provider/). 

### Live regions to provide screen reader announcements

[Live regions](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions) are used to notify screen readers of content changes. We use them in **dnd kit** to provide screen reader announcements in real-time of time-sensitive information of what is currently happening with draggable and droppable elements without having to move focus .

By default, each  [`<DndContext>`](../api-documentation/context-provider/) component renders a unique HTML element that is rendered off-screen to be used for live screen-reader announcements of events like when a drag operation has started, when a draggable item has been dragged over a droppable container, when a drag operation has ended, and when a drag operation has been cancelled.

#### Aria attributes

| Attribute | Default value | Description |
| :--- | :--- | :--- |
| `role` | `"button"` | If possible, we recommend you use a semantic `<button>` element for the DOM element you plan on attaching draggable listeners to. In case that's not possible, make sure you include the `role="button"`attribute, which is the default value. |
| `aria-roledescription` | `"draggable"` | While `draggable` is a sensible default, we recommend you customize this value to something that is  |
| `aria-describedby` | `"DndContext-[uniqueId]"` | Each draggable item is provided a unique `aria-describedby` ID that points to the voiceover instructions to be read out when a draggable item receives focus. |

### 

