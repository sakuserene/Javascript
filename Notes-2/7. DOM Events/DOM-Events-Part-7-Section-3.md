---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 7 - Section 3
subtopic: stopPropagation() and stopImmediatePropagation()
title: DOM Events - Part 7 - Stopping Propagation
topic: DOM Events
---

# DOM Events --- Part 7 (Section 3)

## Combining Propagation Methods

Sometimes an application needs to stop both the browser's default action
**and** event propagation.

``` javascript
link.addEventListener("click", (event) => {
  event.preventDefault();
  event.stopPropagation();

  console.log("Handled entirely by JavaScript");
});
```

### Result

-   The browser does **not** follow the link.
-   Parent click listeners do **not** execute.

## Production Example --- Modal Window

``` javascript
overlay.addEventListener("click", () => {
  closeModal();
});

modal.addEventListener("click", (event) => {
  event.stopPropagation();
});
```

Clicking inside the modal keeps it open, while clicking outside closes
it.

## Production Example --- Dropdown Menu

``` javascript
document.addEventListener("click", closeMenu);

menu.addEventListener("click", (event) => {
  event.stopPropagation();
});
```

This prevents the global click handler from immediately closing the
menu.

## Performance

-   Stop propagation only when necessary.
-   Avoid unnecessary propagation control because it complicates
    debugging.

## Accessibility

-   Ensure keyboard interactions still work correctly.
-   Test with assistive technologies after changing propagation.

## Common Pitfalls

-   Confusing `preventDefault()` with `stopPropagation()`.
-   Forgetting that another listener may already have stopped
    propagation.
-   Using propagation control where better event design would suffice.

## Continue

Next file:

**DOM-Events-Part-7-Section-4.md**
