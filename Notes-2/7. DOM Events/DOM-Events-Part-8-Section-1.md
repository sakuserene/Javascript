---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 8 - Section 1
subtopic: Custom Events
title: DOM Events - Part 8 - Custom Events
topic: DOM Events
---

# DOM Events --- Part 8 (Section 1)

## Introduction

The browser provides many built-in events such as `click`, `submit`, and
`keydown`.

Sometimes your own application needs to communicate using events that
**you define yourself**.

These are called **Custom Events**.

## Why Custom Events?

Imagine a shopping cart.

When an item is added, many parts of the application may need to react:

-   Update the cart badge
-   Recalculate the total
-   Save the cart
-   Display a notification

Instead of tightly connecting all these components, one part of the
application can **dispatch a custom event** and any interested component
can listen for it.

## Creating a Custom Event

``` javascript
const cartUpdated = new CustomEvent("cartUpdated");
```

The string `"cartUpdated"` is the event type.

## Dispatching the Event

``` javascript
document.dispatchEvent(cartUpdated);
```

## Listening for the Event

``` javascript
document.addEventListener("cartUpdated", () => {
  console.log("Cart updated!");
});
```

Output:

``` text
Cart updated!
```

## Event Flow

``` text
Application Logic
      ↓
Create CustomEvent
      ↓
dispatchEvent()
      ↓
Browser dispatches event
      ↓
Matching listeners execute
```

## Key Takeaways

-   Custom events let your own code communicate using the browser's
    event system.
-   `CustomEvent` creates the event.
-   `dispatchEvent()` sends it.
-   Any listener registered for that event type can respond.

## Continue

Next file:

**DOM-Events-Part-8-Section-2.md**
