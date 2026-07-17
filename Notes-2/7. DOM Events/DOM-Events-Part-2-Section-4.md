---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 2 - Section 4 (Completion)
subtopic: Event Listeners (addEventListener)
title: DOM Events - Part 2 - Event Listeners (addEventListener)
topic: DOM Events
---

# DOM Events --- Part 2 (Section 4)

## Real-World Example

``` javascript
const form = document.querySelector("#login-form");

function handleSubmit(event) {
  console.log("Form submitted");
}

form.addEventListener("submit", handleSubmit);
```

When the form is submitted, the browser invokes `handleSubmit()`.

## Common Pitfalls

-   Registering duplicate listeners accidentally.
-   Using anonymous listeners when they must later be removed.
-   Attaching listeners before DOM elements exist.
-   Registering listeners inside repeatedly called functions.

## Accessibility

-   Ensure interactive elements are keyboard accessible.
-   Prefer semantic elements (`button`, `a`, `input`) over clickable
    `div`s.
-   Don't rely on mouse events alone.

## Security

-   Validate all user input inside event handlers.
-   Avoid inserting untrusted HTML.
-   Don't expose sensitive data through client-side handlers.

## Knowledge Check

1.  What is an event listener?
2.  Why is `addEventListener()` preferred over inline handlers?
3.  When should you use a named callback?
4.  What does `{ once: true }` do?
5.  Why might `{ passive: true }` improve performance?

## Exercises

### Beginner

1.  Register a click listener on a button.
2.  Register an input listener on a text field.
3.  Register focus and blur listeners.

### Intermediate

1.  Attach two click listeners to the same button.
2.  Remove a listener using `removeEventListener()`.
3.  Experiment with `{ once: true }`.

### Advanced

1.  Build a reusable event registration helper.
2.  Measure the effects of repeatedly registering listeners.

## Interview Questions

**Q:** What is the purpose of `addEventListener()`?

**A:** It registers a callback function that the browser executes
whenever a specified event occurs on a target.

**Q:** Why doesn't `removeEventListener()` work with a new anonymous
function?

**A:** Because JavaScript compares function references, not function
bodies.

## Part 2 Summary

You learned:

-   How event listeners connect browser events to JavaScript.
-   The syntax of `addEventListener()`.
-   Callback functions.
-   Named vs anonymous handlers.
-   Multiple listeners.
-   Removing listeners.
-   Listener options (`once`, `capture`, `passive`).
-   Performance and accessibility fundamentals.

## Navigation

  ------------------------------------------------------------------------
  Previous                       Current                  Next
  ------------------------------ ------------------------ ----------------
  DOM Events -- Part 1           DOM Events -- Part 2     DOM Events --
                                                          Part 3: The
                                                          Event Object
                                                          (`event`)

  ------------------------------------------------------------------------
