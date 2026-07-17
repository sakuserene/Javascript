---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 6 - Section 3
subtopic: preventDefault()
title: DOM Events - Part 6 - Preventing Default Behavior
  (preventDefault)
topic: DOM Events
---

# DOM Events --- Part 6 (Section 3)

## Advanced Use Cases

### SPA Navigation

Single Page Applications often prevent link navigation and use
JavaScript routing instead.

``` javascript
document.querySelector("a").addEventListener("click", (event) => {
  event.preventDefault();
  console.log("Navigate with router");
});
```

### Prevent Multiple Form Submissions

``` javascript
form.addEventListener("submit", (event) => {
  event.preventDefault();
  submitButton.disabled = true;
});
```

## Performance

-   Only call `preventDefault()` when necessary.
-   Avoid unnecessary work inside high-frequency events.

## Accessibility

-   Don't block expected browser behaviors without providing an
    alternative.
-   Ensure keyboard users can still complete tasks.

## Security

-   `preventDefault()` does not validate data.
-   Always validate and sanitize user input on the server.

## Best Practices

-   Use with intent.
-   Check `event.cancelable` if unsure.
-   Keep handlers focused.

## Exercises

### Beginner

1.  Prevent a link from opening.
2.  Prevent a form submission.

### Intermediate

1.  Show a validation message before allowing submit.
2.  Prevent the context menu on a specific element.

### Advanced

1.  Build a confirmation dialog before deleting an item.
2.  Compare behavior with and without `preventDefault()`.

## Interview Question

**Q:** Does `preventDefault()` stop event bubbling?

**A:** No. It only cancels the browser's default action. Use
`stopPropagation()` to stop propagation.

## Continue

Next file:

**DOM-Events-Part-6-Section-4.md**
