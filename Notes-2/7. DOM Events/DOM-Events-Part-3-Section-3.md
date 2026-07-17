---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 3 - Section 3
subtopic: The Event Object
title: DOM Events - Part 3 - The Event Object
topic: DOM Events
---

# DOM Events --- Part 3 (Section 3)

## Event Methods

The Event Object also provides methods that allow your code to influence
how the browser handles an event.

### `preventDefault()`

Prevents the browser's default action for an event.

Example:

``` html
<a id="docs" href="https://example.com">Documentation</a>
```

``` javascript
document.getElementById("docs").addEventListener("click", (event) => {
  event.preventDefault();
  console.log("Navigation prevented.");
});
```

Output:

``` text
Navigation prevented.
```

The browser does **not** follow the link because the default action was
cancelled.

------------------------------------------------------------------------

### `stopPropagation()`

Stops the event from continuing to ancestor elements.

``` javascript
child.addEventListener("click", (event) => {
  event.stopPropagation();
});
```

You'll study propagation in depth in the next lesson.

------------------------------------------------------------------------

### `stopImmediatePropagation()`

Stops propagation **and** prevents any remaining listeners on the same
element from executing.

------------------------------------------------------------------------

## Mouse Event Properties

Common properties include:

-   `clientX`
-   `clientY`
-   `button`
-   `buttons`

``` javascript
document.addEventListener("click", (event) => {
  console.log(event.clientX, event.clientY);
});
```

------------------------------------------------------------------------

## Keyboard Event Properties

Frequently used properties:

-   `key`
-   `code`
-   `altKey`
-   `ctrlKey`
-   `shiftKey`

``` javascript
document.addEventListener("keydown", (event) => {
  console.log(event.key);
});
```

------------------------------------------------------------------------

## Form Event Properties

Form events commonly use:

-   `target`
-   `currentTarget`
-   `type`

``` javascript
form.addEventListener("submit", (event) => {
  event.preventDefault();
  console.log(event.type);
});
```

------------------------------------------------------------------------

## Best Practices

-   Prevent default behaviour only when necessary.
-   Avoid stopping propagation without a clear reason.
-   Use the Event Object instead of global browser objects.

## Continue

Next file:

**DOM-Events-Part-3-Section-4.md**
