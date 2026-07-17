---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 7 - Section 4 (Completion)
subtopic: stopPropagation() and stopImmediatePropagation()
title: DOM Events - Part 7 - Stopping Propagation
topic: DOM Events
---

# DOM Events --- Part 7 (Section 4)

## Knowledge Check

1.  What is event propagation?
2.  What is the difference between `stopPropagation()` and
    `stopImmediatePropagation()`?
3.  Does `stopPropagation()` prevent the browser's default action?
4.  When should propagation be stopped?
5.  Why should propagation control be used sparingly?

## Exercises

### Beginner

1.  Create a parent and child element.
2.  Log which listener executes first.
3.  Prevent the parent listener from executing.

### Intermediate

1.  Attach two listeners to one button and compare the behavior of:
    -   `stopPropagation()`
    -   `stopImmediatePropagation()`
2.  Build a modal that closes only when the overlay is clicked.

### Advanced

1.  Create a nested menu system using propagation control.
2.  Explain how propagation affects event delegation.

## Debugging Checklist

-   Verify where listeners are attached.
-   Check whether another listener has already stopped propagation.
-   Log `event.target`, `event.currentTarget`, and `event.eventPhase`.
-   Inspect listener registration order.

## Interview Questions

**Q:** When should `stopImmediatePropagation()` be used?

**A:** When you must stop both ancestor listeners and any remaining
listeners on the same element.

**Q:** Why shouldn't propagation be stopped by default?

**A:** Because it can interfere with reusable components, event
delegation, and make applications harder to maintain and debug.

## Part 7 Summary

You learned:

-   How propagation travels through the DOM.
-   The purpose of `stopPropagation()`.
-   The purpose of `stopImmediatePropagation()`.
-   Practical use cases for both methods.
-   Performance, accessibility, and debugging considerations.

## Navigation

  ------------------------------------------------------------------------
  Previous                       Current                  Next
  ------------------------------ ------------------------ ----------------
  DOM Events -- Part 6           DOM Events -- Part 7     DOM Events --
                                                          Part 8: Custom
                                                          Events

  ------------------------------------------------------------------------
