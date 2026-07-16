---
title: "JavaScript Mastery Course"
module: "Async/Await"
part: 4
topic: "Error Handling with Async/Await"
author: "OpenAI"
version: "1.0"
prerequisites:
  - Promises
  - Promise Error Handling
  - Async Functions
  - await Keyword
navigation:
  previous_topic: "Async Functions - await in Depth"
  previous_subtopic: "Sequential vs Parallel Execution"
  current_topic: "Async/Await"
  current_subtopic: "Error Handling with Async/Await"
  next_subtopic: "Sequential vs Parallel Async Patterns"
  next_topic: "DOM"
---

# Error Handling with Async/Await

> "Professional software is not software that never fails.
>
> Professional software is software that knows **how to fail correctly**."

---

# Table of Contents

1. Why Error Handling Exists
2. What Happens When await Fails
3. Promise Rejection vs JavaScript Exception
4. try...catch
5. finally
6. Throwing Custom Errors
7. Error Propagation
8. Nested Error Handling
9. Multiple Await Statements
10. Production Error Handling Patterns
11. Retry Pattern
12. Three Progressive Examples
13. Common Mistakes
14. Best Practices
15. Performance Considerations
16. Interview Questions
17. Exercises
18. Summary

---

# Why Error Handling Exists

Let's revisit a familiar example.

```
User Opens POS

↓

Login Request

↓

Download Profile

↓

Download Transactions

↓

Calculate Totals

↓

Display Dashboard
```

Everything looks perfect.

But reality is different.

Any one of these steps can fail.

```
Internet Lost

↓

Server Offline

↓

Authentication Failed

↓

Database Error

↓

Timeout

↓

Permission Denied

↓

Malformed JSON
```

Applications must be prepared for these situations.

Ignoring failures leads to:

- Crashes
- Frozen interfaces
- Data corruption
- Lost transactions
- Poor user experience

Error handling exists to make applications resilient.

---

# What Happens When `await` Fails?

Suppose we write

```javascript
const user = await fetchUser();
```

If `fetchUser()` resolves,

execution continues.

```
fetchUser()

↓

Resolved

↓

Continue
```

But if it rejects,

execution changes dramatically.

```
fetchUser()

↓

Rejected

↓

Throw Error

↓

Current async function stops

↓

Error propagates
```

This is one of the biggest mental shifts.

---

# await Converts Promise Rejections into Exceptions

Recall Promise syntax.

```javascript
fetchUser()

.catch(error => {

    console.log(error);

});
```

With Async/Await

```javascript
const user = await fetchUser();
```

If the Promise rejects,

JavaScript behaves conceptually like

```javascript
throw error;
```

inside the async function.

This means:

Promise rejection

↓

becomes

↓

JavaScript exception

---

# Visual Diagram

```
Promise

↓

Rejected

↓

await

↓

throw Error

↓

try...catch
```

This is why Async/Await works naturally with JavaScript's existing error handling syntax.

---

# What is try?

The `try` block contains code that **might throw an error**.

General syntax

```javascript
try {

    // risky code

}
```

JavaScript executes everything inside the block.

If nothing fails,

execution continues normally.

---

# What is catch?

The `catch` block runs **only if an error occurs** inside the associated `try` block.

General syntax

```javascript
try {

    // risky code

}

catch(error) {

    // handle error

}
```

The `error` parameter contains the thrown value (usually an `Error` object).

---

# Mental Model

Imagine a safety net.

```
Risky Code

↓

Success?

↓

Yes

↓

Continue

------------------

No

↓

Jump to catch

↓

Recover
```

---

# First Example

```javascript
async function login() {

    try {

        const user = await Promise.reject(

            new Error("Invalid Password")

        );

        console.log(user);

    }

    catch(error) {

        console.log(error.message);

    }

}

login();
```

Output

```text
Invalid Password
```

Notice

Execution never reaches

```javascript
console.log(user);
```

because the Promise rejected.

---

# Internal JavaScript Behaviour

Conceptually

```
await Promise

↓

Rejected?

↓

Yes

↓

Throw Error

↓

Nearest catch

↓

Handle

↓

Continue
```

---

# Successful Execution

```javascript
async function demo() {

    try {

        const value = await Promise.resolve(100);

        console.log(value);

    }

    catch(error) {

        console.log(error);

    }

}
```

Output

```text
100
```

Since no error occurred,

the `catch` block is skipped.

---

# What is finally?

Sometimes code must run

whether success or failure occurs.

Examples

- Close database connection
- Hide loading spinner
- Unlock file
- Release memory
- Stop animation

For this,

JavaScript provides

```javascript
finally
```

General syntax

```javascript
try {

}

catch(error) {

}

finally {

}
```

---

# Execution Flow

```
try

↓

Success?

↓

Yes

↓

finally

----------------

No

↓

catch

↓

finally
```

Notice

`finally` always executes.

---

# Example

```javascript
async function load() {

    try {

        await Promise.resolve();

        console.log("Loaded");

    }

    finally {

        console.log("Cleanup");

    }

}

load();
```

Output

```text
Loaded

Cleanup
```

---

# Throwing Errors Yourself

You are not limited to Promise rejections.

You can throw your own errors.

```javascript
async function withdraw(amount) {

    if (amount <= 0) {

        throw new Error(

            "Invalid Amount"

        );

    }

    return "Success";

}
```

Usage

```javascript
withdraw(-10)

.catch(error => {

    console.log(error.message);

});
```

Output

```text
Invalid Amount
```

---

# Why Throw Errors?

Imagine

```javascript
async function login(username) {

    if (!username) {

        throw new Error(

            "Username Required"

        );

    }

}
```

Instead of silently failing,

the function immediately communicates the problem to its caller.

This makes debugging much easier.

---

# Error Propagation

Suppose

```javascript
async function A() {

    await B();

}

async function B() {

    throw new Error(

        "Database Error"

    );

}
```

Execution

```
B()

↓

Error

↓

A()

↓

Error

↓

Caller

↓

catch
```

Errors travel upward until handled.

---

# Nested Error Handling

Sometimes each layer handles different responsibilities.

```javascript
async function upload() {

    try {

        await save();

    }

    catch(error) {

        console.log(

            "Logging Error"

        );

        throw error;

    }

}
```

Notice

The function logs the error,

then rethrows it.

The caller can still respond appropriately.

---

# Multiple Await Statements

```javascript
async function dashboard() {

    try {

        const user = await fetchUser();

        const orders = await fetchOrders();

        const totals = await calculateTotals();

        console.log(totals);

    }

    catch(error) {

        console.log(

            error.message

        );

    }

}
```

If **any** awaited Promise rejects,

execution jumps immediately to `catch`.

Remaining statements are skipped.

---

# Production Pattern

Professional applications often separate:

- Logging
- Recovery
- User notification

Example

```javascript
try {

    await upload();

}

catch(error) {

    logError(error);

    showNotification(

        "Upload Failed"

    );

    cacheOffline();

}
```

Each responsibility has its own function.

This keeps the `catch` block concise and maintainable.

---

# Retry Pattern

Some failures are temporary.

Example

```javascript
async function retryUpload() {

    try {

        return await upload();

    }

    catch(error) {

        console.log(

            "Retrying..."

        );

        return await upload();

    }

}
```

Real applications usually add:

- Retry limits
- Delays
- Exponential backoff

We'll study those in advanced modules.

---

# Example 1 (Beginner)

```javascript
async function demo() {

    try {

        const number = await Promise.resolve(50);

        console.log(number);

    }

    catch(error) {

        console.log(error);

    }

}

demo();
```

Output

```text
50
```

---

# Example 2 (Intermediate)

```javascript
async function login() {

    try {

        await Promise.reject(

            new Error("Login Failed")

        );

    }

    catch(error) {

        console.log(error.message);

    }

}

login();
```

Output

```text
Login Failed
```

---

# Example 3 (Real-World)

```javascript
async function syncTransactions() {

    try {

        await uploadTransactions();

        await uploadReceipts();

        console.log(

            "Sync Complete"

        );

    }

    catch(error) {

        console.log(

            "Offline Mode Enabled"

        );

    }

    finally {

        hideLoadingSpinner();

    }

}
```

Workflow

```
Upload Transactions

↓

Upload Receipts

↓

Success?

↓

Yes

↓

Complete

↓

Hide Spinner

----------------

Failure

↓

Offline Mode

↓

Hide Spinner
```

---

# Common Mistakes

## Mistake 1

Ignoring rejected Promises.

Always handle failures.

---

## Mistake 2

Using empty catch blocks.

```javascript
catch(error) {

}
```

Never ignore errors silently.

---

## Mistake 3

Throwing strings.

Incorrect

```javascript
throw "Error";
```

Correct

```javascript
throw new Error(

    "Error"

);
```

---

## Mistake 4

Putting unrelated code inside one large try block.

Smaller, focused try blocks are often easier to understand and debug.

---

# Best Practices

- Catch only errors you can meaningfully handle.
- Use `finally` for cleanup, not business logic.
- Throw `Error` objects instead of strings.
- Keep try blocks focused on the operations that can fail.
- Log errors before discarding or rethrowing them.

---

# Performance Considerations

- `try...catch` has minimal performance overhead in modern JavaScript engines.
- The cost of proper error handling is negligible compared to the cost of production failures.
- Avoid retrying indefinitely; uncontrolled retries can overload servers and degrade user experience.

---

# Interview Questions

### Beginner

**Q:** What happens when an awaited Promise rejects?

**Answer:**

The rejection is converted into a JavaScript exception inside the async function, allowing it to be handled with `try...catch`.

---

### Intermediate

**Q:** Does `finally` execute if no error occurs?

**Answer:**

Yes. `finally` executes whether the `try` block succeeds or an error is caught.

---

### Advanced

**Q:** Why should you rethrow an error after logging it?

**Answer:**

Because logging records the problem locally, while rethrowing allows higher-level code to decide how to recover or notify the user. This keeps responsibilities separated.

---

# Exercises

## Beginner (10)

1. Create an async function that returns a fulfilled Promise and handle it with `try...catch`.
2. Await a rejected Promise and print the error message.
3. Add a `finally` block that prints `"Finished"`.
4. Throw a custom `Error` inside an async function.
5. Catch the error and print its `name`.
6. Catch the error and print its `message`.
7. Write an async function that validates a positive number.
8. Throw an error for negative input.
9. Recover from an error by returning a default value.
10. Explain, in your own words, what happens when `await` encounters a rejected Promise.

## Intermediate (10)

1. Simulate a login flow with success and failure cases.
2. Log errors and rethrow them.
3. Build an async function with three awaited operations and one `try...catch`.
4. Return fallback data when a Promise rejects.
5. Create a custom `ValidationError` class and throw it.
6. Differentiate between validation and network errors.
7. Use nested `try...catch` blocks.
8. Refactor Promise `.catch()` code into `async`/`await`.
9. Implement a simple retry mechanism.
10. Display a loading message before an async task and hide it in `finally`.

## Advanced (10)

1. Implement a retry function with a maximum retry count.
2. Add a delay between retries.
3. Build an offline-first synchronization strategy.
4. Design an error hierarchy using custom error classes.
5. Create a reusable `handleApiError()` helper.
6. Build a logging wrapper around async functions.
7. Recover from partial failures while preserving successful results.
8. Chain multiple async functions while preserving error context.
9. Convert a callback-based API to Async/Await using Promises.
10. Build a transaction processor with robust error recovery.

## Debugging Exercises (5)

1. Fix an async function that forgets to `await` a Promise.
2. Correct a `catch` block that silently ignores errors.
3. Find and fix a `finally` block that contains business logic.
4. Identify why an error never reaches the outer `catch`.
5. Debug an async function that retries forever.

## Code Reading Exercises (5)

Read and explain:

1. A function that throws inside `try`.
2. A function that rethrows after logging.
3. A nested async workflow with two `catch` blocks.
4. A retry implementation.
5. A function using `try`, `catch`, and `finally`.

## Mini Projects (3)

1. **Async Login Simulator**
2. **Offline File Synchronizer**
3. **Transaction Upload Manager with Retry and Recovery**

## Capstone Project

Build a **POS Synchronization Engine** that:

- Uploads pending transactions.
- Retries temporary failures.
- Falls back to offline storage.
- Logs all failures.
- Uses custom error classes.
- Displays loading indicators.
- Cleans up resources in `finally`.
- Produces a synchronization report.

---

# Summary

In this lesson you learned:

- Why Async/Await integrates naturally with JavaScript's exception system.
- How `await` converts Promise rejections into exceptions.
- How `try`, `catch`, and `finally` work with async functions.
- How to throw and rethrow errors.
- How errors propagate through async function calls.
- Production patterns for logging, recovery, and retries.
- Best practices for building resilient asynchronous applications.

You now have the knowledge to write robust, production-ready Async/Await code that handles both success and failure gracefully.

---

## End of Part 4

### Next Part (Part 5)

We'll explore **Sequential vs Parallel Async Patterns**, including:

- When to use sequential execution.
- When to use parallel execution.
- Combining `await` with `Promise.all()`.
- Performance optimization techniques.
- Avoiding unnecessary waiting.
- Real-world API request orchestration.
- Concurrency vs parallelism in JavaScript.
- Professional async workflow design.

This lesson will teach you how to write **fast**, **efficient**, and **scalable** asynchronous JavaScript.
