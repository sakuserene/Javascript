---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 4 - Section 1
subtopic: Event Flow
title: DOM Events - Part 4 - Event Flow (Capturing, Target, and
  Bubbling)
topic: DOM Events
---

# DOM Events --- Part 4 (Section 1)

## Introduction

When a browser dispatches an event, it does not simply call one
listener.

The event travels through the DOM following a well-defined process
called **event flow**.

Understanding this explains why parent and child listeners can both run
after a single click.

## Why Event Flow Exists

Consider this structure:

``` html
<body>
  <div id="card">
    <button id="buy">Buy</button>
  </div>
</body>
```

If the button is clicked:

-   Should the button listener run?
-   Should the card listener run?
-   Should the body listener run?

The browser answers these questions using **event propagation**.

## The Three Phases

``` text
Capturing
    ↓
Target
    ↓
Bubbling
```

### 1. Capturing Phase

The event travels from the top of the document down toward the target.

``` text
window
 ↓
document
 ↓
html
 ↓
body
 ↓
div
 ↓
button
```

### 2. Target Phase

The event reaches the element where it occurred.

``` text
button
```

### 3. Bubbling Phase

The event travels back up through ancestor elements.

``` text
button
 ↑
div
 ↑
body
 ↑
html
 ↑
document
 ↑
window
```

## Mental Model

Imagine dropping a stone into a well.

It travels **down** to the bottom (capturing), hits the bottom (target),
then the sound echoes **back up** (bubbling).

## Example

``` html
<div id="parent">
  <button id="child">Click</button>
</div>
```

``` javascript
parent.addEventListener("click", () => {
  console.log("Parent");
});

child.addEventListener("click", () => {
  console.log("Child");
});
```

Clicking the button normally prints:

``` text
Child
Parent
```

This happens because the bubbling phase reaches the parent after the
target listener executes.

## Key Takeaways

-   Events travel through the DOM.
-   Event flow has three phases.
-   Bubbling is the default behaviour used by most listeners.
-   Understanding propagation is essential before learning event
    delegation.

## Continue

Next file:

**DOM-Events-Part-4-Section-2.md**
