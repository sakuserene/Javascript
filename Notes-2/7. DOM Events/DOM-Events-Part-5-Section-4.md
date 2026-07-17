---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 5 - Section 4 (Completion)
subtopic: Event Delegation
title: DOM Events - Part 5 - Event Delegation
topic: DOM Events
---

# DOM Events --- Part 5 (Section 4)

## Accessibility

-   Ensure delegated interactions are reachable with the keyboard.
-   Prefer semantic controls (`button`, `a`, `input`) instead of
    clickable `div` elements.
-   Preserve visible focus indicators.

## Security

-   Never trust values coming from user-controlled elements.
-   Validate actions before performing sensitive operations.
-   Avoid inserting untrusted HTML into the DOM.

## Knowledge Check

1.  What is event delegation?
2.  Why does delegation rely on event bubbling?
3.  When should you use `closest()`?
4.  Why is delegation useful for dynamically created elements?
5.  What are the performance advantages of delegation?

## Exercises

### Beginner

1.  Create a delegated click listener for a list.
2.  Print the text of the clicked list item.
3.  Ignore clicks outside list items.

### Intermediate

1.  Build a delegated delete button for a todo list.
2.  Use `closest()` to identify the correct button.
3.  Add new list items dynamically and verify the listener still works.

### Advanced

1.  Build a delegated CRUD interface using a single listener.
2.  Compare memory usage between 1,000 individual listeners and one
    delegated listener.

## Interview Questions

**Q:** What is event delegation?

**A:** Event delegation is the practice of attaching one listener to a
parent element and using event bubbling to handle events from its
descendant elements.

**Q:** Why is event delegation efficient?

**A:** It reduces the number of event listeners, lowers memory usage,
simplifies maintenance, and automatically supports dynamically added
elements.

## Part 5 Summary

You learned:

-   The concept of event delegation.
-   How delegation works through bubbling.
-   Using `event.target`, `matches()`, and `closest()`.
-   Handling dynamic elements.
-   Performance and scalability benefits.
-   Debugging delegated event handlers.

## Navigation

  ------------------------------------------------------------------------------
  Previous                       Current                  Next
  ------------------------------ ------------------------ ----------------------
  DOM Events -- Part 4           DOM Events -- Part 5     DOM Events -- Part 6:
                                                          Preventing Default
                                                          Behavior
                                                          (`preventDefault()`)

  ------------------------------------------------------------------------------
