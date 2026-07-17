---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 6 - Section 1
subtopic: preventDefault()
title: DOM Events - Part 6 - Preventing Default Behavior
  (preventDefault)
topic: DOM Events
---

# DOM Events --- Part 6 (Section 1)

## Introduction

Many HTML elements have **default browser behaviors**.

Examples:

-   Links navigate.
-   Forms submit.
-   Checkboxes toggle.
-   Right-click opens a context menu.

Sometimes your application needs to override these behaviors.

JavaScript provides **`event.preventDefault()`**.

## What is a Default Action?

A default action is the behavior the browser performs automatically
after an event.

Examples:

  Element                     Default Action
  --------------------------- ----------------------
  `<a>`                       Navigate to `href`
  `<form>`                    Submit form
  `<input type="checkbox">`   Toggle checked state

## Syntax

``` javascript
element.addEventListener("click", (event) => {
  event.preventDefault();
});
```

## Example: Prevent Link Navigation

``` html
<a id="docs" href="https://example.com">Docs</a>
```

``` javascript
document.getElementById("docs")
  .addEventListener("click", (event) => {
    event.preventDefault();
    console.log("Navigation cancelled.");
  });
```

Output:

``` text
Navigation cancelled.
```

The browser stays on the current page.

## Example: Prevent Form Submission

``` javascript
form.addEventListener("submit", (event) => {
  event.preventDefault();
  console.log("Validate first...");
});
```

This allows client-side validation before submitting.

## Browser Workflow

``` text
Event occurs
   ↓
Listener executes
   ↓
preventDefault() called?
   ↓
Yes
   ↓
Skip browser default action
```

## Key Takeaways

-   `preventDefault()` cancels the browser's default behavior.
-   It does not stop event propagation.
-   It is commonly used with forms and links.

## Continue

Next file:

**DOM-Events-Part-6-Section-2.md**
