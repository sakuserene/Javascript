---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 11 - Section 3
subtopic: Form Events
title: DOM Events - Part 11 - Form Events
topic: DOM Events
---

# DOM Events --- Part 11 (Section 3)

## `submit`

The `submit` event fires when a form is about to be submitted.

``` javascript
form.addEventListener("submit", (event) => {
  event.preventDefault();
  console.log("Submitting...");
});
```

Using `preventDefault()` lets you validate data before sending it to a
server.

## `reset`

The `reset` event occurs when a form is reset.

``` javascript
form.addEventListener("reset", () => {
  console.log("Form reset");
});
```

## `invalid`

The `invalid` event fires when a form control fails built-in HTML
validation.

``` javascript
email.addEventListener("invalid", () => {
  console.log("Invalid email");
});
```

## Form Submission Lifecycle

``` text
User fills form
      ↓
submit event
      ↓
Validation
      ↓
preventDefault()?
      ↓
Yes → Stay on page
No  → Browser submits form
```

## Custom Validation

``` javascript
form.addEventListener("submit", (event) => {
  if (password.value.length < 8) {
    event.preventDefault();
    console.log("Password must contain at least 8 characters.");
  }
});
```

## Accessibility

-   Associate labels with form controls.
-   Display clear error messages.
-   Don't rely only on color to indicate errors.
-   Return focus to invalid fields when appropriate.

## Common Mistakes

-   Forgetting `preventDefault()` during client-side validation.
-   Validating only in JavaScript and not on the server.
-   Ignoring built-in HTML validation.

## Debugging Tips

-   Log the submit event.
-   Inspect validation messages.
-   Verify event listeners are attached after the DOM loads.

## Continue

Next file:

**DOM-Events-Part-11-Section-4.md**
