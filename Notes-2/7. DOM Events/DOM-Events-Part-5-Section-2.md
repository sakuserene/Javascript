---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 5 - Section 2
subtopic: Event Delegation
title: DOM Events - Part 5 - Event Delegation
topic: DOM Events
---

# DOM Events --- Part 5 (Section 2)

## `event.target` vs `closest()`

When using event delegation, the clicked element is available through
`event.target`.

However, users often click nested elements.

``` html
<button class="delete">
  <span>Delete</span>
</button>
```

If the user clicks `<span>`, then:

``` javascript
console.log(event.target.tagName);
```

Outputs:

``` text
SPAN
```

Sometimes you want the nearest button instead.

``` javascript
const button = event.target.closest(".delete");

if (!button) return;

console.log("Delete button clicked");
```

`closest()` walks up the DOM tree until it finds a matching ancestor.

## Delegation with Dynamic Elements

One major advantage of delegation is that newly-created child elements
work automatically.

``` javascript
list.addEventListener("click", (event) => {
  const item = event.target.closest("li");
  if (!item) return;

  console.log(item.textContent);
});
```

Even if `<li>` elements are added later using JavaScript, the parent
listener still works.

## Filtering Events

A delegated listener should ignore unrelated clicks.

``` javascript
container.addEventListener("click", (event) => {
  if (!event.target.matches(".save")) {
    return;
  }

  console.log("Save clicked");
});
```

## Production Pattern

``` javascript
document.addEventListener("click", (event) => {
  const action = event.target.closest("[data-action]");
  if (!action) return;

  console.log(action.dataset.action);
});
```

Using `data-*` attributes is a common production technique for routing
actions.

## Common Mistakes

-   Assuming `event.target` is always the desired element.
-   Forgetting to filter delegated events.
-   Attaching the delegated listener too high in the DOM unnecessarily.

## Continue

Next file:

**DOM-Events-Part-5-Section-3.md**
