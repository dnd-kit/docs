# Accessibility

## Introduction

If you're new to accessibility for the web, the _Web Almanac by HTTP Archive_ has an excellent primer on the subject and the state of web accessibility that you should read before diving into this guide: [https://almanac.httparchive.org/en/2020/accessibility](https://almanac.httparchive.org/en/2020/accessibility)

> Web accessibility is about achieving feature and information parity and giving complete access to all aspects of an interface to disabled people. 
>
> A digital product or website is simply not complete if it is not usable by everyone. If it excludes certain disabled populations, this is discrimination and potentially grounds for fines and/or lawsuits.
>
> – Source: [Web Almanac by HTTP Archive](https://almanac.httparchive.org/en/2020/accessibility#screen-reader-only-text)

People with varying disabilities use different assistive technologies to help them experience the web. 

The [Tools and Techniques](https://www.w3.org/WAI/people-use-web/tools-techniques/) article from the Web Accessibility Initiative \(WAI\) of the W3C covers how users can perceive, understand and interact with the web using different assistive technologies.

Some assistive technologies for the web include:

* Screen readers
* Voice control
* Screen magnifiers
* Input devices \(such as the keyboard, pointers and switch devices\)

In this guide, we'll focus on how to make drag and drop interfaces that are keyboard accessible and provide an experience that is optimized for screen readers.

## Building accessible drag and drop interfaces

Building drag and drop interfaces that are accessible to everyone isn't easy, and requires thoughtful consideration.

The `@dnd-kit/core` library provides a number of sensible defaults to help you make your drag and drop interfaces accessible.

These sensible defaults should be seen as _starting points_ rather than something you can set and forget; there is no one-size-fits-all solution to web accessibility.

You know your application best, and while these sensible defaults will go a long way to help making your application more accessible, in most cases you'll want to customize these  so that they are tailored to the context of your application.

The three main areas of focus for this guide to help you make your drag and drop interface more accessible are:

* Keyboard support
* Screen reader instructions
* Live regions to provide screen reader announcements

### Keyboard support

One of the[ five rules of ARIA](https://www.w3.org/TR/using-aria/#rule3) is that all interactive ARIA controls must be usable with the keyboard.

When creating widgets that a user can click or tap, drag, and drop, a user must also be able to **navigate to the widget** and **perform an equivalent action using the keyboard**.

For drag and drop interfaces, this means that the activator element that initiates the drag action must:

* Be able to receive focus
* A user must be able to activate the action associated with the element using **both** the `enter` \(on Windows\) or `return` \(on macOS\) and the `space` key.

Both these guidelines should be respected to comply with the [third rule of ARIA](https://www.w3.org/TR/using-aria/#3rdrule).

The `@dnd-kit/core` library ships with a [Keyboard sensor ](../api-documentation/sensors/keyboard.md)that adheres to these guidelines. The keyboard sensor is one of the two sensors that are enabled by default on the [`<DndContext>`](../api-documentation/context-provider/) provider component.

#### Focus

In order for the Keyboard sensor to function properly, the activator element that receives the `useDraggable` [listeners](../api-documentation/draggable/usedraggable.md#listeners) **must** be able to receive focus.

The `tabindex` attribute dictates the order in which focus moves throughout the document.

* Natively interactive elements such as [buttons](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button), [anchor tags](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/a) and[ form controls ](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormControlsCollection)have a default `tabindex` value of `0`. 
* Custom elements that are intended to be interactive and receive keyboard focus need to have an explicitly assigned `tabindex="0"`\(for example, `div` and `li` elements\)

In other words, in order for your draggable activator elements to be able to receive keyboard focus, they _need_ to have the `tabindex` attribute set to `0` **if** they are not natively interactive elements \(such as the HTML `button` element\).

For this reason, the `useDraggable` hook sets the `tabindex="0"` attribute by default.

#### Keyboard shortcuts

Once a draggable activator element receives focus, the `enter` \(on Windows\) or `return` \(on macOS\) and the `space` keys can be used to initiate a drag operation and pick up the draggable item.

The arrow keys are used to move the draggable item in any given direction.

After an item is picked up, it can be dropped using the `enter` \(on Windows\) or `return` \(on macOS\) and the `space` keys.

A drag operation can be cancelled using the `escape` key. It is recommended to allow users to cancel the drag operation using the `escape` key for all sensors, not just the Keyboard sensor.

In order to let users learn how to interact with draggable elements using these keyboard shortcuts, it's important to provide screen reader instructions.

### Screen reader instructions

In order to let screen reader users know how to interact with draggable items using only the keyboard, it's important to provide clear instructions on how to pick up a a draggable item, how to move it, how to drop it and how to cancel the operation.

By default, each  [`<DndContext>`](../api-documentation/context-provider/) component renders a unique HTML element that is rendered off-screen to be used to provide these instructions to screen readers. The default instructions are:

> To pick up a draggable item, press the space bar.   
> While dragging, use the arrow keys to move the item around.  
> Press space again to drop the item in its new position, or press escape to cancel.

We recommend you customize these instructions to your application and use-case using the `screenReaderInstructions` prop of [`<DndContext>`](../api-documentation/context-provider/). 

If your application supports multiple languages, make sure you also translate and localize these instructions. The `<DndContext>` component only ships with announcements in English due to bundle size concerns.

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

