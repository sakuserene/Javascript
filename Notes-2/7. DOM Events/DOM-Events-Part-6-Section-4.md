---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 6 - Section 4 (Completion)
subtopic: preventDefault()
title: DOM Events - Part 6 - Preventing Default Behavior
  (preventDefault)
topic: DOM Events
---

# DOM Events --- Part 6 (Section 4)

## Knowledge Check

1.  What is a browser's default action?
2.  What does `preventDefault()` do?
3.  Does `preventDefault()` stop event propagation?
4.  What does `event.cancelable` indicate?
5.  Give three situations where `preventDefault()` is useful.

## Common Pitfalls

-   Calling `preventDefault()` when there is no default browser action.
-   Forgetting that it does **not** stop propagation.
-   Preventing expected browser behaviour without providing an
    alternative.
-   Using it as a substitute for validation or authorization.

## Debugging Checklist

-   Is the correct event type being handled?
-   Is the event cancelable?
-   Is the listener attached to the expected element?
-   Is another script triggering the default action?

## Interview Questions

**Q:** Why do browsers provide default actions?

**A:** They give standard behaviour for common HTML elements such as
links, forms, and inputs so pages are usable without custom JavaScript.

**Q:** When should you avoid `preventDefault()`?

**A:** When the browser's built-in behaviour improves usability or
accessibility and there is no strong reason to override it.

## Part 6 Summary

You learned:

-   What browser default actions are.
-   How `preventDefault()` works.
-   The role of `event.cancelable`.
-   Common real-world use cases.
-   Best practices, accessibility, security, and debugging
    considerations.

## Navigation

  ---------------------------------------------------------------------------------------
  Previous                       Current                  Next
  ------------------------------ ------------------------ -------------------------------
  DOM Events -- Part 5           DOM Events -- Part 6     DOM Events -- Part 7: Stopping
                                                          Propagation
                                                          (`stopPropagation()` and
                                                          `stopImmediatePropagation()`)

  ---------------------------------------------------------------------------------------
