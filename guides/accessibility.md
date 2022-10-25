# Accessibility

## Introduction

If you're new to accessibility for the web, the _Web Almanac by HTTP Archive_ has an excellent primer on the subject and the state of web accessibility that you should read before diving into this guide: [https://almanac.httparchive.org/en/2021/accessibility](https://almanac.httparchive.org/en/2021/accessibility)

> Web accessibility is about achieving feature and information parity and giving complete access to all aspects of an interface to disabled people.&#x20;
>
> A digital product or website is simply not complete if it is not usable by everyone. If it excludes certain disabled populations, this is discrimination and potentially grounds for fines and/or lawsuits.
>
> – Source: [Web Almanac by HTTP Archive](https://almanac.httparchive.org/en/2020/accessibility#screen-reader-only-text)

People with varying disabilities use different assistive technologies to help them experience the web.&#x20;

The [Tools and Techniques](https://www.w3.org/WAI/people-use-web/tools-techniques/) article from the Web Accessibility Initiative (WAI) of the W3C covers how users can perceive, understand and interact with the web using different assistive technologies.

Some assistive technologies for the web include:

* Screen readers
* Voice control
* Screen magnifiers
* Input devices (such as the keyboard, pointers and switch devices)

When building accessible interfaces for the web, it's important to keep the three  following questions in mind:

1. **Identity:** What element is the user interacting with?
2. **Operation:** How can the user interact with the element?
3. **State:** What is the current state of the element?

In this guide, we'll focus on how to make drag and drop interfaces that are keyboard accessible and provide identity, operation instructions and live state updates for screen readers.

## Building accessible drag and drop interfaces

Building drag and drop interfaces that are accessible to everyone isn't easy, and requires thoughtful consideration.

The `@dnd-kit/core` library provides a number of sensible defaults to help you make your drag and drop interfaces accessible.

These sensible defaults should be seen as _starting points_ rather than something you can set and forget; there is no one-size-fits-all solution to web accessibility.

You know your application best, and while these sensible defaults will go a long way to help making your application more accessible, in most cases you'll want to customize these  so that they are tailored to the context of your application.

The three main areas of focus for this guide to help you make your drag and drop interface more accessible are:

* [Keyboard support](accessibility.md#keyboard-support)
* [Screen reader instructions](accessibility.md#screen-reader-instructions)
* [Live regions to provide screen reader announcements](accessibility.md#screen-reader-announcements-using-live-regions)

### Keyboard support

One of the[ five rules of ARIA](https://www.w3.org/TR/using-aria/#rule3) is that all interactive ARIA controls must be usable with the keyboard.

When creating widgets that a user can click or tap, drag, and drop, a user must also be able to **navigate to the widget** and **perform an equivalent action using the keyboard**.

For drag and drop interfaces, this means that the activator element that initiates the drag action must:

* Be able to receive focus
* A user must be able to activate the action associated with the element using **both** the `enter` (on Windows) or `return` (on macOS) and the `space` key.

Both these guidelines should be respected to comply with the [third rule of ARIA](https://www.w3.org/TR/using-aria/#3rdrule).

The `@dnd-kit/core` library ships with a [Keyboard sensor ](../api-documentation/sensors/keyboard.md)that adheres to these guidelines. The keyboard sensor is one of the two sensors that are enabled by default on the [`<DndContext>`](../api-documentation/context-provider/) provider component.

#### Focus

In order for the Keyboard sensor to function properly, the activator element that receives the `useDraggable` [listeners](../api-documentation/draggable/usedraggable.md#listeners) **must** be able to receive focus.

The `tabindex` attribute dictates the order in which focus moves throughout the document.

* Natively interactive elements such as [buttons](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button), [anchor tags](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/a) and[ form controls ](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormControlsCollection)have a default `tabindex` value of `0`.&#x20;
* Custom elements that are intended to be interactive and receive keyboard focus need to have an explicitly assigned `tabindex="0"`(for example, `div` and `li` elements)

In other words, in order for your draggable activator elements to be able to receive keyboard focus, they _need_ to have the `tabindex` attribute set to `0` **if** they are not natively interactive elements (such as the HTML `button` element).

For this reason, the `useDraggable` hook sets the `tabindex="0"` attribute by default.

#### Keyboard shortcuts

Once a draggable activator element receives focus, the `enter` (on Windows) or `return` (on macOS) and the `space` keys can be used to initiate a drag operation and pick up the draggable item.

The arrow keys are used to move the draggable item in any given direction.

After an item is picked up, it can be dropped using the `enter` (on Windows) or `return` (on macOS) and the `space` keys.

A drag operation can be cancelled using the `escape` key. It is recommended to allow users to cancel the drag operation using the `escape` key for all sensors, not just the Keyboard sensor.

The keyboard shortcuts of the Keyboard sensor can be [customized](../api-documentation/sensors/keyboard.md#keyboard-codes), but we discourage you to do so unless you maintain support for the `enter`, `return` and `space` keys to follow the guidelines set by the third rule of ARIA.

By default, the [Keyboard sensor](../api-documentation/sensors/keyboard.md) moves in any given direction by `25` pixels when the arrow keys are pressed while dragging.

This is an arbitrary default that is likely not suited for all use-cases. We encourage you to customize this behaviour and tailor it to the context of your application using the  [`getNextCoordinates` option ](../api-documentation/sensors/keyboard.md#coordinates-getter)of the Keyboard sensor.

For example, the `useSortable` hook ships with an augmented version of the Keyboard sensor that uses the `getNextCoordinates` option behind the scenes to find the coordinates of the next sortable element in any given direction when an arrow key is pressed.

In order to let users learn how to interact with draggable elements using these keyboard shortcuts, it's important to provide screen reader instructions.

### Screen reader instructions

In order to users know how to interact with draggable items using only the keyboard, it's important to provide information to the user that their focus is currently on a draggable item, along with clear instruction  on how to pick up a a draggable item, how to move it, how to drop it and how to cancel the operation.

#### Role

To let users know that their focus is currently on a draggable item, the [`useDraggable`](../api-documentation/draggable/usedraggable.md) hook provides the [`role`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles) and [`aria-roledescription`](https://www.digitala11y.com/aria-roledescriptionproperties/), and [`aria-describedby`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA\_Techniques/Using\_the\_aria-describedby\_attribute) attributes by default:

| Attribute              | Default value             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ---------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `role`                 | `"button"`                | <p>The ARIA <code>"role"</code> attribute lets you explicitly define the role for an element, which communicates its purpose to assistive technologies.<br></p><p>If possible, we recommend you use a semantic <code>&#x3C;button></code> element for the DOM element you plan on attaching draggable listeners to. <br><br>In case that's not possible, make sure you include the <code>role="button"</code>attribute, which is the default value.</p> |
| `aria-roledescription` | `"draggable"`             | <p>Defines a human-readable, localized description for the role of an element that is read by screen readers.<br></p><p>While <code>draggable</code> is a sensible default, we recommend you customize this value to something that is tailored to your use-case.</p>                                                                                                                                                                                   |
| `aria-describedby`     | `"DndContext-[uniqueId]"` | Each draggable item is provided a unique `aria-describedby` ID that points to the voiceover instructions to be read out when a draggable item receives focus                                                                                                                                                                                                                                                                                            |

The `role` and `aria-roledescription` attributes can be customized via the [options passed to the `useDraggable` hook](../api-documentation/draggable/usedraggable.md#arguments).

To customize the `aria-describedby` instructions, refer to the section below.

#### Instructions

By default, each  [`<DndContext>`](../api-documentation/context-provider/) component renders a unique HTML element that is rendered off-screen to be used to provide these instructions to screen readers.&#x20;

The default instructions are:

> To pick up a draggable item, press space or enter. \
> While dragging, use the arrow keys to move the item in any given direction.\
> Press space or enter again to drop the item in its new position, or press escape to cancel.

We recommend you customize and localize these instructions to your application and use-case using the `screenReaderInstructions` prop of [`<DndContext>`](../api-documentation/context-provider/). &#x20;

For example, if you were building a sortable grocery shopping list, you may want to tailor the instructions like so:

> To pick up a grocery list item, press space or enter. \
> Use the up and down arrow keys to update the position of the item in the grocery list.\
> Press space or enter again to drop the item in its new position, or press escape to cancel.

If your application supports multiple languages, make sure you also translate these instructions. The `<DndContext>` component only ships with instructions in English due to bundle size concerns.

### Screen reader announcements using live regions

[Live regions](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA\_Live\_Regions) are used to notify screen readers of content changes.&#x20;

When building accessible drag and drop interfaces, live regions should be used to provide screen reader announcements in real-time of time-sensitive information of what is currently happening with draggable and droppable elements without having to move focus .

By default, each  [`<DndContext>`](../api-documentation/context-provider/) component renders a unique HTML element that is rendered off-screen to be used for live screen-reader announcements of events like when a drag operation has started, when a draggable item has been dragged over a droppable container, when a drag operation has ended, and when a drag operation has been cancelled.

These instructions can be customized using the `announcements` prop of `DndContext`.

The default announcements are:

```javascript
const defaultAnnouncements = {
  onDragStart({active}) {
    return `Picked up draggable item ${active.id}.`;
  },
  onDragOver({active, over}) {
    if (over) {
      return `Draggable item ${active.id} was moved over droppable area ${over.id}.`;
    }

    return `Draggable item ${active.id} is no longer over a droppable area.`;
  },
  onDragEnd({active, over}) {
    if (over) {
      return `Draggable item ${active.id} was dropped over droppable area ${over.id}`;
    }

    return `Draggable item ${active.id} was dropped.`;
  },
  onDragCancel({active}) {
    return `Dragging was cancelled. Draggable item ${active.id} was dropped.`;
  },
}
```

While these default announcements are sensible defaults that should cover most simple use cases, you know your application best, and we highly recommend that you customize these to provide a screen reader experience that is more tailored to the use case you are building.

{% hint style="info" %}
When authoring screen reader announcements that rely on an element's position (index) in a list, use positions rather than indices to describe the element's current position.

Here's an example of index based announcements and why you should avoid them:

> Item with index 0 was picked up. Item was moved to index 1 of 4.

Position based announcements are much more intuitive and natural:

> Item at position 1 was picked up. Item was moved to position 2 of 5.
{% endhint %}

For example, when building a sortable list, you could write custom announcements that are tailored to that use-case using position based announcements:

```javascript
function App() {
  const items = useState(['Apple', 'Orange', 'Strawberries', 'Raspberries']);
  const getPosition = (id) => items.indexOf(id) + 1; // prefer position over index
  const itemCount = items.length;
  
  const announcements = {
    onDragStart({active}) {
      return `Picked up sortable item ${active.id}. Sortable item ${active.id} is in position ${getPosition(id)} of ${itemCount}`;
    },
    onDragOver({active, over}) {
      if (over) {
        return `Sortable item ${active.id} was moved into position ${getPosition(over.id)} of ${itemCount}`;
      }
    },
    onDragEnd({active, over}) {
      if (over) {
        return `Sortable item ${active.id} was dropped at position ${getPosition(over.id)} of ${itemCount}`;
      }
    },
    onDragCancel({active}) {
      return `Dragging was cancelled. Sortable item ${active.id} was dropped.`;
    },
  };
  
  return (
    <DndContext
      accessibility={{
        announcements,
      }}
    >
```

The example above assumes that the [`closestCenter` collision detection strategy](../api-documentation/context-provider/collision-detection-algorithms.md#closest-center) is used, so the `over` property should always be defined.

If your application supports multiple languages, make sure you also translate these announcements. The `<DndContext>` component only ships with announcements in English due to bundle size concerns.
