---
title: DOM Events - Part 11 - Keyboard Events
---

# DOM Events --- Part 11 (Section 1)

## Keyboard Events

JavaScript can respond to keyboard input using:

-   `keydown`
-   `keyup`
-   `keypress` (deprecated)

### `keydown`

``` javascript
document.addEventListener("keydown", (event) => {
  console.log(event.key);
});
```

Fires when a key is pressed.

### `keyup`

``` javascript
document.addEventListener("keyup", (event) => {
  console.log(event.key);
});
```

Fires when a pressed key is released.

## Useful Properties

-   `event.key`
-   `event.code`
-   `event.ctrlKey`
-   `event.shiftKey`
-   `event.altKey`

### Example

``` javascript
document.addEventListener("keydown", (e) => {
  if (e.ctrlKey && e.key === "s") {
    e.preventDefault();
    console.log("Save shortcut");
  }
});
```

## Continue

Next: DOM-Events-Part-11-Section-2.md
