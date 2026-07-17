---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 8 - Section 3
subtopic: Custom Events
title: DOM Events - Part 8 - Custom Events
topic: DOM Events
---

# DOM Events --- Part 8 (Section 3)

## Advanced Communication Patterns

Custom events allow independent parts of an application to communicate
without directly calling each other's functions.

``` text
Shopping Cart
      │
dispatchEvent()
      │
      ▼
cartUpdated
 ├── Cart Badge
 ├── Order Summary
 ├── Analytics
 └── Notification
```

Each component listens only for the event it cares about.

## Component Example

``` javascript
document.addEventListener("themeChanged", (event) => {
  document.body.dataset.theme = event.detail.theme;
});

const event = new CustomEvent("themeChanged", {
  detail: { theme: "dark" }
});

document.dispatchEvent(event);
```

Output:

``` text
Theme changed to dark
```

## Performance

-   Dispatch custom events only when meaningful state changes occur.
-   Keep `detail` payloads reasonably small.
-   Avoid using custom events for tight loops or high-frequency updates.

## Debugging

``` javascript
document.addEventListener("cartUpdated", (event) => {
  console.log(event.type);
  console.log(event.detail);
});
```

Check:

-   Was the event dispatched?
-   Is the listener registered before dispatch?
-   Does `detail` contain the expected data?

## Common Mistakes

-   Dispatching before listeners are registered.
-   Using vague event names like `"update"`.
-   Putting unrelated data into `detail`.
-   Using custom events where a normal function call is simpler.

## Continue

Next file:

**DOM-Events-Part-8-Section-4.md**
