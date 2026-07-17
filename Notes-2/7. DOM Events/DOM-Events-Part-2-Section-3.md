---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 2 - Section 3
subtopic: Event Listeners (addEventListener)
title: DOM Events - Part 2 - Event Listeners (addEventListener)
topic: DOM Events
---

# DOM Events --- Part 2 (Section 3)

## Removing Event Listeners

Sometimes you no longer want an event listener to respond.

JavaScript provides:

``` javascript
target.removeEventListener(type, listener);
```

### Correct Example

``` javascript
function handleClick() {
  console.log("Clicked");
}

button.addEventListener("click", handleClick);

// Later
button.removeEventListener("click", handleClick);
```

After removal, clicking the button no longer runs `handleClick`.

## Why the Same Function Reference Matters

This **does not work**:

``` javascript
button.addEventListener("click", function () {
  console.log("Clicked");
});

button.removeEventListener("click", function () {
  console.log("Clicked");
});
```

Although the code looks identical, these are two different function
objects.

Always keep a reference to reusable listeners.

## Event Listener Options

### once

``` javascript
button.addEventListener("click", handleClick, {
  once: true
});
```

The browser automatically removes the listener after the first click.

### capture

``` javascript
button.addEventListener("click", handleClick, {
  capture: true
});
```

This changes **when** the listener runs during event propagation.
Propagation is covered in the next lessons.

### passive

``` javascript
window.addEventListener("scroll", handleScroll, {
  passive: true
});
```

Passive listeners tell the browser that the listener will not call
`preventDefault()`, allowing smoother scrolling.

## Browser Workflow

``` text
Event occurs
      ↓
Browser finds registered listeners
      ↓
Checks listener options
      ↓
Invokes matching callbacks
      ↓
Removes listener automatically if once=true
```

## Performance Notes

-   Reuse named handlers where appropriate.
-   Avoid repeatedly registering the same listener.
-   Prefer passive listeners for high-frequency events like scrolling
    when suitable.

## Debugging Tips

-   Ensure the same function reference is used with
    `removeEventListener()`.
-   Check that the event type matches exactly.
-   Verify the listener is attached to the expected element.

## Continue

Next file:

**DOM-Events-Part-2-Section-4.md**
