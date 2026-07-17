---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 2 - Section 1
subtopic: Event Listeners (addEventListener)
title: DOM Events - Part 2 - Event Listeners (addEventListener)
topic: DOM Events
---

# DOM Events --- Part 2 (Section 1)

## Introduction

In Part 1 you learned that the browser creates events.

A natural question follows:

> **How does JavaScript know which code should run when an event
> occurs?**

The answer is **event listeners**.

An event listener is a function that you register with the browser. When
the specified event occurs, the browser calls that function.

## Before Event Listeners

Without event listeners, JavaScript has no instructions for responding
to a click.

``` html
<button>Save</button>
```

Clicking this button does nothing because no listener has been
registered.

## What is an Event Listener?

An event listener is a callback function associated with:

1.  A DOM element
2.  A specific event type

Example:

``` javascript
button.addEventListener("click", handleClick);
```

When a click occurs on `button`, the browser invokes `handleClick`.

## Why `addEventListener()` Was Introduced

Earlier approaches relied on HTML attributes:

``` html
<button onclick="saveData()">
  Save
</button>
```

Problems:

-   Mixes HTML and JavaScript
-   Hard to maintain
-   Only one handler per property
-   Difficult to reuse and test

Modern JavaScript separates structure and behaviour.

## Syntax

``` javascript
target.addEventListener(
  type,
  listener,
  options
);
```

### Parameters

-   **target** -- the object receiving events
-   **type** -- event name such as `"click"` or `"input"`
-   **listener** -- callback function to execute
-   **options** -- optional configuration

## Example

``` html
<button id="save">Save</button>
```

``` javascript
const button =
  document.getElementById("save");

button.addEventListener(
  "click",
  function () {
    console.log("Saved!");
  }
);
```

Output after clicking:

``` text
Saved!
```

## Internal Workflow

``` text
User clicks
      ↓
Browser detects click
      ↓
Browser checks registered listeners
      ↓
Matching click listener found
      ↓
Browser calls listener
      ↓
JavaScript executes callback
```

## Key Takeaways

-   Event listeners connect browser events to JavaScript.
-   `addEventListener()` is the preferred modern API.
-   A listener is simply a function executed when an event occurs.
-   The browser---not your code---decides when to invoke the listener.

## Continue

Next file:

**DOM-Events-Part-2-Section-2.md**
