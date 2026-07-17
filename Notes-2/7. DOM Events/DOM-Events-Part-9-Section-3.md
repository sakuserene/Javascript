---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 9 - Section 3
subtopic: Mouse Events
title: DOM Events - Part 9 - Mouse Events
topic: DOM Events
---

# DOM Events --- Part 9 (Section 3)

## Mouse Position Properties

Mouse events provide coordinates describing where the pointer is
located.

### `clientX` and `clientY`

Coordinates relative to the visible browser viewport.

``` javascript
document.addEventListener("click", (event) => {
  console.log(event.clientX, event.clientY);
});
```

### `pageX` and `pageY`

Coordinates relative to the entire document, including any scrolling.

### `screenX` and `screenY`

Coordinates relative to the user's screen.

## Mouse Buttons

### `button`

Indicates which mouse button triggered the event.

Typical values:

-   `0` → Left button
-   `1` → Middle button
-   `2` → Right button

### `buttons`

Represents all buttons currently pressed as a bitmask.

## Drag Example

``` javascript
box.addEventListener("mousemove", (event) => {
  console.log(`(${event.clientX}, ${event.clientY})`);
});
```

This pattern forms the basis of drawing apps, sliders, and drag-and-drop
interfaces.

## Performance Tips

-   Avoid expensive work inside `mousemove` because it can fire many
    times per second.
-   Consider throttling or `requestAnimationFrame()` for smooth
    animations.

## Common Mistakes

-   Confusing viewport (`clientX`) with page (`pageX`) coordinates.
-   Assuming `button` and `buttons` are the same.
-   Performing heavy DOM updates during every mouse movement.

## Continue

Next file:

**DOM-Events-Part-9-Section-4.md**
