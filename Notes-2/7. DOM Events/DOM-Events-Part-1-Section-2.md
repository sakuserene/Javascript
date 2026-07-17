---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 1 - Section 2
subtopic: Introduction to the Browser Event System
title: DOM Events - Part 1 - Introduction to the Browser Event System
topic: DOM Events
---

# DOM Events --- Part 1 (Section 2)

## Event-Driven Programming

An **event-driven program** waits for something meaningful to happen
instead of continuously checking for changes.

Traditional program:

``` text
Start
 ↓
Do Task A
 ↓
Do Task B
 ↓
Finish
```

Browser application:

``` text
Start
 ↓
Wait for events
 ↓
User clicks
 ↓
React
 ↓
Wait again
```

## Polling vs Events

### Polling

``` javascript
while (true) {
  checkMouse();
}
```

Problems:

-   High CPU usage
-   Wasted battery
-   Poor performance
-   Difficult to scale

### Event-based approach

``` text
Wait
 ↓
Event occurs
 ↓
React
```

The browser performs the monitoring for you.

## Browser Event Sources

Events can originate from many places:

-   Mouse (`click`, `mousemove`)
-   Keyboard (`keydown`, `keyup`)
-   Forms (`submit`, `input`, `change`)
-   Window (`resize`, `scroll`)
-   Clipboard (`copy`, `paste`)
-   Media (`play`, `pause`)
-   Touch and pointer devices

## Mental Model

Think of the browser as a receptionist.

``` text
Visitor
   ↓
Receptionist (Browser)
   ↓
Developer (JavaScript)
```

The receptionist decides when to notify you. You don't constantly ask
whether someone has arrived.

## High-Level Event Pipeline

``` text
User
 ↓
Operating System
 ↓
Browser
 ↓
Event Object
 ↓
Event Queue
 ↓
JavaScript
 ↓
DOM Update
 ↓
Browser Render
```

## Key Takeaways

-   Browsers detect events.
-   JavaScript reacts to browser notifications.
-   Event-driven programming is more efficient than polling.
-   Most modern web applications spend most of their lifetime waiting
    for events.

## Continue

Next file:

**DOM-Events-Part-1-Section-3.md**
