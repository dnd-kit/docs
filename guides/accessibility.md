# Accessibility

Building accessible drag and drop interfaces is hard; **dnd kit**  has a number of sensible defaults and starting points to help you make your drag and drop interface accessible.

{% hint style="info" %}
While **dnd kit** tries to provide sensible defaults to help make your draggable and droppable interface more accessible, you know your application best. In most cases you'll want to customize and personalize these defaults so that they are tailored to the context of your application. 
{% endhint %}

#### Screen reader instructions for how to interact with draggable items

In order to let screen reader users know how to interact with draggable items using only the keyboard, it's important to provide clear instructions on how to pick up a a draggable item, how to move it, how to drop it and how to cancel the operation.

By default, each  [`<DndContext>`](../api-documentation/context-provider/) component renders a unique HTML element that is rendered off-screen to be used to provide these instructions to screen readers. The default instructions are:

> To pick up a draggable item, press the space bar.   
> While dragging, use the arrow keys to move the item around.  
> Press space again to drop the item in its new position, or press escape to cancel.

We recommend you customize these instructions to your application and use-case using the `screenReaderInstructions` prop of [`<DndContext>`](../api-documentation/context-provider/). 

#### Live regions to provide screen reader announcements

[Live regions](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions) are used to notify screen readers of content changes. We use them in **dnd kit** to provide screen reader announcements in real-time of time-sensitive information of what is currently happening with draggable and droppable elements without having to move focus .

By default, each  [`<DndContext>`](../api-documentation/context-provider/) component renders a unique HTML element that is rendered off-screen to be used for live screen-reader announcements of events like when a drag operation has started, when a draggable item has been dragged over a droppable container, when a drag operation has ended, and when a drag operation has been cancelled.

#### Aria attributes

| Attribute | Default value | Description |
| :--- | :--- | :--- |
| `role` | `"button"` | If possible, we recommend you use a semantic `<button>` element for the DOM element you plan on attaching draggable listeners to. In case that's not possible, make sure you include the `role="button"`attribute, which is the default value. |
| `aria-roledescription` | `"draggable"` | While `draggable` is a sensible default, we recommend you customize this value to something that is  |
| `aria-describedby` | `"DndContext-[uniqueId]"` | Each draggable item is provided a unique `aria-describedby` ID that points to the voiceover instructions to be read out when a draggable item receives focus. |

### 

