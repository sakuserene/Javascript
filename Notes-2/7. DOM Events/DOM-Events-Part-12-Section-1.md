---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 12 - Section 1
subtopic: Clipboard and Specialized Events
title: DOM Events - Part 12 - Clipboard, Drag & Drop, and Other
  Specialized Events
topic: DOM Events
---

# DOM Events --- Part 12 (Section 1)

## Introduction

Not all browser events come from clicking buttons or typing into forms.

Modern browsers also generate events for:

-   Clipboard operations
-   Drag and drop
-   Scrolling
-   Resizing
-   Page visibility changes
-   Window lifecycle events

These specialized events enable richer web applications.

## Clipboard Events

The browser fires clipboard events whenever the user interacts with
copied data.

### `copy`

``` javascript
document.addEventListener("copy", () => {
  console.log("Content copied");
});
```

### `cut`

``` javascript
document.addEventListener("cut", () => {
  console.log("Content cut");
});
```

### `paste`

``` javascript
document.addEventListener("paste", (event) => {
  console.log(event.clipboardData.getData("text"));
});
```

### Example Output

``` text
Hello World
```

## Event Flow

``` text
User presses Ctrl+C
        ↓
Browser copies selection
        ↓
copy event
        ↓
JavaScript listener executes
```

## Key Takeaways

-   Clipboard events respond to copy, cut, and paste actions.
-   `clipboardData` provides access to clipboard content during
    supported events.
-   These events are useful for validation, formatting, and productivity
    features.

## Continue

Next file:

**DOM-Events-Part-12-Section-2.md**
