---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 7 - Section 2
subtopic: stopPropagation() and stopImmediatePropagation()
title: DOM Events - Part 7 - Stopping Propagation
topic: DOM Events
---

# DOM Events --- Part 7 (Section 2)

## `stopImmediatePropagation()`

While `stopPropagation()` prevents the event from reaching ancestor
elements, it does **not** stop other listeners attached to the same
element.

For that, use `stopImmediatePropagation()`.

## Example

``` javascript
button.addEventListener("click", (event) => {
  event.stopImmediatePropagation();
  console.log("First listener");
});

button.addEventListener("click", () => {
  console.log("Second listener");
});
```

Output:

``` text
First listener
```

The second listener never executes.

## Comparison

  ----------------------------------------------------------------------------------------------
  Method                         Stops ancestor          Stops remaining listeners on same
                                 listeners               element
  ------------------------------ ----------------------- ---------------------------------------
  `stopPropagation()`            ✅ Yes                  ❌ No

  `stopImmediatePropagation()`   ✅ Yes                  ✅ Yes
  ----------------------------------------------------------------------------------------------

## Real-World Uses

-   Complex dropdown menus
-   Nested modals
-   Interactive dashboards
-   Preventing duplicate event handling

## Debugging Tips

-   Check whether another listener is stopping propagation.
-   Log execution order with `console.log()`.
-   Inspect listeners using browser DevTools.

## Continue

Next file:

**DOM-Events-Part-7-Section-3.md**
