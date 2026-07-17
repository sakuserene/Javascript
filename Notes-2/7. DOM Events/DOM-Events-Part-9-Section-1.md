---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 9 - Section 1
subtopic: Mouse Events
title: DOM Events - Part 9 - Mouse Events
topic: DOM Events
---

# DOM Events --- Part 9 (Section 1)

## Introduction

Mouse events allow JavaScript to react to interactions performed with a
mouse, touchpad, or similar pointing device.

Common examples include:

-   `click`
-   `dblclick`
-   `mousedown`
-   `mouseup`

## Why Different Mouse Events?

Each event represents a different stage of user interaction.

``` text
Mouse button pressed
        ↓
mousedown
        ↓
Mouse button released
        ↓
mouseup
        ↓
click
```

If the user quickly repeats the action:

``` text
click
 ↓
click
 ↓
dblclick
```

## Event Order

``` text
mousedown
    ↓
mouseup
    ↓
click
```

For a double-click:

``` text
mousedown
mouseup
click
mousedown
mouseup
click
dblclick
```

## Example

``` html
<button id="save">Save</button>
```

``` javascript
const button = document.getElementById("save");

button.addEventListener("mousedown", () => console.log("Pressed"));
button.addEventListener("mouseup", () => console.log("Released"));
button.addEventListener("click", () => console.log("Clicked"));
```

### Output

``` text
Pressed
Released
Clicked
```

## Key Takeaways

-   Mouse interactions generate multiple events.
-   `click` is not the first event fired.
-   Understanding event order helps build responsive interfaces.

## Continue

Next file:

**DOM-Events-Part-9-Section-2.md**
