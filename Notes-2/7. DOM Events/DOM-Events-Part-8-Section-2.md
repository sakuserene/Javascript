---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 8 - Section 2
subtopic: Custom Events
title: DOM Events - Part 8 - Custom Events
topic: DOM Events
---

# DOM Events --- Part 8 (Section 2)

## Passing Data with `detail`

A custom event becomes much more useful when it carries data.

``` javascript
const cartUpdated = new CustomEvent("cartUpdated", {
  detail: {
    item: "Keyboard",
    quantity: 2,
    total: 150
  }
});
```

The `detail` property can contain any JavaScript value.

## Receiving the Data

``` javascript
document.addEventListener("cartUpdated", (event) => {
  console.log(event.detail.item);
  console.log(event.detail.quantity);
  console.log(event.detail.total);
});
```

Output:

``` text
Keyboard
2
150
```

## Event Options

### `bubbles`

``` javascript
new CustomEvent("cartUpdated", {
  bubbles: true
});
```

Allows the custom event to participate in event bubbling.

### `cancelable`

``` javascript
new CustomEvent("cartUpdated", {
  cancelable: true
});
```

Allows listeners to call `preventDefault()`.

### `composed`

``` javascript
new CustomEvent("cartUpdated", {
  composed: true
});
```

Allows the event to cross Shadow DOM boundaries when appropriate.

## Real-World Example

``` javascript
const loginSuccess = new CustomEvent("loginSuccess", {
  detail: {
    username: "Alice"
  }
});

document.dispatchEvent(loginSuccess);

document.addEventListener("loginSuccess", (event) => {
  console.log(`Welcome ${event.detail.username}`);
});
```

## Best Practices

-   Choose descriptive event names.
-   Keep `detail` focused on relevant data.
-   Document the expected payload shape for team projects.
-   Prefer custom events for communication between loosely coupled
    components.

## Continue

Next file:

**DOM-Events-Part-8-Section-3.md**
