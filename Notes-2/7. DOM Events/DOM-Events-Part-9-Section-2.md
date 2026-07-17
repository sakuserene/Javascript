---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 9 - Section 2
subtopic: Mouse Events
title: DOM Events - Part 9 - Mouse Events
topic: DOM Events
---

# DOM Events --- Part 9 (Section 2)

## Hover-Related Mouse Events

The browser provides several events for pointer movement.

### `mouseover`

Fires when the pointer enters an element **or one of its child
elements**.

### `mouseenter`

Fires only when the pointer enters the element itself.

Unlike `mouseover`, it does **not** fire again when moving between child
elements.

## Leaving an Element

### `mouseout`

Fires when the pointer leaves an element or enters one of its children.

### `mouseleave`

Fires only when the pointer leaves the element entirely.

## Tracking Pointer Movement

### `mousemove`

Runs whenever the pointer moves across an element.

``` javascript
box.addEventListener("mousemove", (event) => {
  console.log(event.clientX, event.clientY);
});
```

Useful for:

-   Drawing applications
-   Games
-   Drag interactions
-   Tooltips

## Right-Click

### `contextmenu`

Triggered when the user requests the context menu.

``` javascript
document.addEventListener("contextmenu", (event) => {
  event.preventDefault();
  console.log("Custom menu");
});
```

## Common Mistakes

-   Confusing `mouseover` with `mouseenter`.
-   Using `mousemove` for expensive operations without optimization.
-   Forgetting that `contextmenu` still has a browser default action
    unless prevented.

## Debugging Tips

-   Log the event type to confirm which event is firing.
-   Test nested elements when comparing `mouseover` and `mouseenter`.
-   Inspect pointer coordinates with `clientX` and `clientY`.

## Continue

Next file:

**DOM-Events-Part-9-Section-3.md**
