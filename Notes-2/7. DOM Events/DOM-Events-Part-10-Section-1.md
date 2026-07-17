---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 10 - Section 1
subtopic: Keyboard Events
title: DOM Events - Part 10 - Keyboard Events
topic: DOM Events
---

# DOM Events --- Part 10 (Section 1)

## Introduction

Keyboard events let JavaScript respond when users press and release
keys.

The primary keyboard events are:

-   `keydown`
-   `keyup`

## Keyboard Event Lifecycle

When a key is pressed:

``` text
Key Press
   ↓
keydown
   ↓
(optional repeated keydown while held)
   ↓
Key Released
   ↓
keyup
```

## `keydown`

Fires as soon as a key is pressed.

``` javascript
document.addEventListener("keydown", (event) => {
  console.log("Pressed:", event.key);
});
```

## `keyup`

Fires when the key is released.

``` javascript
document.addEventListener("keyup", (event) => {
  console.log("Released:", event.key);
});
```

## `event.key` vs `event.code`

### `event.key`

Represents the character or action produced.

Examples:

-   `a`
-   `A`
-   `Enter`
-   `ArrowLeft`

### `event.code`

Represents the physical key location.

Examples:

-   `KeyA`
-   `Enter`
-   `ArrowLeft`

## Example

``` javascript
document.addEventListener("keydown", (event) => {
  console.log(event.key, event.code);
});
```

Example output after pressing **A**:

``` text
a KeyA
```

## Key Takeaways

-   `keydown` fires when a key is pressed.
-   `keyup` fires when the key is released.
-   `event.key` represents the resulting key value.
-   `event.code` represents the physical keyboard key.

## Continue

Next file:

**DOM-Events-Part-10-Section-2.md**
