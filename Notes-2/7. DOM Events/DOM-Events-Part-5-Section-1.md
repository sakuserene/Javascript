---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 5 - Section 1
subtopic: Event Delegation
title: DOM Events - Part 5 - Event Delegation
topic: DOM Events
---

# DOM Events --- Part 5 (Section 1)

## Introduction

Imagine a list with 10,000 buttons.

Should you attach 10,000 event listeners?

**No.**

Event delegation lets one ancestor element handle events from many child
elements.

## What is Event Delegation?

Event delegation is a technique where you attach a listener to a parent
element and use **event bubbling** to respond to events from its
descendants.

## Why Use It?

Without delegation:

``` javascript
buttons.forEach(button => {
  button.addEventListener("click", handleClick);
});
```

With delegation:

``` javascript
list.addEventListener("click", handleClick);
```

Only one listener is needed.

## How It Works

``` text
User clicks <button>
        ↓
Button receives event
        ↓
Event bubbles to parent
        ↓
Parent listener executes
        ↓
Parent checks event.target
```

## Example

``` html
<ul id="menu">
  <li>Home</li>
  <li>Products</li>
  <li>Contact</li>
</ul>
```

``` javascript
const menu = document.getElementById("menu");

menu.addEventListener("click", (event) => {
  console.log(event.target.textContent);
});
```

Clicking **Products** prints:

``` text
Products
```

## Benefits

-   Fewer listeners
-   Better memory usage
-   Easier maintenance
-   Automatically works for dynamically added child elements

## Continue

Next file:

**DOM-Events-Part-5-Section-2.md**
