---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 10 - Section 3
subtopic: Keyboard Events
title: DOM Events - Part 10 - Keyboard Events
topic: DOM Events
---

# DOM Events --- Part 10 (Section 3)

## Key Repeat

When a key is held down, the browser repeatedly fires `keydown`.

Use `event.repeat` to detect this.

``` javascript
document.addEventListener("keydown", (event) => {
  console.log(event.key, event.repeat);
});
```

Example output while holding **A**:

``` text
a false
a true
a true
a true
```

The first event has `repeat === false`. Subsequent repeated events have
`repeat === true`.

## Building Keyboard-Driven Interfaces

Keyboard events are commonly used for:

-   Command palettes
-   Games
-   Text editors
-   Search boxes
-   Spreadsheet navigation
-   Accessibility shortcuts

### Example: Arrow Key Navigation

``` javascript
document.addEventListener("keydown", (event) => {
  switch (event.key) {
    case "ArrowUp":
      console.log("Move up");
      break;
    case "ArrowDown":
      console.log("Move down");
      break;
  }
});
```

## Accessibility

Keyboard accessibility is essential.

Best practices:

-   Ensure all interactive features are usable without a mouse.
-   Preserve standard keyboard behavior unless there is a strong reason
    to override it.
-   Support common navigation keys such as Tab, Enter, Space, and
    Escape.

## Performance

-   Keep keyboard handlers lightweight.
-   Avoid unnecessary DOM updates on every `keydown`.
-   Debounce or throttle expensive work when appropriate.

## Common Mistakes

-   Ignoring `event.repeat`.
-   Blocking browser shortcuts unnecessarily.
-   Relying only on mouse interactions.

## Continue

Next file:

**DOM-Events-Part-10-Section-4.md**
