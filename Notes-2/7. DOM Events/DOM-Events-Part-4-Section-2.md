---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 4 - Section 2
subtopic: Event Flow
title: DOM Events - Part 4 - Event Flow (Capturing, Target, and
  Bubbling)
topic: DOM Events
---

# DOM Events --- Part 4 (Section 2)

## `event.eventPhase`

The browser exposes the current propagation stage through
`event.eventPhase`.

Values:

    Value Phase
  ------- -----------
        1 Capturing
        2 Target
        3 Bubbling

``` javascript
button.addEventListener("click", (event) => {
  console.log(event.eventPhase);
});
```

## Listening During the Capturing Phase

By default, listeners run during bubbling.

To listen during capturing:

``` javascript
parent.addEventListener(
  "click",
  () => {
    console.log("Capturing");
  },
  { capture: true }
);
```

## Capturing vs Bubbling

### Capturing

``` text
window
 ↓
document
 ↓
html
 ↓
body
 ↓
target
```

### Bubbling

``` text
target
 ↑
body
 ↑
html
 ↑
document
 ↑
window
```

## Nested Example

``` html
<div id="outer">
  <div id="inner">
    <button id="btn">Click</button>
  </div>
</div>
```

``` javascript
outer.addEventListener("click", () => console.log("Outer"));
inner.addEventListener("click", () => console.log("Inner"));
btn.addEventListener("click", () => console.log("Button"));
```

Clicking the button:

``` text
Button
Inner
Outer
```

## Debugging Tips

-   Verify where the listener is attached.
-   Check whether `capture: true` is being used.
-   Log `event.eventPhase` and `event.currentTarget` when tracing
    propagation.

## Continue

Next file:

**DOM-Events-Part-4-Section-3.md**
