---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 2 - Section 2
subtopic: Event Listeners (addEventListener)
title: DOM Events - Part 2 - Event Listeners (addEventListener)
topic: DOM Events
---

# DOM Events --- Part 2 (Section 2)

## Callback Functions

The second argument to `addEventListener()` is a **callback function**.

A callback is a function that is **passed to another function so it can
be executed later**.

``` javascript
function handleClick() {
  console.log("Button clicked!");
}

button.addEventListener("click", handleClick);
```

The browser stores a reference to `handleClick` and calls it whenever a
matching event occurs.

## Anonymous vs Named Functions

### Anonymous

``` javascript
button.addEventListener("click", function () {
  console.log("Clicked");
});
```

Advantages:

-   Short
-   Good for simple logic

Disadvantages:

-   Harder to remove later
-   Harder to reuse

### Named

``` javascript
function handleClick() {
  console.log("Clicked");
}

button.addEventListener("click", handleClick);
```

Advantages:

-   Reusable
-   Easier to debug
-   Required when using `removeEventListener()`

## Multiple Listeners

You can register more than one listener for the same event.

``` javascript
button.addEventListener("click", () => {
  console.log("Saving...");
});

button.addEventListener("click", () => {
  console.log("Analytics updated.");
});
```

Output:

``` text
Saving...
Analytics updated.
```

## Different Event Types

One element can listen for different events.

``` javascript
input.addEventListener("focus", () => {
  console.log("Focused");
});

input.addEventListener("blur", () => {
  console.log("Lost focus");
});
```

## Execution Flow

``` text
User Action
    ↓
Browser detects event
    ↓
Find listeners for that event type
    ↓
Execute callbacks in registration order
```

## Common Mistakes

-   Writing `handleClick()` instead of `handleClick` when registering.
-   Registering listeners inside loops unnecessarily.
-   Using anonymous functions when they must later be removed.

## Best Practices

-   Prefer named handlers for reusable logic.
-   Keep listeners small and focused.
-   Move business logic into separate functions.

## Continue

Next file:

**DOM-Events-Part-2-Section-3.md**
