---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 10 - Section 4 (Completion)
subtopic: Keyboard Events
title: DOM Events - Part 10 - Keyboard Events
topic: DOM Events
---

# DOM Events --- Part 10 (Section 4)

## Knowledge Check

1.  What is the difference between `keydown` and `keyup`?
2.  How does `event.key` differ from `event.code`?
3.  What does `event.repeat` indicate?
4.  When should `preventDefault()` be used with keyboard shortcuts?
5.  Why is keyboard accessibility important?

## Exercises

### Beginner

1.  Display the last key pressed.
2.  Log whether the Ctrl key is pressed.
3.  Detect the Enter key.

### Intermediate

1.  Implement a Ctrl + S shortcut.
2.  Navigate a menu using arrow keys.
3.  Ignore repeated key presses using `event.repeat`.

### Advanced

1.  Build a keyboard shortcut manager.
2.  Create a simple game controlled entirely by the keyboard.

## Debugging Checklist

-   Verify the correct element has focus.
-   Log `event.key`, `event.code`, and modifier keys.
-   Check whether `preventDefault()` is affecting behavior.
-   Test shortcuts across browsers and operating systems.

## Interview Questions

**Q:** What is the difference between `event.key` and `event.code`?

**A:** `event.key` represents the character or action produced, while
`event.code` represents the physical key on the keyboard.

**Q:** Why is `event.repeat` useful?

**A:** It allows applications to distinguish the initial key press from
repeated events generated while the key is held down.

## Part 10 Summary

You learned:

-   The keyboard event lifecycle.
-   `keydown` and `keyup`.
-   `event.key` and `event.code`.
-   Modifier keys.
-   Keyboard shortcuts.
-   Key repeat behavior.
-   Accessibility and performance considerations.

## Navigation

  ------------------------------------------------------------------------
  Previous                       Current                  Next
  ------------------------------ ------------------------ ----------------
  DOM Events -- Part 9           DOM Events -- Part 10    DOM Events --
                                                          Part 11: Form
                                                          Events

  ------------------------------------------------------------------------
