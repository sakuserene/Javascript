---
title: "Async/Await (Part 1) — Introduction, First Principles, async Functions & await"
description: "Understand Async/Await from first principles, why it was introduced, how it works internally, and why it is built on top of Promises."
course: "JavaScript Beginner → Professional Mastery"
module: "Async/Await"
topic: "Introduction to Async/Await"
part: 1
difficulty: "Intermediate → Professional"
estimated_time: "7–8 Hours"
---

# Async/Await (Part 1)

# Introduction to Async/Await

> **This module marks another major milestone in your JavaScript journey.**

By now you have mastered:

- Functions
- Higher-Order Functions
- Callback Functions
- Event Loop
- Browser APIs
- Promises
- Promise Chaining
- Promise Combinators
- Promise Error Handling

You now have everything required to understand **Async/Await** from first principles.

Many tutorials introduce Async/Await as a completely new feature.

That is incorrect.

> **Async/Await is built entirely on top of Promises.**

It does **not** replace Promises.

It provides a **cleaner syntax** for working with them.

Understanding this distinction is essential for professional JavaScript development.

---

# Navigation

| Navigation | Link |
|------------|------|
| Previous Topic | Promises |
| Previous Subtopic | Building Custom Promise APIs & Promisification |
| Current Topic | Async/Await |
| Current Subtopic | Introduction to Async/Await |
| Next Subtopic | Async Functions in Depth |
| Next Topic | DOM |

---

# Table of Contents

1. Why Async/Await Was Introduced
2. The Problems It Solves
3. Async/Await Is Not Magic
4. What Is an Async Function?
5. What Does `await` Do?
6. Internal JavaScript Behaviour
7. Execution Timeline
8. Three Progressive Examples
9. Common Mistakes
10. Best Practices
11. Performance Considerations
12. Interview Questions
13. Summary

---

# Why Was Async/Await Introduced?

Promises solved one major problem:

```
Callback Hell
```

Instead of writing

```javascript
login(function () {

    getProfile(function () {

        getTransactions(function () {

            printReceipt(function () {

                syncCloud(function () {

                });

            });

        });

    });

});
```

we could write

```javascript
login()

.then(getProfile)

.then(getTransactions)

.then(printReceipt)

.then(syncCloud);
```

This was a huge improvement.

However,

large Promise chains still became difficult to read.

Imagine

```javascript
authenticate()

.then(getProfile)

.then(validateProfile)

.then(getTransactions)

.then(validateTransactions)

.then(generateReport)

.then(uploadReport)

.then(sendNotification)

.then(updateDashboard)

.catch(handleError);
```

This is much better than Callback Hell,

but it still requires thinking in terms of chained callbacks.

JavaScript developers wanted asynchronous code that looked like ordinary synchronous code.

That desire led to **Async/Await**.

---

# The Main Goal

Async/Await makes asynchronous code look like synchronous code.

Compare these.

Promise version

```javascript
fetchUser()

.then(user => {

    return fetchOrders(user.id);

})

.then(orders => {

    console.log(orders);

});
```

Async/Await version

```javascript
const user = await fetchUser();

const orders = await fetchOrders(user.id);

console.log(orders);
```

Notice the difference.

Instead of mentally following a chain,

you read the code from top to bottom.

---

# Async/Await Is Not a Replacement for Promises

One of the biggest misconceptions in JavaScript is:

> "Async/Await replaced Promises."

This is false.

Async/Await **uses Promises internally.**

Every `await` works with a Promise.

If there were no Promises,

there would be no Async/Await.

---

# Mental Model

Think of Promises as an engine.

```
Promise Engine

↓

Handles Asynchronous Work
```

Now imagine Async/Await as the dashboard of a car.

```
Dashboard

↓

Uses Engine

↓

Doesn't Replace Engine
```

The dashboard gives you a nicer interface,

but the engine still performs the work.

---

# What Is an Async Function?

An **async function** is a function declared using the `async` keyword.

General syntax

```javascript
async function greet() {

}
```

or

```javascript
const greet = async function () {

};
```

or

```javascript
const greet = async () => {

};
```

---

# What Does the `async` Keyword Mean?

The keyword

```javascript
async
```

tells JavaScript:

> **This function always returns a Promise.**

This is true even if you explicitly return a normal value.

Example

```javascript
async function greet() {

    return "Hello";

}
```

Question:

Does this return

```text
"Hello"
```

No.

It actually returns

```javascript
Promise.resolve("Hello")
```

JavaScript performs this wrapping automatically.

---

# Internal Behaviour

Suppose we write

```javascript
async function add() {

    return 10;

}
```

Conceptually,

JavaScript behaves as though you had written

```javascript
function add() {

    return Promise.resolve(10);

}
```

These are functionally equivalent.

The `async` keyword simply saves you from writing `Promise.resolve(...)` yourself.

---

# What Does `await` Do?

The `await` keyword can only be used inside an async function (or at the top level in environments that support top-level `await`).

General syntax

```javascript
const result = await promise;
```

Its job is simple.

> **Pause the async function until the Promise settles.**

Notice the wording.

It does **not** pause the entire JavaScript program.

It only pauses the current async function.

---

# Mental Model

Imagine a chef cooking three dishes.

One dish needs to bake for 20 minutes.

Instead of standing in front of the oven doing nothing,

the chef prepares other dishes.

Similarly,

`await` pauses only the current async function while the rest of the JavaScript runtime continues processing other work.

---

# Internal JavaScript Behaviour

Suppose we write

```javascript
async function load() {

    const user = await fetchUser();

    console.log(user);

}
```

Conceptually,

JavaScript performs something similar to:

```text
Call Async Function

↓

Start Promise

↓

Pause Async Function

↓

Return Control

↓

Promise Resolves

↓

Resume Async Function

↓

Continue Execution
```

Notice

The JavaScript engine never blocks the entire program.

Only the async function is suspended.

---

# Execution Timeline

```text
Call Async Function

↓

Create Promise

↓

Begin Execution

↓

Encounter await

↓

Pause Async Function

↓

Continue Other JavaScript

↓

Promise Settles

↓

Resume Async Function

↓

Finish Function

↓

Resolve Returned Promise
```

---

# Example 1 — Beginner

```javascript
function getMessage() {

    return Promise.resolve("Welcome");

}

async function displayMessage() {

    const message = await getMessage();

    console.log(message);

}

displayMessage();
```

Output

```text
Welcome
```

Explanation

1. `getMessage()` returns a Promise.
2. `await` waits for that Promise.
3. The resolved value is assigned to `message`.
4. The value is printed.

---

# Example 2 — Intermediate

```javascript
function getPrice() {

    return Promise.resolve(500);

}

async function calculateTax() {

    const price = await getPrice();

    const tax = price * 0.16;

    console.log(tax);

}

calculateTax();
```

Output

```text
80
```

Notice how the asynchronous code reads like ordinary synchronous code.

---

# Example 3 — Real-World Application

Imagine a POS application.

```javascript
async function loadDashboard() {

    const user = await fetchUser();

    const transactions = await fetchTransactions(user.id);

    const totals = await calculateTotals(transactions);

    console.log(totals);

}
```

Workflow

```text
Fetch User

↓

Fetch Transactions

↓

Calculate Totals

↓

Display Dashboard
```

Instead of a long Promise chain,

the workflow is expressed as straightforward sequential code.

---

# Common Mistakes

## Mistake 1

Thinking `await` blocks JavaScript.

It does not.

It only pauses the current async function.

---

## Mistake 2

Using `await` outside an async function.

Incorrect

```javascript
const user = await fetchUser();
```

unless the environment supports top-level `await`.

Correct

```javascript
async function load() {

    const user = await fetchUser();

}
```

---

## Mistake 3

Thinking async functions return ordinary values.

Incorrect

```javascript
const result = greet();
```

`result` is **not** `"Hello"`.

It is a Promise.

To access the value, you must:

```javascript
await greet();
```

or

```javascript
greet().then(...);
```

---

## Mistake 4

Using `await` on code that is already synchronous.

Example

```javascript
await 10;
```

While valid, it provides no practical benefit because the value is already available.

---

# Best Practices

- Use `async` for functions that naturally perform asynchronous work.
- Use `await` with Promise-returning APIs.
- Keep async functions focused on one responsibility.
- Continue using Promises where they are more appropriate (for example, when using `Promise.all()` for parallel work).
- Remember that `async`/`await` improves readability—it does not change the underlying asynchronous model.

---

# Performance Considerations

Using `await` sequentially means each awaited operation starts only after the previous one completes.

Example

```javascript
const user = await fetchUser();
const orders = await fetchOrders();
```

If the two operations are independent, this may be slower than running them in parallel with `Promise.all()`.

We'll explore this optimization in a later lesson.

---

# Interview Questions

### Beginner

**Q:** What does the `async` keyword do?

**Answer:**

It declares an async function that always returns a Promise.

---

### Intermediate

**Q:** Does `await` block the JavaScript thread?

**Answer:**

No.

It pauses only the current async function while allowing the rest of the JavaScript runtime to continue.

---

### Advanced

**Q:** Is `async`/`await` a replacement for Promises?

**Answer:**

No.

It is syntactic sugar built on top of Promises.

Every async function returns a Promise, and every `await` works with a Promise.

---

# Summary

In this lesson you learned:

- Why Async/Await was introduced.
- The problems it solves.
- That Async/Await is built on top of Promises.
- What an async function is.
- That async functions always return Promises.
- What the `await` keyword does.
- How JavaScript pauses and resumes async functions internally.
- Why Async/Await improves readability without changing JavaScript's asynchronous model.

You now understand the conceptual foundation of Async/Await.

---

> **End of Part 1**

## Next Part (Part 2)

We'll study **Async Functions in Depth**, including:

- Returning values from async functions
- Returning Promises
- Throwing errors
- Async function execution lifecycle
- How async functions interact with the Event Loop
- Async arrow functions
- Async methods
- Internal engine transformations
- Professional patterns and common pitfalls

This lesson will deepen your understanding of what the `async` keyword really does under the hood before we move into advanced `await` usage and error handling.
