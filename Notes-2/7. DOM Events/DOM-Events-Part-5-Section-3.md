---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 5 - Section 3
subtopic: Event Delegation
title: DOM Events - Part 5 - Event Delegation
topic: DOM Events
---

# DOM Events --- Part 5 (Section 3)

## Performance Comparison

### Individual Listeners

``` javascript
items.forEach(item => {
  item.addEventListener("click", handleClick);
});
```

-   One listener per item
-   More memory usage
-   New items need new listeners

### Delegated Listener

``` javascript
list.addEventListener("click", handleClick);
```

-   One listener
-   Lower memory usage
-   Automatically supports new child elements

## Real-World CRUD Example

``` html
<ul id="tasks">
  <li><button class="delete">Delete</button></li>
</ul>
```

``` javascript
tasks.addEventListener("click", (event) => {
  const button = event.target.closest(".delete");
  if (!button) return;

  button.closest("li").remove();
});
```

This pattern scales well even if thousands of tasks are added later.

## Debugging Delegated Events

``` javascript
container.addEventListener("click", (event) => {
  console.log(event.target);
  console.log(event.currentTarget);
});
```

Check:

-   Was the click inside the delegated container?
-   Did `closest()` return the expected element?
-   Is the selector correct?

## Best Practices

-   Attach the delegated listener to the closest stable ancestor.
-   Filter events early using `matches()` or `closest()`.
-   Keep delegated handlers focused on one responsibility.
-   Avoid delegating from `document` unless necessary.

## Continue

Next file:

**DOM-Events-Part-5-Section-4.md**
