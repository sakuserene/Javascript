---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 12 - Section 3
subtopic: Other Specialized Events
title: DOM Events - Part 12 - Clipboard, Drag & Drop, and Other
  Specialized Events
topic: DOM Events
---

# DOM Events --- Part 12 (Section 3)

## `scroll`

The `scroll` event fires whenever the document or an element is
scrolled.

``` javascript
window.addEventListener("scroll", () => {
  console.log(window.scrollY);
});
```

Use cases:

-   Infinite scrolling
-   Sticky headers
-   Lazy loading

## `resize`

Triggered when the browser window changes size.

``` javascript
window.addEventListener("resize", () => {
  console.log(window.innerWidth);
});
```

Useful for responsive layouts.

## `wheel`

Fires when the mouse wheel or touchpad scroll gesture is used.

``` javascript
document.addEventListener("wheel", (event) => {
  console.log(event.deltaY);
});
```

## `visibilitychange`

Occurs when the page becomes visible or hidden.

``` javascript
document.addEventListener("visibilitychange", () => {
  console.log(document.visibilityState);
});
```

Common uses:

-   Pause videos
-   Pause animations
-   Suspend polling

## `beforeunload`

Runs just before the user leaves the page.

``` javascript
window.addEventListener("beforeunload", (event) => {
  event.preventDefault();
  event.returnValue = "";
});
```

Commonly used to warn about unsaved changes.

## Performance Tips

-   Throttle expensive `scroll` and `resize` handlers.
-   Use `requestAnimationFrame()` for smooth visual updates.
-   Avoid unnecessary DOM work inside frequently fired events.

## Continue

Next file:

**DOM-Events-Part-12-Section-4.md**
