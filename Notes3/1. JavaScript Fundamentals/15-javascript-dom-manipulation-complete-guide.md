# Lesson 15: JavaScript DOM Manipulation - Complete Guide

**Level:** Beginner to Advanced  
**Prerequisite:** JavaScript objects, arrays, loops, methods, and JSON  
**Previous:** [Lesson 14 - JSON](14-javascript-json-complete-guide.md)  
**Next:** [Lesson 16 - JavaScript Events](16-javascript-events.md)  
**Postponed:** Object-Oriented Programming (return later)

## Table of Contents

1. [Learning goals and setup](#1-learning-goals-and-setup)
2. [What the DOM is](#2-what-the-dom-is)
3. [The document object and selecting elements](#3-the-document-object-and-selecting-elements)
4. [CSS selectors and null checks](#4-css-selectors-and-null-checks)
5. [NodeList, HTMLCollection, and arrays](#5-nodelist-htmlcollection-and-arrays)
6. [Looping over selected elements](#6-looping-over-selected-elements)
7. [textContent, innerText, and innerHTML](#7-textcontent-innertext-and-innerhtml)
8. [Changing styles and CSS classes](#8-changing-styles-and-css-classes)
9. [Attributes, properties, and data attributes](#9-attributes-properties-and-data-attributes)
10. [Creating and inserting elements](#10-creating-and-inserting-elements)
11. [Removing and replacing elements](#11-removing-and-replacing-elements)
12. [DOM traversal](#12-dom-traversal)
13. [Timing and DOMContentLoaded](#13-timing-and-domcontentloaded)
14. [Event handling preview](#14-event-handling-preview)
15. [Rendering collections with loops vs methods](#15-rendering-collections-with-loops-vs-methods)
16. [Performance and security](#16-performance-and-security)
17. [Practical project - product list](#17-practical-project---product-list)
18. [Common mistakes](#18-common-mistakes)
19. [Ten interview questions with answers](#19-ten-interview-questions-with-answers)
20. [Ten coding exercises with complete solutions](#20-ten-coding-exercises-with-complete-solutions)
21. [Mastery checklist and navigation](#21-mastery-checklist-and-navigation)

---

## 1. Learning goals and setup

You will learn to select, inspect, update, create, insert, and remove HTML elements. You will implement collection operations using both **plain loops** (`for`, `for...of`) and **methods** (`forEach`, `map`, `filter`). We will explain expressions, variables, callbacks, indexes, and execution traces.

Create two files in the same folder:

**index.html**

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>DOM Practice</title>
  <script src="script.js" defer></script>
</head>
<body>
  <h1 id="title">Hello World</h1>
  <p class="message">First message</p>
  <p class="message">Second message</p>
  <div id="output"></div>
</body>
</html>
```

**script.js**

```javascript
const heading = document.getElementById("title");
console.log(heading.textContent);
```

Open `index.html` in a browser and open Developer Tools > Console. The `defer` attribute tells the browser to execute the external classic script after parsing the HTML. JavaScript DOM APIs generally require a browser or DOM environment; `document` is not automatically available in Node.js.

## 2. What the DOM is

The **Document Object Model** is the browser's object-based representation of a document. HTML is source markup; the DOM is a tree of nodes created by parsing the markup. JavaScript can update that tree while the page remains open.

```text
document
`-- html
    |-- head
    |   `-- title
    `-- body
        |-- h1#title
        |   `-- text: Hello World
        |-- p.message
        |-- p.message
        `-- div#output
```

An **element node** represents an HTML tag; a **text node** contains text; the **document node** is the entry point. `children` contains element children; `childNodes` can also include whitespace text nodes. The DOM is not the same as JavaScript itself: DOM APIs are provided by the environment.

## 3. The document object and selecting elements

```javascript
const title = document.getElementById("title");
const firstMessage = document.querySelector(".message");
const allMessages = document.querySelectorAll(".message");
```

**Line-by-line:** `const` creates a binding; `title` is the variable name; `=` assigns the returned value. `document` is the current document; `.` accesses a method; `getElementById` searches by ID; `("title")` calls it with a string argument. `getElementById("title")` does **not** take a `#`. `querySelector` takes a CSS selector and returns the first match or `null`. `querySelectorAll` returns a **static NodeList**, possibly empty.

```javascript
console.log(title.tagName);          // "H1"
console.log(firstMessage.textContent); // "First message"
console.log(allMessages.length);     // 2
```

If no element is found, `getElementById` and `querySelector` return `null`. Calling `.textContent` on `null` throws a TypeError.

## 4. CSS selectors and null checks

| Selector | Matches |
| --- | --- |
| `#title` | Element with ID `title` |
| `.message` | Elements with class `message` |
| `p` | Paragraph elements |
| `div p` | Paragraph descendants of a div |
| `ul > li` | Direct li children of ul |
| `[data-price]` | Elements with a data-price attribute |
| `input[type="text"]` | Text inputs |

```javascript
const target = document.querySelector("#missing");
if (target !== null) {
  target.textContent = "Found";
} else {
  console.log("No matching element");
}
```

`!==` is strict inequality. `null` means no matching element. The `if` condition prevents accessing a property on a nonexistent element. Optional chaining is another option: `document.querySelector("#missing")?.classList.add("active")` safely does nothing if the selection is `null`.

## 5. NodeList, HTMLCollection, and arrays

```javascript
const staticItems = document.querySelectorAll(".message");
const liveItems = document.getElementsByClassName("message");
const ordinaryArray = Array.from(staticItems);
```

- `querySelectorAll()` returns a **static NodeList**: it does not automatically add new matches later.
- `getElementsByClassName()` returns a **live HTMLCollection**: it updates as matching elements are added or removed.
- Neither is an actual Array. NodeList supports `forEach()` and `for...of` in modern browsers. HTMLCollection generally does not have `forEach()`.
- `Array.from(...)` creates an array snapshot that supports `map`, `filter`, `reduce`, and other array methods.

**Example of live vs static:**

```javascript
const live = document.getElementsByClassName("message");
const fixed = document.querySelectorAll(".message");
const p = document.createElement("p");
p.className = "message";
p.textContent = "Third message";
document.body.append(p);
console.log(live.length);  // 3
console.log(fixed.length); // 2
```

## 6. Looping over selected elements

**Plain indexed loop:**

```javascript
const messages = document.querySelectorAll(".message");
for (let i = 0; i < messages.length; i++) {
  console.log(messages[i].textContent);
}
```

`let i = 0` initializes the index. `i < messages.length` is checked before each iteration. `i++` increments after each iteration. `messages[i]` selects the element at the current zero-based index. `.` accesses its text.

**for...of:**

```javascript
for (const message of messages) {
  console.log(message.textContent);
}
```

**NodeList.forEach:**

```javascript
messages.forEach((message, index) => {
  console.log(index, message.textContent);
});
```

`(message, index) => { ... }` is an arrow-function callback. `forEach` supplies the current element and its index. It does not produce a transformed array and cannot be exited with `break`.

| Iteration | `i` or `index` | Current text |
| --- | ---: | --- |
| 1 | 0 | First message |
| 2 | 1 | Second message |

**When to choose:** indexed `for` when you need index control or `break`; `for...of` for straightforward element processing; `forEach` for concise callback-based processing. A NodeList is not directly compatible with `.map()`; use `Array.from(messages).map(...)`.

## 7. textContent, innerText, and innerHTML

```html
<div id="box">Hello <strong>World</strong></div>
```

```javascript
const box = document.getElementById("box");
console.log(box.textContent); // Text of descendants, including "Hello World"
console.log(box.innerText);   // Rendered text, affected by layout/visibility
console.log(box.innerHTML);  // 'Hello <strong>World</strong>'
box.textContent = "Safe plain text";
```

`textContent` treats the assigned value as text, not HTML. `innerHTML` parses markup and creates elements. Do **not** insert untrusted text using `innerHTML`: it can enable cross-site scripting. Use `textContent` for user-provided strings. `innerText` is affected by CSS/rendering and may trigger layout work.

## 8. Changing styles and CSS classes

```javascript
const heading = document.getElementById("title");
heading.style.color = "blue";
heading.style.backgroundColor = "lightyellow";
heading.style.fontSize = "24px";
heading.classList.add("highlight");
heading.classList.remove("highlight");
heading.classList.toggle("highlight");
console.log(heading.classList.contains("highlight"));
```

CSS `background-color` becomes the camelCase property `backgroundColor`; numeric CSS lengths generally need units such as `"24px"`. `classList.add` adds a class; `remove` removes it; `toggle` switches its presence; `contains` returns a boolean. Prefer CSS classes for reusable presentation rules.

```css
.highlight { color: white; background: navy; padding: 8px; }
```

## 9. Attributes, properties, and data attributes

```html
<img id="photo" src="photo.jpg" alt="A landscape">
<li data-price="1500">Mouse</li>
```

```javascript
const photo = document.getElementById("photo");
console.log(photo.getAttribute("src")); // "photo.jpg"
photo.setAttribute("alt", "Mountain landscape");
console.log(photo.hasAttribute("alt")); // true
const item = document.querySelector("[data-price]");
const price = Number(item.dataset.price); // 1500
```

`dataset.price` is a string (`"1500"`), so `Number(...)` converts it to a number. Validate with `Number.isFinite(price)` before arithmetic. An element property such as `photo.src` may resolve to an absolute URL, while `getAttribute("src")` returns the literal attribute value. `removeAttribute("alt")` removes the attribute, though images should generally retain meaningful alternative text.

## 10. Creating and inserting elements

```javascript
const output = document.getElementById("output");
const paragraph = document.createElement("p");
paragraph.textContent = "Created with JavaScript";
paragraph.classList.add("message");
output.append(paragraph);
```

**Trace:** (1) `output` references an existing DOM element. (2) `createElement("p")` creates a detached paragraph. (3) `textContent` sets its text. (4) `classList.add` gives it a class. (5) `append` inserts it into the document. `appendChild(paragraph)` also appends a node and returns that node. `prepend` inserts at the beginning; `before` and `after` insert relative to an element. Re-inserting the same node **moves** it; it does not duplicate it.

## 11. Removing and replacing elements

```javascript
const oldElement = document.querySelector(".message");
if (oldElement) oldElement.remove();

const replacement = document.createElement("h2");
replacement.textContent = "Replacement heading";
const title = document.getElementById("title");
if (title) title.replaceWith(replacement);
```

`remove()` detaches an element; `replaceWith(...)` replaces it. Parent-based `parent.removeChild(child)` is another removal API. After replacement, the old `title` variable still references the detached old element; it does not automatically refer to the new `h2`.

## 12. DOM traversal

```javascript
const output = document.getElementById("output");
console.log(output.parentElement);
console.log(output.children);
console.log(output.firstElementChild);
console.log(output.lastElementChild);
console.log(output.nextElementSibling);
console.log(output.previousElementSibling);
```

`parentElement` returns an element parent or `null`; `children` is an HTMLCollection of element children; `firstElementChild` and `lastElementChild` return element children or `null`; sibling properties move to adjacent element siblings. `childNodes` also includes text and comment nodes. Use `closest(".card")` to find the nearest matching ancestor (including the element itself).

## 13. Timing and DOMContentLoaded

A script in the document head may run before body elements have been parsed. Common solutions:

```html
<script src="script.js" defer></script>
```

or:

```javascript
document.addEventListener("DOMContentLoaded", () => {
  const heading = document.getElementById("title");
  console.log(heading.textContent);
});
```

`defer` applies to external classic scripts and runs them after HTML parsing, before `DOMContentLoaded`. Module scripts are deferred by default. If your code executes after `DOMContentLoaded` has already fired, adding that listener at that point will not run it retroactively.

## 14. Event handling preview

```html
<button id="changeBtn">Change title</button>
```

```javascript
const button = document.getElementById("changeBtn");
const heading = document.getElementById("title");
button.addEventListener("click", () => {
  heading.textContent = "Title changed!";
});
```

`addEventListener("click", callback)` registers a callback to run later when a click occurs. Registration does not immediately execute the callback. We will cover event objects, bubbling, capturing, delegation, forms, and keyboard events in Lesson 16.

## 15. Rendering collections with loops vs methods

```html
<ul id="products"></ul>
```

```javascript
const names = ["Laptop", "Mouse", "Keyboard"];
const list = document.getElementById("products");
```

**A. Plain for loop:**

```javascript
for (let i = 0; i < names.length; i++) {
  const li = document.createElement("li");
  li.textContent = names[i];
  list.append(li);
}
```

**B. forEach method (run on a cleared list):**

```javascript
list.replaceChildren();
names.forEach((name) => {
  const li = document.createElement("li");
  li.textContent = name;
  list.append(li);
});
```

Both create three `li` children. `replaceChildren()` clears the existing children first, avoiding duplicate rendering. `forEach` expresses the operation concisely; a plain loop makes index control explicit. Both take O(n) iterations for n names.

**Filtering before rendering:**

```javascript
const products = [
  { name: "Laptop", price: 50000 },
  { name: "Mouse", price: 1500 },
  { name: "Keyboard", price: 3500 }
];
```

**Plain loop:**

```javascript
const cheapNames = [];
for (const product of products) {
  if (product.price < 5000) cheapNames.push(product.name);
}
```

**Methods:**

```javascript
const cheapNames = products
  .filter(product => product.price < 5000)
  .map(product => product.name);
```

Both yield `["Mouse", "Keyboard"]`. `.filter()` creates a subset; `.map()` transforms that subset into names. The chained method version creates intermediate arrays; the loop can do the selection and transformation in one pass.

## 16. Performance and security

- DOM operations can be more expensive than ordinary JavaScript calculations because they may affect style and layout.
- Prefer updating only what needs to change. For many insertions, a `DocumentFragment` can batch node construction before attachment.
- Avoid repeatedly reading layout-dependent properties between style writes when possible.
- Prefer `textContent` for user input. Never treat untrusted input as trusted HTML.
- Use `addEventListener` rather than embedding JavaScript in HTML attributes.
- A static NodeList is convenient when you need a stable snapshot; a live collection can change during iteration.
- A plain loop and a method may have the same O(n) time complexity but different allocation and control-flow characteristics.

## 17. Practical project - product list

Create these three files. The project supports adding products, removing products, and counting products. It renders with a **plain loop** by default; switch to the method implementation by replacing `renderPlain()` with `renderMethods()` in the event callbacks.

**index.html**

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Product Manager</title>
  <link rel="stylesheet" href="style.css">
  <script src="script.js" defer></script>
</head>
<body>
  <h1>Product Manager</h1>
  <form id="productForm">
    <label for="productName">Product name</label>
    <input id="productName" required maxlength="80">
    <button type="submit">Add</button>
  </form>
  <p id="count" aria-live="polite"></p>
  <ul id="productList"></ul>
</body>
</html>
```

**style.css**

```css
body { font-family: system-ui, sans-serif; max-width: 40rem; margin: 2rem auto; padding: 1rem; }
li { display: flex; justify-content: space-between; padding: .5rem; border-bottom: 1px solid #ccc; }
button { cursor: pointer; }
```

**script.js**

```javascript
const form = document.getElementById("productForm");
const input = document.getElementById("productName");
const list = document.getElementById("productList");
const count = document.getElementById("count");
let products = ["Laptop", "Mouse"];

function createRow(name, index) {
  const li = document.createElement("li");
  const label = document.createElement("span");
  label.textContent = name;
  const remove = document.createElement("button");
  remove.type = "button";
  remove.textContent = "Remove";
  remove.addEventListener("click", () => {
    products.splice(index, 1);
    renderPlain();
  });
  li.append(label, remove);
  return li;
}

function renderPlain() {
  list.replaceChildren();
  for (let i = 0; i < products.length; i++) {
    list.append(createRow(products[i], i));
  }
  count.textContent = `Total products: ${products.length}`;
}

function renderMethods() {
  list.replaceChildren();
  products.forEach((name, index) => {
    list.append(createRow(name, index));
  });
  count.textContent = `Total products: ${products.length}`;
}

form.addEventListener("submit", (event) => {
  event.preventDefault();
  const name = input.value.trim();
  if (name === "") return;
  products.push(name);
  input.value = "";
  renderPlain();
});

renderPlain();
```

**Execution trace:** Initially `products.length` is 2. `renderPlain()` clears the list and creates two rows. Submitting `Keyboard` calls `preventDefault()` (stops navigation), reads and trims the input, pushes the name, clears the input, and re-renders three rows. Clicking the second row's Remove button calls `splice(1, 1)`, leaving two products, then re-renders. `createRow` uses `textContent`, so product names are inserted as text rather than executable HTML.

**Important implementation detail:** `renderMethods()` currently uses the same `createRow` function, whose remove callback calls `renderPlain()`. Both produce the same UI, but if you want the method renderer to remain selected after a removal, change that callback to call `renderMethods()` or pass the render function as a parameter. This is a useful example of separating data manipulation from rendering strategy.

## 18. Common mistakes

1. **Wrong ID syntax:** `getElementById("#title")` is wrong; use `getElementById("title")` or `querySelector("#title")`.
2. **Ignoring null:** `querySelector(".missing").textContent` throws; check the result.
3. **Confusing one element with a collection:** `querySelectorAll()` returns a NodeList, not a single element.
4. **Calling array methods directly on NodeList:** `nodeList.map(...)` fails; use `Array.from(nodeList).map(...)`.
5. **Assuming HTMLCollection is static:** It may update while you iterate, potentially skipping elements if you remove them.
6. **Using `innerHTML` with untrusted text:** Prefer `textContent` to avoid XSS.
7. **Creating without inserting:** `createElement()` alone does not display anything.
8. **Appending the same node repeatedly:** It moves the node; create or clone a new node to get duplicates.
9. **Running code too early:** Use `defer` or wait until DOM is ready.
10. **Using `forEach` when early exit is needed:** Use `for...of` plus `break` instead.

## 19. Ten interview questions with answers

**Q1. What is the DOM?**  
**Answer:** The Document Object Model is a tree of nodes representing a document. Browser DOM APIs let JavaScript inspect and modify nodes and their relationships.

**Q2. `querySelector` vs `querySelectorAll`?**  
**Answer:** `querySelector` returns the first match or `null`; `querySelectorAll` returns a static NodeList of all matches. Example: `document.querySelector("p")` vs `document.querySelectorAll("p")`.

**Q3. What does `getElementById` return if no match exists?**  
**Answer:** `null`. Check before accessing properties.

**Q4. `textContent` vs `innerHTML`?**  
**Answer:** `textContent` treats values as text; `innerHTML` parses HTML markup. `textContent` is safer for untrusted input.

**Q5. What is `classList.toggle`?**  
**Answer:** It adds a class if absent and removes it if present; an optional force argument can explicitly add or remove it.

**Q6. Does `createElement` immediately display an element?**  
**Answer:** No. It creates a detached element. Insert it with `append`, `prepend`, or another insertion method.

**Q7. What is the difference between `append` and `prepend`?**  
**Answer:** `append` inserts at the end of the parent's children; `prepend` inserts at the beginning.

**Q8. What is a live HTMLCollection?**  
**Answer:** A collection that updates automatically as matching DOM elements are added or removed. For example, `getElementsByClassName()` returns one.

**Q9. Why might DOM selection return null?**  
**Answer:** The selector may be wrong, the element may not exist, or the code may run before HTML parsing reaches the element.

**Q10. What is DOM traversal?**  
**Answer:** Navigating between related nodes using properties like `parentElement`, `children`, and `nextElementSibling`, or methods such as `closest`.

## 20. Ten coding exercises with complete solutions

Each exercise has an objective, a standalone HTML fragment, JavaScript solution, and an explanation. Put the HTML fragment inside `<body>` of the starter `index.html`; put the JavaScript in `script.js` loaded with `defer`. For exercises with two approaches, execute them sequentially as described or choose one at a time to avoid duplicate output.

### Exercise 1: Change a heading

**Task:** Change a heading. Study the solution, then rewrite it without looking.

**HTML**

```html
<h1 id="title">Original</h1>
```

**JavaScript model solution**

```javascript
const title = document.getElementById("title");
if (title) title.textContent = "Updated heading";
```

**Step-by-step explanation and expected behavior:** The ID lookup returns an element or null. The guard prevents a TypeError. Assignment updates the existing text node representation; no HTML string parsing is needed.

**Practice extension:** Modify the input data, predict what changes, and test the result in the browser console. Check that missing elements are handled where appropriate and that user-provided text is inserted using `textContent`.

### Exercise 2: Print five paragraphs using for and forEach

**Task:** Print five paragraphs using for and forEach. Study the solution, then rewrite it without looking.

**HTML**

```html
<p class="line">One</p><p class="line">Two</p><p class="line">Three</p><p class="line">Four</p><p class="line">Five</p>
```

**JavaScript model solution**

```javascript
const lines = document.querySelectorAll(".line");
// A: plain indexed loop
for (let i = 0; i < lines.length; i++) console.log(lines[i].textContent);
// B: NodeList method
lines.forEach(line => console.log(line.textContent));
```

**Step-by-step explanation and expected behavior:** `i` visits indexes 0 through 4; each `lines[i]` is a paragraph. `forEach` supplies the current paragraph to its callback. Both print One, Two, Three, Four, Five in order.

**Practice extension:** Modify the input data, predict what changes, and test the result in the browser console. Check that missing elements are handled where appropriate and that user-provided text is inserted using `textContent`.

### Exercise 3: Change a paragraph style

**Task:** Change a paragraph style. Study the solution, then rewrite it without looking.

**HTML**

```html
<p id="notice">Notice</p>
```

**JavaScript model solution**

```javascript
const notice = document.getElementById("notice");
notice.style.color = "navy";
notice.style.backgroundColor = "lightyellow";
notice.style.fontSize = "20px";
```

**Step-by-step explanation and expected behavior:** Each `.style` assignment changes an inline CSS declaration. `backgroundColor` is camelCase; `20px` includes a unit.

**Practice extension:** Modify the input data, predict what changes, and test the result in the browser console. Check that missing elements are handled where appropriate and that user-provided text is inserted using `textContent`.

### Exercise 4: Toggle a class with a button

**Task:** Toggle a class with a button. Study the solution, then rewrite it without looking.

**HTML**

```html
<style>.active { background: yellow; }</style><p id="notice">Notice</p><button id="toggle">Toggle</button>
```

**JavaScript model solution**

```javascript
const notice = document.getElementById("notice");
const button = document.getElementById("toggle");
button.addEventListener("click", () => notice.classList.toggle("active"));
```

**Step-by-step explanation and expected behavior:** Registration happens once. Each click runs the callback: first click adds `active`, second removes it. `toggle` changes the class without rewriting the inline style.

**Practice extension:** Modify the input data, predict what changes, and test the result in the browser console. Check that missing elements are handled where appropriate and that user-provided text is inserted using `textContent`.

### Exercise 5: Create five list items with loop and method

**Task:** Create five list items with loop and method. Study the solution, then rewrite it without looking.

**HTML**

```html
<ul id="list"></ul>
```

**JavaScript model solution**

```javascript
const names = ["A", "B", "C", "D", "E"];
const list = document.getElementById("list");
function addItem(name) {
  const li = document.createElement("li");
  li.textContent = name;
  list.append(li);
}
// A: plain loop
for (let i = 0; i < names.length; i++) addItem(names[i]);
// B: method (clear first to avoid duplicates)
list.replaceChildren();
names.forEach(name => addItem(name));
```

**Step-by-step explanation and expected behavior:** The plain loop indexes each name; the method passes each name to the callback. `replaceChildren()` clears the previous result so the page ends with five list items, not ten.

**Practice extension:** Modify the input data, predict what changes, and test the result in the browser console. Check that missing elements are handled where appropriate and that user-provided text is inserted using `textContent`.

### Exercise 6: Remove products with buttons

**Task:** Remove products with buttons. Study the solution, then rewrite it without looking.

**HTML**

```html
<ul id="list"></ul>
```

**JavaScript model solution**

```javascript
let products = ["Laptop", "Mouse", "Keyboard"];
const list = document.getElementById("list");
function render() {
  list.replaceChildren();
  products.forEach((name, index) => {
    const li = document.createElement("li");
    const button = document.createElement("button");
    button.type = "button";
    button.textContent = `Remove ${name}`;
    button.addEventListener("click", () => {
      products.splice(index, 1);
      render();
    });
    li.append(button);
    list.append(li);
  });
}
render();
```

**Step-by-step explanation and expected behavior:** For each product, create a button. Clicking the second button removes one item at index 1 with `splice(1,1)` and re-renders the remaining list. The callback captures the index associated with the current render.

**Practice extension:** Modify the input data, predict what changes, and test the result in the browser console. Check that missing elements are handled where appropriate and that user-provided text is inserted using `textContent`.

### Exercise 7: Count matching DOM elements

**Task:** Count matching DOM elements. Study the solution, then rewrite it without looking.

**HTML**

```html
<p class="item">A</p><p class="item">B</p><p class="item">C</p><output id="count"></output>
```

**JavaScript model solution**

```javascript
const items = document.querySelectorAll(".item");
// A: plain loop
let countA = 0;
for (const item of items) countA++;
// B: collection length
const countB = items.length;
document.getElementById("count").textContent = `${countA} / ${countB}`;
```

**Step-by-step explanation and expected behavior:** The loop increments 0 to 1 to 2 to 3. `.length` gives 3 immediately from the selected NodeList. Both count only the snapshot of matching elements at query time.

**Practice extension:** Modify the input data, predict what changes, and test the result in the browser console. Check that missing elements are handled where appropriate and that user-provided text is inserted using `textContent`.

### Exercise 8: Filter elements by data-price

**Task:** Filter elements by data-price. Study the solution, then rewrite it without looking.

**HTML**

```html
<ul><li class="product" data-price="50000">Laptop</li><li class="product" data-price="1500">Mouse</li><li class="product" data-price="3500">Keyboard</li></ul>
```

**JavaScript model solution**

```javascript
const elements = document.querySelectorAll(".product");
// A: plain loop, show only cheap products
for (const element of elements) {
  const price = Number(element.dataset.price);
  element.hidden = !Number.isFinite(price) || price >= 5000;
}
// B: methods, calculate cheap names
const cheapNames = Array.from(elements)
  .filter(element => Number(element.dataset.price) < 5000)
  .map(element => element.textContent);
console.log(cheapNames); // ["Mouse", "Keyboard"]
```

**Step-by-step explanation and expected behavior:** `dataset.price` is a string. `Number` converts it. `element.hidden` controls visibility; `!` negates the boolean. The method version filters a converted array and maps elements to names. The two examples demonstrate different outputs: visibility vs name list.

**Practice extension:** Modify the input data, predict what changes, and test the result in the browser console. Check that missing elements are handled where appropriate and that user-provided text is inserted using `textContent`.

### Exercise 9: Build a student table

**Task:** Build a student table. Study the solution, then rewrite it without looking.

**HTML**

```html
<table><thead><tr><th>Name</th><th>Score</th><th>Status</th></tr></thead><tbody id="rows"></tbody></table>
```

**JavaScript model solution**

```javascript
const students = [{name:"Alice",score:85},{name:"Bob",score:42}];
const tbody = document.getElementById("rows");
function makeRow(student) {
  const tr = document.createElement("tr");
  for (const value of [student.name, student.score, student.score >= 50 ? "Pass" : "Fail"]) {
    const td = document.createElement("td");
    td.textContent = String(value);
    tr.append(td);
  }
  return tr;
}
// A: plain loop
for (const student of students) tbody.append(makeRow(student));
// B: method (clear before rerender)
tbody.replaceChildren();
students.forEach(student => tbody.append(makeRow(student)));
```

**Step-by-step explanation and expected behavior:** The outer loop processes two student objects; the inner loop creates three cells for each. The ternary `condition ? a : b` chooses Pass or Fail. Both approaches produce two table rows and safely insert text.

**Practice extension:** Modify the input data, predict what changes, and test the result in the browser console. Check that missing elements are handled where appropriate and that user-provided text is inserted using `textContent`.

### Exercise 10: Dynamic product manager

**Task:** Dynamic product manager. Study the solution, then rewrite it without looking.

**HTML**

```html
<form id="form"><input id="name" required><button>Add</button></form><p id="count"></p><ul id="list"></ul>
```

**JavaScript model solution**

```javascript
const form = document.getElementById("form");
const input = document.getElementById("name");
const count = document.getElementById("count");
const list = document.getElementById("list");
let products = [];
function row(name, index) {
  const li = document.createElement("li");
  const label = document.createElement("span");
  label.textContent = name;
  const remove = document.createElement("button");
  remove.type = "button";
  remove.textContent = "Remove";
  remove.addEventListener("click", () => {
    products.splice(index, 1);
    renderPlain();
  });
  li.append(label, remove);
  return li;
}
function renderPlain() {
  list.replaceChildren();
  for (let i = 0; i < products.length; i++) list.append(row(products[i], i));
  count.textContent = `Count: ${products.length}`;
}
function renderMethods() {
  list.replaceChildren();
  products.forEach((name, i) => list.append(row(name, i)));
  count.textContent = `Count: ${products.length}`;
}
form.addEventListener("submit", event => {
  event.preventDefault();
  const name = input.value.trim();
  if (!name) return;
  products.push(name);
  input.value = "";
  renderPlain();
});
renderPlain();
```

**Step-by-step explanation and expected behavior:** Adding A then B changes `products` from [] to [A] to [A,B], and the count from 0 to 1 to 2. Clicking Remove on A calls `splice(0,1)`, leaving [B]. `renderPlain` uses indexes; `renderMethods` uses `forEach`. To keep the method renderer active after clicks, use `renderMethods()` in both the remove and submit callbacks. The two renderers are alternatives, not meant to run simultaneously.

**Practice extension:** Modify the input data, predict what changes, and test the result in the browser console. Check that missing elements are handled where appropriate and that user-provided text is inserted using `textContent`.

## 21. Mastery checklist and navigation

- [ ] Explain HTML vs DOM and element vs text nodes.
- [ ] Use `getElementById`, `querySelector`, and `querySelectorAll` correctly.
- [ ] Guard against `null` selectors.
- [ ] Explain NodeList vs HTMLCollection, static vs live.
- [ ] Process DOM collections with `for`, `for...of`, and `forEach`.
- [ ] Convert NodeList to an array before `map` or `filter`.
- [ ] Choose `textContent` over `innerHTML` for untrusted text.
- [ ] Update styles, classes, attributes, and dataset properties.
- [ ] Create, append, prepend, remove, and replace elements.
- [ ] Traverse parents, children, and siblings.
- [ ] Explain script `defer` and `DOMContentLoaded`.
- [ ] Build and debug a simple dynamic list and table.
- [ ] Solve all ten exercises using loops and methods where applicable.

**Previous:** [Lesson 14 - JSON](14-javascript-json-complete-guide.md)  
**Next:** [Lesson 16 - JavaScript Events](16-javascript-events.md)  
**Later:** Object-Oriented Programming in JavaScript (postponed)
