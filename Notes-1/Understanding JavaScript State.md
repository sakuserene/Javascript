---

title: "CRUD with Plain Loops, Array Methods, DOM, and JavaScript State"
phase: "Phase 1: JavaScript Application Foundations"
module: "Module 1: CRUD, State, and DOM Foundations"
topic: "Topic 1: CRUD Operations with JavaScript State and the DOM"
subtopic: "Subtopic 2: Understanding JavaScript State"
difficulty: "Beginner"
estimated_time: "60–90 minutes"
prerequisites:

* Basic JavaScript variables
* Arrays
* Objects
* Functions
  previous_topic: "CRUD Operations with JavaScript State and the DOM"
  previous_subtopic: "Understanding CRUD"
  next_topic: "JavaScript State and DOM Synchronization"
  next_subtopic: "Understanding DOM State"

---

# Understanding JavaScript State

## Course Navigation

| Direction         | Link                                                                                      |
| ----------------- | ----------------------------------------------------------------------------------------- |
| Previous Topic    | [CRUD Operations with JavaScript State and the DOM](./README.md)                          |
| Previous Subtopic | [Understanding CRUD](./01-understanding-crud.md)                                          |
| Next Subtopic     | [Understanding DOM State](./03-understanding-dom-state.md)                                |
| Next Topic        | [JavaScript State and DOM Synchronization](../02-state-and-dom-synchronization/README.md) |

> **Current Position:** Phase 1 → Module 1 → Topic 1 → Subtopic 2

---

# Learning Objectives

By the end of this lesson you should be able to:

* Explain what JavaScript state is.
* Explain why applications need state.
* Explain why state is stored in memory.
* Recognize state in real-world applications.
* Distinguish between ordinary variables and application state.
* Explain why CRUD applications commonly use an array of objects.
* Explain why state is called the **source of truth**.
* Prepare for rendering state into the DOM.

---

# Prerequisites

You should already understand:

* Variables
* Arrays
* Objects
* CRUD operations

---

# What You Will Build

Later in this topic you will build:

* Shopping List
* Product Inventory
* Task Manager

Each application stores its data inside JavaScript state.

---

# 2. Understanding JavaScript State

## What You Will Learn

In this lesson you will learn:

* What state means.
* Why every application has state.
* What application state looks like.
* Why state is important.
* Why JavaScript stores application data.
* Why frameworks such as React and Vue revolve around state.

### Why This Matters

Suppose you open a shopping application.

```text
Shopping List

Milk
Bread
Eggs
```

Those items must come from somewhere.

JavaScript remembers them while the application is running.

That remembered information is called **state**.

Without state:

* Nothing can be added.
* Nothing can be updated.
* Nothing can be deleted.

CRUD depends entirely on state.

---

# What Is JavaScript State?

> **JavaScript state is the data your application currently remembers while it is running.**

Think of state as the application's memory.

Example:

```js
let username = "John";
```

Current state:

```text
John
```

Another example:

```js
let score = 45;
```

Current state:

```text
45
```

The values currently stored by the program are its state.

---

# State Can Store Many Types of Data

State is not limited to arrays.

A string:

```js
let username = "John";
```

A number:

```js
let age = 22;
```

A boolean:

```js
let isLoggedIn = true;
```

An object:

```js
let user = {
  id: 1,
  name: "John",
};
```

An array:

```js
let products = [
  "Laptop",
  "Phone",
];
```

An array of objects:

```js
let products = [
  {
    id: 1,
    name: "Laptop",
  },
  {
    id: 2,
    name: "Phone",
  },
];
```

For CRUD applications, an **array of objects** is the most common choice.

---

# Why Use an Array of Objects?

A product has multiple properties.

```js
{
  id: 1,
  name: "Laptop",
  price: 50000,
  quantity: 2,
}
```

If an application has many products:

```js
let products = [
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2,
  },
  {
    id: 2,
    name: "Phone",
    price: 25000,
    quantity: 5,
  },
  {
    id: 3,
    name: "Keyboard",
    price: 3000,
    quantity: 8,
  },
];
```

The entire array becomes the application's state.

---

# Real Applications That Use State

## Shopping Cart

```js
let cart = [
  {
    id: 1,
    name: "Milk",
    quantity: 2,
  },
];
```

## Student Records

```js
let students = [
  {
    id: 1,
    name: "Alice",
  },
];
```

## Task Manager

```js
let tasks = [
  {
    id: 1,
    title: "Study JavaScript",
    completed: false,
  },
];
```

## Inventory

```js
let inventory = [
  {
    id: 1,
    name: "Laptop",
    quantity: 5,
  },
];
```

Although these applications solve different problems, they all keep important information in JavaScript state.

---

# State Changes Over Time

Initially:

```js
let products = [];
```

State:

```text
No products
```

After creating one product:

```js
products.push({
  id: 1,
  name: "Laptop",
});
```

State:

```text
Laptop
```

After another create:

```js
products.push({
  id: 2,
  name: "Phone",
});
```

State:

```text
Laptop
Phone
```

After deleting the first product:

```js
products.splice(0, 1);
```

State:

```text
Phone
```

The application's memory changes as CRUD operations occur.

---

# Why Is It Called "State"?

Imagine a traffic light.

It changes between:

```text
Red
```

```text
Yellow
```

```text
Green
```

Each condition is a different **state**.

Applications work the same way.

Morning inventory:

```text
Laptop: 5
Phone: 3
```

Afternoon inventory:

```text
Laptop: 4
Phone: 8
```

Evening inventory:

```text
Laptop: 2
Phone: 10
```

The application's current condition changes over time.

That changing condition is called its **state**.

---

# Variables vs Application State

Every piece of state is stored in a variable.

However, not every variable is considered application state.

Temporary variable:

```js
let x = 5;
```

Application state:

```js
let currentUser = {
  id: 1,
  name: "Alice",
};
```

| Variable                  | Application State? | Reason                                |
| ------------------------- | ------------------ | ------------------------------------- |
| `let x = 5`               | Usually No         | Temporary calculation                 |
| `let currentUser = {...}` | Yes                | Represents important application data |
| `let cart = []`           | Yes                | Stores shopping cart contents         |
| `let tasks = []`          | Yes                | Stores tasks                          |
| `let inventory = []`      | Yes                | Stores products                       |

---

# State Is the Source of Truth

One of the most important ideas in JavaScript is:

> **The JavaScript state is the source of truth.**

Example:

```js
let products = [
  {
    id: 1,
    name: "Laptop",
  },
];
```

If the array contains one laptop, then the application believes one laptop exists.

Later, the webpage should simply display whatever exists in the state.

The array is trusted.

The webpage is only a representation of that data.

---

# Why Not Store Everything in HTML?

Consider:

```html
<li>Laptop</li>
```

Questions:

* Where is the ID?
* Where is the quantity?
* Where is the price?
* Where is the stock level?

HTML is designed to **display** information.

JavaScript state is designed to **store** information.

---

# Three Complete Examples

## Example 1 — Basic

### Problem

Store a student's name.

### Code

```js
let studentName = "Alice";

console.log(studentName);
```

### Expected Output

```text
Alice
```

### Final State

```text
studentName = "Alice"
```

### Challenge

Change the stored name to your own.

---

## Example 2 — Intermediate

### Problem

Store product information.

### Code

```js
let products = [
  {
    id: 1,
    name: "Laptop",
    price: 50000,
  },
  {
    id: 2,
    name: "Phone",
    price: 25000,
  },
];

console.log(products);
```

### Expected Output

```text
[
  { id: 1, name: "Laptop", price: 50000 },
  { id: 2, name: "Phone", price: 25000 }
]
```

### Final State

Two products are stored in memory.

### Challenge

Add a keyboard product.

---

## Example 3 — Browser State

### Problem

Store products in JavaScript without displaying them.

### HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <title>State Example</title>
</head>
<body>

<h1>Inventory</h1>

<script>
let products = [
  {
    id: 1,
    name: "Laptop",
    price: 50000,
  },
  {
    id: 2,
    name: "Phone",
    price: 25000,
  },
];

console.log(products);
</script>

</body>
</html>
```

### Expected Browser Output

```text
Inventory
```

### Expected Console Output

```text
[
  { id: 1, name: "Laptop", price: 50000 },
  { id: 2, name: "Phone", price: 25000 }
]
```

### Final State

```text
Two products stored in JavaScript memory.
Nothing has been displayed from the state yet.
```

### Challenge

Add one more product and verify it appears in the console.

---

# Common Beginner Mistakes

### Mistake 1

Thinking state must always be visible.

State can exist entirely in JavaScript memory.

---

### Mistake 2

Thinking HTML stores application data.

HTML displays data.

JavaScript stores data.

---

### Mistake 3

Recreating state repeatedly.

Incorrect:

```js
function addProduct() {
  let products = [];
}
```

Correct:

```js
let products = [];

function addProduct() {
  // Modify the existing state.
}
```

---

### Mistake 4

Using many unrelated variables instead of objects.

Instead of:

```js
let name = "Laptop";
let price = 50000;
```

Prefer:

```js
const product = {
  name: "Laptop",
  price: 50000,
};
```

---

# Beginner Questions

1. What is JavaScript state?
2. Why do applications need state?
3. Is state always an array?
4. Why are arrays useful?
5. Why are objects useful?
6. What is an array of objects?
7. Why is state called application memory?
8. What is the source of truth?
9. Why should HTML not be the primary place for storing application data?
10. Which data type is most commonly used for CRUD applications?

---

# Intermediate Questions

1. Why do CRUD applications commonly use arrays of objects?
2. Can state exist without a webpage?
3. Why is state more important than the displayed HTML?
4. What happens when state changes?
5. Why should state not be recreated repeatedly?
6. Why are IDs stored inside state?
7. What information belongs inside a product object?
8. What happens when duplicate IDs exist?
9. Why is state useful for inventory systems?
10. Why should application logic depend on state rather than HTML?

---

# Coding Exercises

1. Create an empty `students` array.
2. Store three student objects.
3. Create an empty `products` array.
4. Store five product objects.
5. Create a shopping cart state.
6. Add a `completed` property to a task object.
7. Add a `quantity` property to a product.
8. Add a `category` property to a product.
9. Print every state variable using `console.log()`.
10. Create your own state for a simple library system.

---

# Key Takeaways

* State is the data an application remembers while it is running.
* State can be any JavaScript value.
* CRUD applications usually use an array of objects as state.
* State changes whenever CRUD operations occur.
* The JavaScript state should be the application's **source of truth**.
* HTML is responsible for displaying information, while JavaScript state is responsible for storing information.

---

## Continue Learning

| Direction         | Link                                                                                      |
| ----------------- | ----------------------------------------------------------------------------------------- |
| Previous Topic    | [CRUD Operations with JavaScript State and the DOM](./README.md)                          |
| Previous Subtopic | [Understanding CRUD](./01-understanding-crud.md)                                          |
| Next Subtopic     | [Understanding DOM State](./03-understanding-dom-state.md)                                |
| Next Topic        | [JavaScript State and DOM Synchronization](../02-state-and-dom-synchronization/README.md) |

> **Current Position:** Phase 1 → Module 1 → Topic 1 → Subtopic 2

When you are ready to continue, type:

```text
Next
```

