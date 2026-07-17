---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 7 - Section 1
subtopic: stopPropagation() and stopImmediatePropagation()
title: DOM Events - Part 7 - Stopping Propagation
topic: DOM Events
---

# DOM Events --- Part 7 (Section 1)

## Introduction

Event propagation allows an event to travel through the DOM.

Sometimes, however, you want the event to stop before it reaches other
listeners.

JavaScript provides two methods:

-   `event.stopPropagation()`
-   `event.stopImmediatePropagation()`

## Why Stop Propagation?

Consider a modal dialog.

Clicking inside the modal should **not** trigger the page click handler
that closes the modal.

Stopping propagation prevents parent listeners from responding.

## `stopPropagation()`

Stops the event from continuing to ancestor elements.

``` javascript
modal.addEventListener("click", (event) => {
  event.stopPropagation();
});
```

## Example

``` html
<div id="parent">
  <button id="child">Click</button>
</div>
```

``` javascript
parent.addEventListener("click", () => console.log("Parent"));

child.addEventListener("click", (event) => {
  event.stopPropagation();
  console.log("Child");
});
```

Output:

``` text
Child
```

The parent listener is never reached.

## Key Takeaways

-   Propagation controls event travel through the DOM.
-   `stopPropagation()` affects ancestor listeners only.
-   It does not cancel the browser's default action.

## Continue

Next file:

**DOM-Events-Part-7-Section-2.md**
