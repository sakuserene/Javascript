---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 3 - Section 1
subtopic: The Event Object
title: DOM Events - Part 3 - The Event Object
topic: DOM Events
---

# DOM Events --- Part 3 (Section 1)

## Introduction

When an event occurs, the browser doesn't simply tell JavaScript
**that** something happened.

It also provides information **about** what happened.

That information is packaged inside an **Event Object**.

## What is the Event Object?

An Event Object is a JavaScript object automatically created by the
browser for every event.

It contains details such as:

-   The event type
-   The element that triggered the event
-   The time the event occurred
-   Whether the event bubbles
-   Whether the default action can be prevented

## Why Does It Exist?

Without the Event Object, a listener would know only that an event
occurred.

With it, your code can answer questions like:

-   Which button was clicked?
-   Which key was pressed?
-   Which input changed?
-   Which element triggered the event?

## Receiving the Event Object

The browser passes it as the first argument to the listener.

``` javascript
button.addEventListener("click", function (event) {
  console.log(event);
});
```

You do not create or pass the object yourself.

## Browser Workflow

``` text
User clicks
    ↓
Browser creates Event Object
    ↓
Browser invokes listener
    ↓
Event Object passed as first parameter
    ↓
JavaScript reads event data
```

## First Common Properties

``` javascript
button.addEventListener("click", function (event) {
  console.log(event.type);
  console.log(event.target);
  console.log(event.timeStamp);
});
```

Typical output:

``` text
click
<button id="save">...</button>
24587.3
```

## Key Takeaways

-   Every event gets a fresh Event Object.
-   The browser creates it automatically.
-   It is passed into your callback as the first parameter.
-   The Event Object is the primary source of information about an
    event.

## Continue

Next file:

**DOM-Events-Part-3-Section-2.md**
