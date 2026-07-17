---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 12 - Section 2
subtopic: Drag and Drop Events
title: DOM Events - Part 12 - Clipboard, Drag & Drop, and Other
  Specialized Events
topic: DOM Events
---

# DOM Events --- Part 12 (Section 2)

## Drag and Drop Events

HTML5 provides built-in events for drag-and-drop interactions.

Common events:

-   `dragstart`
-   `drag`
-   `dragenter`
-   `dragover`
-   `dragleave`
-   `drop`
-   `dragend`

## Drag Lifecycle

``` text
dragstart
    ↓
drag
    ↓
dragenter
    ↓
dragover
    ↓
drop
    ↓
dragend
```

## Making an Element Draggable

``` html
<div id="card" draggable="true">
  Drag me
</div>
```

``` javascript
const card = document.getElementById("card");

card.addEventListener("dragstart", () => {
  console.log("Dragging started");
});
```

Output:

``` text
Dragging started
```

## Allowing a Drop

By default, dropping is not allowed.

``` javascript
dropZone.addEventListener("dragover", (event) => {
  event.preventDefault();
});
```

Calling `preventDefault()` tells the browser that the drop target
accepts drops.

## Handling the Drop

``` javascript
dropZone.addEventListener("drop", (event) => {
  event.preventDefault();
  console.log("Item dropped");
});
```

Output:

``` text
Item dropped
```

## Common Mistakes

-   Forgetting `draggable="true"`.
-   Forgetting `event.preventDefault()` inside `dragover`.
-   Updating the DOM before the `drop` event.

## Debugging Tips

-   Log each drag event to verify the lifecycle.
-   Confirm the drop target receives `dragover`.
-   Ensure the correct element is draggable.

## Continue

Next file:

**DOM-Events-Part-12-Section-3.md**
