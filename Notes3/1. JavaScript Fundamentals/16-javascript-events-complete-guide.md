# Lesson 16: JavaScript Events - Complete Guide

**Previous:** [Lesson 15 - DOM Manipulation](15-javascript-dom-manipulation-complete-guide.md)  
**Next:** [Lesson 17 - Asynchronous JavaScript](17-javascript-asynchronous-complete-guide.md)  
**Status:** Beginner to advanced | Examples use browser JavaScript

## Table of contents

1. [What is an event?](#1-what-is-an-event)
2. [Three ways to handle events](#2-three-ways-to-handle-events)
3. [Understanding addEventListener](#3-understanding-addeventlistener)
4. [Click events and counters](#4-click-events-and-counters)
5. [The event object](#5-the-event-object)
6. [target vs currentTarget](#6-target-vs-currenttarget)
7. [Mouse and pointer events](#7-mouse-and-pointer-events)
8. [Keyboard events](#8-keyboard-events)
9. [Input and change events](#9-input-and-change-events)
10. [Form submission](#10-form-submission)
11. [Bubbling](#11-bubbling)
12. [Capturing](#12-capturing)
13. [preventDefault and propagation](#13-preventdefault-and-propagation)
14. [Event delegation](#14-event-delegation)
15. [Plain loops vs methods](#15-plain-loops-vs-methods)
16. [Removing listeners](#16-removing-listeners)
17. [this in handlers](#17-this-in-handlers)
18. [Listener options](#18-listener-options)
19. [Debouncing and throttling](#19-debouncing-and-throttling)
20. [Custom events](#20-custom-events)
21. [Practical task-list project](#21-practical-task-list-project)
22. [Interview questions and answers](#22-interview-questions-and-answers)
23. [Ten coding exercises with solutions](#23-ten-coding-exercises-with-solutions)
24. [Mastery checklist](#24-mastery-checklist)

**How to run examples:** Save HTML as `index.html`, place JavaScript inside a `<script>` tag immediately before `</body>` (or load an external script with `defer`), and open the page in a browser. Open Developer Tools > Console for logged output. Code snippets in different sections are independent unless stated otherwise.

## 1. What is an event?

An event is a browser notification that something happened: a click, key press, value change, submission, scroll, or a custom application action. A listener is a function registered to run when an event is dispatched.

```html
<h1 id="title">Hello World</h1>
<button id="changeBtn">Change heading</button>
<script>
  const title = document.getElementById("title");
  const button = document.getElementById("changeBtn");
  button.addEventListener("click", function () {
    title.textContent = "Hello JavaScript!";
  });
</script>
```

Execution: (1) browser parses elements; (2) JavaScript selects them; (3) listener is registered; (4) a click dispatches an event; (5) callback changes text. Registration does **not** execute the callback immediately.

## 2. Three ways to handle events

**Inline HTML (generally avoid):**

```html
<button onclick="sayHello()">Click</button>
<script>
  function sayHello() { alert("Hello"); }
</script>
```

**Event property (one assigned handler):**

```javascript
const button = document.querySelector("button");
button.onclick = function () { console.log("First"); };
button.onclick = function () { console.log("Second"); }; // Replaces first
```

**addEventListener (recommended):**

```javascript
button.addEventListener("click", () => console.log("First"));
button.addEventListener("click", () => console.log("Second"));
```

The last example registers two distinct listeners; both run for a click. Avoid mixing inline JavaScript with HTML when possible.

## 3. Understanding addEventListener

```javascript
element.addEventListener(type, listener, options);
```

- `element`: event target, such as a button or `document`.
- `.`: property-access operator.
- `addEventListener`: method that registers a callback.
- `type`: event name as a string, e.g. `"click"` (not `"onclick"`).
- `listener`: function reference called later when the event is dispatched.
- `options`: optional boolean capture flag or options object.
- `()` calls a function; `{}` encloses a function body; `;` terminates a statement.

```javascript
function sayHello(event) {
  console.log("Hello", event.type);
}
button.addEventListener("click", sayHello);   // Correct: pass reference
// button.addEventListener("click", sayHello()); // Wrong: calls now
button.addEventListener("click", function (event) { console.log(event.type); });
button.addEventListener("click", (event) => console.log(event.type));
```

`function` creates a regular function; `(event) => ...` creates an arrow function. `event` is a parameter supplied by the browser when the callback runs. The variable name can be `e` or another valid identifier.

## 4. Click events and counters

```html
<h2 id="count">0</h2>
<button id="increase">Increase</button>
<button id="decrease">Decrease</button>
<button id="reset">Reset</button>
<script>
  let count = 0;
  const display = document.getElementById("count");
  document.getElementById("increase").addEventListener("click", () => {
    count++;
    display.textContent = count;
  });
  document.getElementById("decrease").addEventListener("click", () => {
    count--;
    display.textContent = count;
  });
  document.getElementById("reset").addEventListener("click", () => {
    count = 0;
    display.textContent = count;
  });
</script>
```

`let` allows reassignment. `count++` increments by one; as a standalone statement it is equivalent to `count += 1` or `count = count + 1`. **Postfix detail:** `let x = count++` assigns the *old* count to `x`, then increments count. `++count` increments first and yields the new value.

| Action | Count before | Count after | Display |
|---|---:|---:|---:|
| Increase | 0 | 1 | 1 |
| Increase | 1 | 2 | 2 |
| Decrease | 2 | 1 | 1 |
| Reset | 1 | 0 | 0 |

## 5. The event object

```javascript
button.addEventListener("click", function (event) {
  console.log(event.type);             // "click"
  console.log(event.target);           // Original target
  console.log(event.currentTarget);    // Target whose listener is running
  console.log(event.defaultPrevented); // Boolean
  console.log(event.timeStamp);        // Timestamp
});
```

`event.isTrusted` indicates whether the event was generated by the user agent rather than dispatched by script; do not treat it as a complete security boundary.

## 6. target vs currentTarget

```html
<button id="parentButton"><span>Click label</span></button>
<script>
  const parentButton = document.getElementById("parentButton");
  parentButton.addEventListener("click", (event) => {
    console.log(event.target.tagName);        // May be SPAN
    console.log(event.currentTarget.tagName); // BUTTON
  });
</script>
```

`target` identifies where the event originated; `currentTarget` identifies the object whose handler is currently executing. The two are often equal for direct clicks, but can differ for nested elements.

## 7. Mouse and pointer events

Common mouse events: `click`, `dblclick`, `mousedown`, `mouseup`, `mousemove`, `mouseenter`, `mouseleave`, `mouseover`, `mouseout`. `mouseenter` does not bubble; `mouseover` does. For mouse, touch and pen input, consider `pointerdown`, `pointermove`, and `pointerup`.

```html
<div id="box" style="width:160px;height:80px;background:lightgray">Hover here</div>
<script>
  const box = document.getElementById("box");
  box.addEventListener("mouseenter", () => box.style.backgroundColor = "lightblue");
  box.addEventListener("mouseleave", () => box.style.backgroundColor = "lightgray");
</script>
```

## 8. Keyboard events

```html
<input id="keyboard" placeholder="Press a key">
<script>
  const keyboard = document.getElementById("keyboard");
  keyboard.addEventListener("keydown", (event) => {
    console.log("key:", event.key, "code:", event.code);
    if (event.key === "Enter") console.log("Enter pressed");
  });
</script>
```

`keydown` fires when a key is pressed; `keyup` when released. `event.key` represents the key meaning, while `event.code` represents its physical position. Modifier flags include `ctrlKey`, `shiftKey`, `altKey`, and `metaKey`. Use accessible shortcuts and avoid overriding expected browser behavior.

## 9. Input and change events

```html
<input id="name" placeholder="Your name">
<p id="preview"></p>
<script>
  const input = document.getElementById("name");
  const preview = document.getElementById("preview");
  input.addEventListener("input", () => {
    preview.textContent = input.value;
  });
  input.addEventListener("change", () => {
    console.log("Committed value:", input.value);
  });
</script>
```

`input` usually fires as the user edits; `change` generally fires when a changed value is committed. For text inputs, that is commonly on blur. Use `.value` to read the current form-control value; use `.textContent` for safe text output.

## 10. Form submission

```html
<form id="signup">
  <input id="email" type="email" required>
  <button type="submit">Submit</button>
</form>
<p id="result"></p>
<script>
  const form = document.getElementById("signup");
  form.addEventListener("submit", (event) => {
    event.preventDefault();
    const email = document.getElementById("email").value.trim();
    document.getElementById("result").textContent = `Submitted: ${email}`;
  });
</script>
```

`preventDefault()` cancels a cancelable default action, such as normal form navigation. `required` and `type="email"` provide browser validation; production applications must also validate on the server. `trim()` removes leading and trailing whitespace.

## 11. Bubbling

```html
<div id="outer"><div id="inner"><button id="child">Click</button></div></div>
<script>
  document.getElementById("outer").addEventListener("click", () => console.log("Outer"));
  document.getElementById("inner").addEventListener("click", () => console.log("Inner"));
  document.getElementById("child").addEventListener("click", () => console.log("Child"));
</script>
```

Clicking the button logs `Child`, `Inner`, `Outer`. This is bubbling: the event moves from the target through ancestors. Not all events bubble (`focus` and `blur` do not; `focusin` and `focusout` do).

## 12. Capturing

```javascript
outer.addEventListener("click", () => console.log("Outer capture"), { capture: true });
inner.addEventListener("click", () => console.log("Inner capture"), { capture: true });
child.addEventListener("click", () => console.log("Child target"));
```

For a click on `child`, capturing listeners on `outer` and `inner` run before the target listener. Typical flow: ancestors during capturing -> target -> ancestors during bubbling. The third argument may be `true` instead of `{ capture: true }`.

## 13. preventDefault and propagation

```javascript
link.addEventListener("click", (event) => event.preventDefault());
child.addEventListener("click", (event) => event.stopPropagation());
child.addEventListener("click", (event) => event.stopImmediatePropagation());
```

- `preventDefault()` prevents a cancelable default action; it does not stop propagation.
- `stopPropagation()` stops the event from traveling to additional nodes; other listeners on the current node may still run.
- `stopImmediatePropagation()` also stops subsequent listeners on the current target.

## 14. Event delegation

```html
<ul id="products">
  <li data-id="1"><span>Laptop</span></li>
  <li data-id="2"><span>Mouse</span></li>
</ul>
<script>
  const list = document.getElementById("products");
  list.addEventListener("click", (event) => {
    const item = event.target.closest("li[data-id]");
    if (!item || !list.contains(item)) return;
    console.log(item.dataset.id, item.textContent.trim());
  });
</script>
```

`closest(selector)` searches the target and its ancestors. `!item` checks for no match. `||` means logical OR. `return` exits the callback early. `.dataset.id` reads `data-id` as a string. One parent listener handles existing and later-added matching children because clicks bubble.

## 15. Plain loops vs methods

```html
<div id="buttonContainer">
  <button class="action">One</button>
  <button class="action">Two</button>
  <button class="action">Three</button>
</div>
```

**Plain loop:**

```javascript
const buttons = document.querySelectorAll(".action");
for (let i = 0; i < buttons.length; i++) {
  buttons[i].addEventListener("click", () => console.log(`Button ${i + 1}`));
}
```

**Built-in NodeList method:**

```javascript
buttons.forEach((button, index) => {
  button.addEventListener("click", () => console.log(`Button ${index + 1}`));
});
```

**Delegation:**

```javascript
const container = document.getElementById("buttonContainer");
container.addEventListener("click", (event) => {
  const button = event.target.closest(".action");
  if (!button || !container.contains(button)) return;
  console.log(button.textContent);
});
```

| Approach | Listener count for 3 buttons | Automatically handles new buttons? |
|---|---:|---|
| `for` | 3 | No |
| `forEach` | 3 | No |
| Delegation | 1 | Yes |

`i++` increments the loop index after each iteration; `i < buttons.length` keeps the index within bounds. `forEach` supplies the current element and index as callback arguments. A plain `for` loop supports `break` and `continue`; `forEach` does not. NodeLists from `querySelectorAll` have `forEach`, but not `map` or `filter` directly; use `Array.from(buttons)` for those.

## 16. Removing listeners

```javascript
function handleClick() { console.log("Clicked"); }
button.addEventListener("click", handleClick);
button.removeEventListener("click", handleClick);
```

You must use the same function reference and matching capture setting. Two separately written anonymous functions are different objects even if their source text is identical.

```javascript
button.addEventListener("click", handleClick, { once: true });
const controller = new AbortController();
button.addEventListener("click", handleClick, { signal: controller.signal });
controller.abort(); // Removes listeners registered with this signal
```

## 17. this in handlers

```javascript
button.addEventListener("click", function (event) {
  console.log(this === event.currentTarget); // true
});
button.addEventListener("click", (event) => {
  console.log(event.currentTarget); // Reliable target reference
});
```

Regular listener functions receive `this` bound to `currentTarget`. Arrow functions inherit `this` lexically from their surrounding scope; they do not bind their own `this`.

## 18. Listener options

```javascript
element.addEventListener("click", handler, {
  capture: false,
  once: true,
  passive: false
});
```

`capture` selects the capturing registration; `once` automatically removes after one invocation; `passive` promises not to cancel default behavior. An optional `signal` supports cleanup through `AbortController`. Calling `preventDefault` inside a passive listener does not cancel the event.

## 19. Debouncing and throttling

**Debounce:** wait for a quiet period before performing work.

```javascript
let timer;
input.addEventListener("input", () => {
  clearTimeout(timer);
  timer = setTimeout(() => console.log("Search:", input.value), 300);
});
```

Timeline: type at 0ms -> timer scheduled for 300ms; type again at 150ms -> old timer canceled, new timer scheduled for 450ms; no further typing -> search runs around 450ms.

**Throttle:** limit how frequently work runs.

```javascript
let lastRun = 0;
window.addEventListener("scroll", () => {
  const now = Date.now();
  if (now - lastRun >= 200) {
    lastRun = now;
    console.log("Scroll update");
  }
});
```

`now - lastRun` is elapsed time in milliseconds; `>=` means greater than or equal to. Debouncing suits search boxes; throttling suits repeated scroll/resize work. These are simplified teaching implementations, not exhaustive production utilities.

## 20. Custom events

```javascript
document.addEventListener("userLoggedIn", (event) => {
  console.log(event.detail.username);
});
const loginEvent = new CustomEvent("userLoggedIn", {
  detail: { username: "Alice" }
});
document.dispatchEvent(loginEvent);
```

`CustomEvent` carries application-specific data in `.detail`. `dispatchEvent` dispatches synchronously. Custom events do not bubble by default unless created with `{ bubbles: true }`.

## 21. Practical task-list project

Save this complete file as `tasks.html` and open it in a browser. It demonstrates submission, delegated `change` and `click` handlers, DOM creation, safe text rendering, and a plain loop. The numeric ID is a local incrementing counter for this page session.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Task manager</title>
  <style>
    body { font-family: sans-serif; max-width: 520px; margin: 2rem auto; }
    li { margin: .5rem 0; display: flex; align-items: center; gap: .75rem; }
    li span { flex: 1; }
    .done { text-decoration: line-through; color: gray; }
  </style>
</head>
<body>
  <h1>Tasks</h1>
  <form id="taskForm">
    <input id="taskInput" required placeholder="New task">
    <button type="submit">Add</button>
  </form>
  <ul id="taskList"></ul>
  <p id="remainingCount">Remaining: 0</p>
  <script>
    const form = document.getElementById("taskForm");
    const input = document.getElementById("taskInput");
    const list = document.getElementById("taskList");
    const remainingCount = document.getElementById("remainingCount");
    const tasks = [];
    let nextId = 1;

    function renderTasks() {
      list.replaceChildren();
      let remaining = 0;
      for (let i = 0; i < tasks.length; i++) {
        const task = tasks[i];
        const li = document.createElement("li");
        const checkbox = document.createElement("input");
        checkbox.type = "checkbox";
        checkbox.checked = task.completed;
        checkbox.dataset.id = String(task.id);
        const label = document.createElement("span");
        label.textContent = task.text;
        if (task.completed) label.classList.add("done");
        const remove = document.createElement("button");
        remove.type = "button";
        remove.textContent = "Remove";
        remove.dataset.removeId = String(task.id);
        li.append(checkbox, label, remove);
        list.appendChild(li);
        if (!task.completed) remaining++;
      }
      remainingCount.textContent = `Remaining: ${remaining}`;
    }

    form.addEventListener("submit", (event) => {
      event.preventDefault();
      const text = input.value.trim();
      if (text === "") return;
      tasks.push({ id: nextId++, text, completed: false });
      input.value = "";
      renderTasks();
    });

    list.addEventListener("change", (event) => {
      if (!event.target.matches('input[type="checkbox"]')) return;
      const id = Number(event.target.dataset.id);
      const task = tasks.find((task) => task.id === id);
      if (!task) return;
      task.completed = event.target.checked;
      renderTasks();
    });

    list.addEventListener("click", (event) => {
      const button = event.target.closest("button[data-remove-id]");
      if (!button || !list.contains(button)) return;
      const id = Number(button.dataset.removeId);
      const index = tasks.findIndex((task) => task.id === id);
      if (index === -1) return;
      tasks.splice(index, 1);
      renderTasks();
    });
    renderTasks();
  </script>
</body>
</html>
```

**Trace:** Submit `Read book` -> `trim()` yields `Read book` -> `tasks.push` adds `{id:1,text:"Read book",completed:false}` -> `renderTasks()` creates an `li` and reports 1 remaining. Check the checkbox -> delegated `change` finds ID 1 -> `completed=true` -> rerender reports 0 remaining. Click Remove -> delegated `click` finds index 0 -> `splice(0,1)` removes the record -> rerender shows an empty list.

**Alternative using methods:** Replace the remaining-count loop with `const remaining = tasks.filter(task => !task.completed).length;` and render using `tasks.forEach(task => { ... });`. The loop can exit early with `break`; `forEach` cannot. Both render the same task records.

## 22. Interview questions and answers

**1. What is an event?**

A notification that something happened in a browser or was dispatched by code.

**2. onclick vs addEventListener?**

onclick is one handler property; addEventListener registers multiple listeners.

**3. What is the event object?**

An object describing the dispatched event, including type, target and propagation state.

**4. target vs currentTarget?**

target is the original event target; currentTarget is the target whose listener is running.

**5. What is bubbling?**

Propagation from the target upward through ancestors.

**6. What is capturing?**

Propagation through ancestors toward the target.

**7. What is delegation?**

Registering an ancestor listener to handle events from descendants.

**8. What does preventDefault do?**

It cancels the default action when the event is cancelable.

**9. How do you remove a listener?**

Use removeEventListener with the same function reference and matching capture, or an AbortSignal.

**10. Debounce vs throttle?**

Debounce waits for activity to settle; throttle limits how often work executes.

## 23. Ten coding exercises with solutions

Each HTML solution below is standalone: save it as a separate HTML file, or place it in the body of a page. Run one exercise at a time to avoid conflicting IDs. Try solving it yourself before reading the answer.

### Exercise 1: Change a heading on click

**Task:** Change a heading on click.

**Solution:**

```html
<h1 id="heading">Before</h1><button id="btn">Change</button>
<script>
  document.getElementById("btn").addEventListener("click", () => {
    document.getElementById("heading").textContent = "After";
  });
</script>
```

**Explanation / execution trace:** A click runs the callback and assigns new text to the heading.

### Exercise 2: Increase, decrease and reset a counter

**Task:** Increase, decrease and reset a counter.

**Solution:**

```html
<p id="count">0</p><button id="up">+</button><button id="down">-</button><button id="reset">Reset</button>
<script>
  let count = 0;
  const show = () => document.getElementById("count").textContent = count;
  document.getElementById("up").addEventListener("click", () => { count++; show(); });
  document.getElementById("down").addEventListener("click", () => { count--; show(); });
  document.getElementById("reset").addEventListener("click", () => { count = 0; show(); });
</script>
```

**Explanation / execution trace:** After +, +, -, Reset the values are 1, 2, 1, 0. `show` centralizes display updates.

### Exercise 3: Change a box color on mouse enter and leave

**Task:** Change a box color on mouse enter and leave.

**Solution:**

```html
<div id="box" style="width:160px;height:70px;background:gray">Hover</div>
<script>
  const box = document.getElementById("box");
  box.addEventListener("mouseenter", () => box.style.backgroundColor = "skyblue");
  box.addEventListener("mouseleave", () => box.style.backgroundColor = "gray");
</script>
```

**Explanation / execution trace:** mouseenter changes the color; mouseleave restores it.

### Exercise 4: Display the last key pressed

**Task:** Display the last key pressed.

**Solution:**

```html
<input id="keys"><p id="last">No key yet</p>
<script>
  document.getElementById("keys").addEventListener("keydown", (event) => {
    document.getElementById("last").textContent = event.key;
  });
</script>
```

**Explanation / execution trace:** Pressing Enter displays Enter. The event object supplies `.key`.

### Exercise 5: Count textarea characters live

**Task:** Count textarea characters live.

**Solution:**

```html
<textarea id="message"></textarea><p id="length">0</p>
<script>
  const message = document.getElementById("message");
  message.addEventListener("input", () => {
    document.getElementById("length").textContent = message.value.length;
  });
</script>
```

**Explanation / execution trace:** Typing abc produces counts 1, 2, 3. `.length` measures UTF-16 code units, not necessarily visible characters.

### Exercise 6: Validate a name and email form

**Task:** Validate a name and email form.

**Solution:**

```html
<form id="form"><input id="name" required><input id="email" type="email" required><button>Submit</button></form><p id="status"></p>
<script>
  document.getElementById("form").addEventListener("submit", (event) => {
    event.preventDefault();
    const name = document.getElementById("name").value.trim();
    const email = document.getElementById("email").value.trim();
    document.getElementById("status").textContent = name && email ? "Valid submission" : "Complete all fields";
  });
</script>
```

**Explanation / execution trace:** The browser also enforces required/email constraints before firing a normal submit event. Always validate again server-side.

### Exercise 7: Observe bubbling and capturing

**Task:** Observe bubbling and capturing.

**Solution:**

```html
<div id="outer"><button id="inner">Click</button></div>
<script>
  const outer = document.getElementById("outer");
  const inner = document.getElementById("inner");
  outer.addEventListener("click", () => console.log("capture"), {capture:true});
  inner.addEventListener("click", () => console.log("target"));
  outer.addEventListener("click", () => console.log("bubble"));
</script>
```

**Explanation / execution trace:** Clicking the button logs capture, target, bubble in that order.

### Exercise 8: Identify clicked products with delegation

**Task:** Identify clicked products with delegation.

**Solution:**

```html
<ul id="list"><li data-id="1">Apple</li><li data-id="2">Banana</li></ul>
<script>
  const list = document.getElementById("list");
  list.addEventListener("click", (event) => {
    const item = event.target.closest("li[data-id]");
    if (!item || !list.contains(item)) return;
    console.log(item.dataset.id);
  });
</script>
```

**Explanation / execution trace:** Clicking Banana logs 2. A single listener serves both items.

### Exercise 9: Create buttons with a loop and forEach

**Task:** Create buttons with a loop and forEach.

**Solution:**

```html
<div id="plain"></div><div id="method"></div>
<script>
  const labels = ["One", "Two", "Three"];
  const plain = document.getElementById("plain");
  for (let i = 0; i < labels.length; i++) {
    const button = document.createElement("button");
    button.textContent = labels[i];
    button.addEventListener("click", () => console.log(labels[i]));
    plain.append(button);
  }
  const method = document.getElementById("method");
  labels.forEach((label) => {
    const button = document.createElement("button");
    button.textContent = label;
    button.addEventListener("click", () => console.log(label));
    method.append(button);
  });
</script>
```

**Explanation / execution trace:** Both produce three buttons. The loop uses `i` to index labels; forEach passes each label to the callback.

### Exercise 10: Build a task manager with add, complete, remove and counts

**Task:** Build a task manager with add, complete, remove and counts.

**Solution:**

```html
See the complete runnable `tasks.html` solution in Section 21. Try rewriting `renderTasks()` with `tasks.forEach()` and calculate remaining with `tasks.filter(task => !task.completed).length`.
```

**Explanation / execution trace:** Use the Section 21 project as the complete solution: submit adds records, delegated change updates completion, delegated click removes records, and rendering recalculates counts.

## 24. Mastery checklist

- [ ] I can explain what a browser event and listener are.
- [ ] I can distinguish passing a function from calling it.
- [ ] I can use click, keyboard, input, change, and submit events.
- [ ] I can explain `event.target` vs `event.currentTarget`.
- [ ] I can predict capturing, target, and bubbling order.
- [ ] I know the difference between `preventDefault()` and `stopPropagation()`.
- [ ] I can use event delegation for dynamic lists.
- [ ] I can compare a plain `for` loop with `forEach` for listener registration.
- [ ] I can remove listeners and use `{ once: true }`.
- [ ] I can explain debouncing, throttling, and custom events.
- [ ] I can build and test the task-list project.

---

**Previous:** [Lesson 15 - DOM Manipulation](15-javascript-dom-manipulation-complete-guide.md)  
**Next:** [Lesson 17 - Asynchronous JavaScript](17-javascript-asynchronous-complete-guide.md)
