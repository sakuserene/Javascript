---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 4 - Section 3
subtopic: Event Flow
title: DOM Events - Part 4 - Event Flow (Capturing, Target, and
  Bubbling)
topic: DOM Events
---

# DOM Events --- Part 4 (Section 3)

## Controlling Event Propagation

Sometimes you do **not** want an event to continue through the
propagation path.

The Event Object provides methods to control propagation.

### `stopPropagation()`

Stops the event from continuing to ancestor elements.

``` javascript
parent.addEventListener("click", () => {
  console.log("Parent");
});

child.addEventListener("click", (event) => {
  event.stopPropagation();
  console.log("Child");
});
```

Clicking the child produces:

``` text
Child
```

The parent listener is never reached.

------------------------------------------------------------------------

### `stopImmediatePropagation()`

Stops propagation **and** prevents any remaining listeners on the same
element from executing.

``` javascript
button.addEventListener("click", (event) => {
  event.stopImmediatePropagation();
  console.log("First");
});

button.addEventListener("click", () => {
  console.log("Second");
});
```

Output:

``` text
First
```

The second listener never runs.

------------------------------------------------------------------------

## Real-World Uses

Use propagation control when:

-   Closing dropdown menus
-   Preventing modal background clicks
-   Building custom context menus
-   Creating nested interactive components

Avoid using it as a default solution because it can make applications
harder to debug.

------------------------------------------------------------------------

## Common Pitfalls

-   Calling `stopPropagation()` without understanding why parent
    listeners stop working.
-   Assuming it also prevents the browser's default behaviour (it does
    not).
-   Overusing propagation control instead of designing better event
    handling.

------------------------------------------------------------------------

## Performance Notes

-   Allow events to propagate naturally unless you have a specific
    reason to stop them.
-   Excessive propagation control can complicate large applications.
-   Event delegation (next lesson) often reduces the number of listeners
    needed.

## Continue

Next file:

**DOM-Events-Part-4-Section-4.md**
