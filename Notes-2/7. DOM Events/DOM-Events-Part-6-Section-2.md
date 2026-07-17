---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 6 - Section 2
subtopic: preventDefault()
title: DOM Events - Part 6 - Preventing Default Behavior
  (preventDefault)
topic: DOM Events
---

# DOM Events --- Part 6 (Section 2)

## When Does `preventDefault()` Work?

`preventDefault()` only works for **cancelable events**.

Use the `cancelable` property to check:

``` javascript
button.addEventListener("click", (event) => {
  console.log(event.cancelable);
});
```

If `event.cancelable` is `false`, calling `preventDefault()` has no
effect.

## `preventDefault()` vs `stopPropagation()`

  -----------------------------------------------------------------------
  Method                              Purpose
  ----------------------------------- -----------------------------------
  `preventDefault()`                  Cancels the browser's default
                                      action

  `stopPropagation()`                 Stops the event from moving to
                                      ancestor elements
  -----------------------------------------------------------------------

They solve different problems and are often used independently.

## Real-World Examples

### Disable Right-Click

``` javascript
document.addEventListener("contextmenu", (event) => {
  event.preventDefault();
});
```

### Prevent Dragging an Image

``` javascript
image.addEventListener("dragstart", (event) => {
  event.preventDefault();
});
```

### Validate a Form Before Submission

``` javascript
form.addEventListener("submit", (event) => {
  if (!username.value.trim()) {
    event.preventDefault();
    console.log("Username is required.");
  }
});
```

## Common Mistakes

-   Calling `preventDefault()` on non-cancelable events.
-   Assuming it stops propagation.
-   Blocking useful browser behavior without a good reason.

## Debugging Tips

-   Check `event.cancelable`.
-   Verify the correct event type.
-   Test whether the browser's default action actually exists.

## Continue

Next file:

**DOM-Events-Part-6-Section-3.md**
