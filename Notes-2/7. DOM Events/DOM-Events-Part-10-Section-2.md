---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 10 - Section 2
subtopic: Keyboard Events
title: DOM Events - Part 10 - Keyboard Events
topic: DOM Events
---

# DOM Events --- Part 10 (Section 2)

## Modifier Keys

Keyboard events expose the state of modifier keys.

``` javascript
document.addEventListener("keydown", (event) => {
  console.log(event.ctrlKey);
  console.log(event.shiftKey);
  console.log(event.altKey);
  console.log(event.metaKey);
});
```

Properties:

-   `ctrlKey`
-   `shiftKey`
-   `altKey`
-   `metaKey`

Each returns `true` or `false`.

## Keyboard Shortcuts

``` javascript
document.addEventListener("keydown", (event) => {
  if (event.ctrlKey && event.key === "s") {
    event.preventDefault();
    console.log("Save shortcut");
  }
});
```

Output:

``` text
Save shortcut
```

## Preventing Default Keyboard Actions

Some shortcuts already have browser actions.

``` javascript
document.addEventListener("keydown", (event) => {
  if (event.ctrlKey && event.key === "p") {
    event.preventDefault();
    console.log("Custom print");
  }
});
```

## Common Mistakes

-   Confusing `event.key` with `event.code`.
-   Forgetting to call `preventDefault()` when overriding browser
    shortcuts.
-   Ignoring platform differences (`Meta` on macOS vs `Ctrl` on
    Windows/Linux).

## Debugging Tips

-   Log the entire event object.
-   Test shortcuts across browsers.
-   Verify focus is on the expected element.

## Continue

Next file:

**DOM-Events-Part-10-Section-3.md**
