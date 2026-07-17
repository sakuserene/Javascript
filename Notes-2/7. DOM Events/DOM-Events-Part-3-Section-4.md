---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 3 - Section 4 (Completion)
subtopic: The Event Object
title: DOM Events - Part 3 - The Event Object
topic: DOM Events
---

# DOM Events --- Part 3 (Section 4)

## Real-World Example

``` html
<form id="login">
  <input name="username">
  <button>Login</button>
</form>
```

``` javascript
const form = document.getElementById("login");

form.addEventListener("submit", (event) => {
  event.preventDefault();

  console.log("Event type:", event.type);
  console.log("Target:", event.target);
  console.log("Time:", event.timeStamp);
});
```

### Sample Output

``` text
Event type: submit
Target: <form id="login">...</form>
Time: 18642.17
```

## Common Mistakes

-   Forgetting to receive the `event` parameter.
-   Confusing `target` with `currentTarget`.
-   Calling `preventDefault()` on events that are not cancelable.
-   Stopping propagation unnecessarily.
-   Assuming every event has the same properties.

## Performance

-   Avoid expensive work inside frequently fired events.
-   Reuse handlers where practical.
-   Keep event callbacks focused and small.

## Accessibility

-   Support keyboard interactions.
-   Preserve default browser behavior unless there is a clear reason to
    override it.
-   Test with keyboard navigation and assistive technologies.

## Security

-   Never trust user input contained in event targets.
-   Validate and sanitize input before processing.
-   Avoid injecting untrusted HTML into the DOM.

## Knowledge Check

1.  Who creates the Event Object?
2.  What is the difference between `target` and `currentTarget`?
3.  What does `preventDefault()` do?
4.  When would `stopPropagation()` be useful?
5.  Why is the Event Object important?

## Exercises

### Beginner

1.  Log `event.type`.
2.  Log `event.target`.
3.  Prevent a form submission.

### Intermediate

1.  Compare `target` and `currentTarget`.
2.  Display mouse coordinates.
3.  Log the key pressed during `keydown`.

### Advanced

1.  Build a reusable event logger.
2.  Investigate different Event Object properties for several event
    types.

## Interview Questions

**Q:** What is the Event Object?

**A:** It is a browser-created object containing information about a
specific event and is automatically passed to event listeners.

**Q:** Why is `event.target` useful?

**A:** It identifies the element where the event originally occurred.

## Part 3 Summary

You have learned:

-   What the Event Object is.
-   How the browser creates it.
-   Common properties and methods.
-   How to inspect and use event information.
-   Best practices for writing robust event-driven code.

## Navigation

  ------------------------------------------------------------------------
  Previous                       Current                  Next
  ------------------------------ ------------------------ ----------------
  DOM Events -- Part 2           DOM Events -- Part 3     DOM Events --
                                                          Part 4: Event
                                                          Flow (Capturing,
                                                          Target, and
                                                          Bubbling)

  ------------------------------------------------------------------------
