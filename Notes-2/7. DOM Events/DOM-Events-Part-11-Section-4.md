---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 11 - Section 4 (Completion)
subtopic: Form Events
title: DOM Events - Part 11 - Form Events
topic: DOM Events
---

# DOM Events --- Part 11 (Section 4)

## Performance Considerations

-   Validate fields as efficiently as possible.
-   Avoid expensive validation logic on every `input` event.
-   Debounce server-side validation requests.
-   Reuse validation functions across forms.

## Security Considerations

Client-side validation improves user experience but **does not provide
security**.

Always:

-   Validate all input on the server.
-   Sanitize user input.
-   Protect against SQL injection, XSS, and other attacks.
-   Never trust values simply because JavaScript validated them.

## Knowledge Check

1.  What is the difference between `input` and `change`?
2.  When do `focus` and `blur` occur?
3.  What is the purpose of the `submit` event?
4.  Why is `preventDefault()` commonly used during form submission?
5.  Why is server-side validation still required?

## Exercises

### Beginner

1.  Display the current value while typing.
2.  Show a message when an input gains focus.
3.  Prevent a form from submitting.

### Intermediate

1.  Validate an email field using the `blur` event.
2.  Display custom messages for invalid fields.
3.  Reset a form and log the reset event.

### Advanced

1.  Build a complete registration form with client-side validation.
2.  Implement asynchronous username availability checking.

## Interview Questions

**Q:** What is the difference between `input` and `change`?

**A:** `input` fires immediately whenever the value changes, while
`change` fires only after the value has been committed.

**Q:** Why should client-side validation always be paired with
server-side validation?

**A:** Because users can bypass or modify JavaScript. The server must
independently verify all incoming data.

## Part 11 Summary

You learned:

-   How form events work.
-   The differences between `input`, `change`, `focus`, `blur`,
    `submit`, `reset`, and `invalid`.
-   Real-time and submit-time validation.
-   Accessibility, performance, and security best practices.

## Navigation

  ------------------------------------------------------------------------
  Previous                       Current                  Next
  ------------------------------ ------------------------ ----------------
  DOM Events -- Part 10          DOM Events -- Part 11    DOM Events --
                                                          Part 12:
                                                          Clipboard, Drag
                                                          & Drop, and
                                                          Other
                                                          Specialized
                                                          Events

  ------------------------------------------------------------------------
