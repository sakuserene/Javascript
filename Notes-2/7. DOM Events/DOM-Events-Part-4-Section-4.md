---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 4 - Section 4 (Completion)
subtopic: Event Flow
title: DOM Events - Part 4 - Event Flow (Capturing, Target, and
  Bubbling)
topic: DOM Events
---

# DOM Events --- Part 4 (Section 4)

## Accessibility Considerations

-   Ensure keyboard users can trigger the same functionality as mouse
    users.
-   Avoid stopping propagation unless necessary, as it may interfere
    with assistive technologies.
-   Test nested interactive elements carefully.

## Security Considerations

-   Do not trust event data from user-controlled inputs.
-   Validate and sanitize data before processing.
-   Prevent unintended actions with proper permission checks.

## Knowledge Check

1.  What are the three phases of event flow?
2.  What is the purpose of `event.eventPhase`?
3.  When should `capture: true` be used?
4.  What is the difference between `stopPropagation()` and
    `stopImmediatePropagation()`?
5.  Why is understanding propagation important?

## Exercises

### Beginner

1.  Draw the event propagation path for nested elements.
2.  Register one listener during bubbling.
3.  Register one listener during capturing.

### Intermediate

1.  Log `event.eventPhase`.
2.  Compare bubbling and capturing outputs.
3.  Stop propagation in a nested component.

### Advanced

1.  Build a nested menu demonstrating all three phases.
2.  Explain how propagation enables event delegation.

## Interview Questions

**Q:** What are the three phases of event propagation?

**A:** Capturing, Target, and Bubbling.

**Q:** Why does a parent listener execute after a child listener by
default?

**A:** Because listeners are registered for the bubbling phase unless
`capture: true` is specified.

## Part 4 Summary

You learned:

-   The three phases of event flow.
-   How events travel through the DOM.
-   Capturing versus bubbling.
-   `event.eventPhase`.
-   How and when to stop propagation.
-   Why propagation is the foundation of event delegation.

## Navigation

  ------------------------------------------------------------------------
  Previous                       Current                  Next
  ------------------------------ ------------------------ ----------------
  DOM Events -- Part 3           DOM Events -- Part 4     DOM Events --
                                                          Part 5: Event
                                                          Delegation

  ------------------------------------------------------------------------
