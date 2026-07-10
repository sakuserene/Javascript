# Module 0: How Loops Actually Work

## Lesson 0.3 — Variables in Memory During Loop Execution

> **Course:** JavaScript Loops Mastery (Beginner → Advanced)

---

# Table of Contents

- [Learning Objectives](#learning-objectives)
- [Prerequisites](#prerequisites)
- [Introduction](#introduction)
- [Mental Model](#mental-model)
- [Theory](#theory)
- [Variables in Memory](#variables-in-memory)
- [How Memory Changes During a Loop](#how-memory-changes-during-a-loop)
- [Variable Scope](#variable-scope)
- [Internal Mechanics](#internal-mechanics)
- [Syntax](#syntax)
- [Example 1](#example-1)
- [Example 2](#example-2)
- [Example 3](#example-3)
- [Visual Walkthrough](#visual-walkthrough)
- [Common Mistakes](#common-mistakes)
- [Performance](#performance)
- [Best Practices](#best-practices)
- [Exercises](#exercises)
- [Mini Project](#mini-project)
- [Challenge](#challenge)
- [Summary](#summary)
- [Key Takeaways](#key-takeaways)
- [Glossary](#glossary)
- [Further Reading](#further-reading)
- [Practice Problems](#practice-problems)
- [Common Interview Questions](#common-interview-questions)
- [References](#references)
- [What's Next](#whats-next)

---

# Learning Objectives

After completing this lesson, you should be able to:

- Explain where variables are stored.
- Explain what happens when variables are updated.
- Understand why loops reuse variables.
- Understand the difference between creating and updating variables.
- Understand loop scope.

---

# Prerequisites

You should already understand:

- Variables
- Assignment
- Increment (`++`)
- The four parts of a loop

---

# Introduction

One of the biggest misconceptions beginners have is this:

> Every iteration creates a new variable.

**It doesn't.**

Most loops repeatedly update **the same variable**.

Understanding this single idea will make loops much easier to understand.

---

# Mental Model

Imagine a whiteboard.

Instead of buying a new whiteboard every minute...

...you erase it and write a new number.

```text
+-----------+
|     0     |
+-----------+

↓

Erase

↓

+-----------+
|     1     |
+-----------+

↓

Erase

↓

+-----------+
|     2     |
+-----------+

↓

Erase

↓

+-----------+
|     3     |
+-----------+
```

The whiteboard is the variable.

The writing is the value.

---

# Theory

Consider:

```js
let count = 0;
```

JavaScript allocates memory.

```text
Memory

+---------------+
| count |   0   |
+---------------+
```

Now:

```js
count = 1;
```

Memory becomes

```text
+---------------+
| count |   1   |
+---------------+
```

Notice:

The variable wasn't recreated.

Only its value changed.

---

# Variables in Memory

Let's write:

```js
let i = 0;
```

Memory:

```text
Address      Variable      Value

0x0012          i            0
```

Now:

```js
i++;
```

Memory:

```text
Address      Variable      Value

0x0012          i            1
```

Notice something.

The address stayed the same.

Only the value changed.

Think of it like a mailbox.

```text
Mailbox #12

Before

0

↓

Replace letter

↓

Mailbox #12

1
```

Same mailbox.

Different contents.

---

# How Memory Changes During a Loop

Example:

```js
for (let i = 0; i < 4; i++) {
    console.log(i);
}
```

Iteration 1

```text
Memory

i → 0
```

Output

```text
0
```

Update

```text
i → 1
```

Iteration 2

```text
Memory

i → 1
```

Output

```text
1
```

Update

```text
i → 2
```

Iteration 3

```text
Memory

i → 2
```

Output

```text
2
```

Update

```text
i → 3
```

Iteration 4

```text
Memory

i → 3
```

Output

```text
3
```

Update

```text
i → 4
```

Condition

```text
4 < 4

↓

false
```

Exit.

The same memory location was reused every time.

---

# Variable Scope

Now look at this.

```js
{
    let x = 10;
}
```

Can we use `x` outside?

```js
console.log(x);
```

No.

Why?

Because `let` is block-scoped.

Memory exists only inside that block.

```text
{

Memory

x → 10

}

↓

Block ends

↓

Memory released
```

---

Now look at a loop.

```js
for (let i = 0; i < 3; i++) {
    console.log(i);
}

console.log(i);
```

This causes an error.

Why?

Because `i` exists only inside the loop.

```text
Loop Starts

↓

Create i

↓

Run loop

↓

Destroy i

↓

Outside

i ❌
```

---

# Internal Mechanics

When JavaScript sees:

```js
for (let i = 0; i < 3; i++)
```

it roughly performs:

```text
Create Variable

↓

Store 0

↓

Evaluate Condition

↓

Run Body

↓

Update Value

↓

Repeat

↓

Destroy Variable
```

Notice:

Destroying the variable happens **after** the loop finishes.

---

# Syntax

```js
for (let i = 0; i < 5; i++) {
    // body
}
```

Memory flow:

```text
Create

↓

Update

↓

Update

↓

Update

↓

Destroy
```

---

# Example 1

```js
let score = 1;

score++;

score++;

console.log(score);
```

Memory:

```text
score → 1

↓

score → 2

↓

score → 3
```

Output

```text
3
```

No new variable was created.

---

# Example 2

```js
for (let i = 1; i <= 3; i++) {
    console.log(i);
}
```

Memory timeline

```text
i → 1

↓

i → 2

↓

i → 3

↓

i → 4

↓

Loop exits
```

Notice:

The last value of `i` is **4**, even though `4` is never printed.

Why?

Because the update happens before the final condition check.

---

# Example 3

```js
let total = 0;

for (let i = 1; i <= 3; i++) {
    total += i;
}
```

Memory

Initially

```text
total → 0

i → 1
```

Iteration 1

```text
total → 1

i → 2
```

Iteration 2

```text
total → 3

i → 3
```

Iteration 3

```text
total → 6

i → 4
```

Exit.

Final memory

```text
total → 6
```

---

# Visual Walkthrough

```text
Create i

↓

i = 0

↓

Body

↓

Update

↓

i = 1

↓

Body

↓

Update

↓

i = 2

↓

Body

↓

Update

↓

i = 3

↓

Condition False

↓

Destroy i
```

---

## Memory Timeline

```text
Iteration

1

+-----------+
| i | 0     |
+-----------+

↓

2

+-----------+
| i | 1     |
+-----------+

↓

3

+-----------+
| i | 2     |
+-----------+

↓

4

+-----------+
| i | 3     |
+-----------+

↓

Destroyed
```

---

# Common Mistakes

| Mistake | Why it Happens | Fix |
|----------|----------------|-----|
| Thinking `i++` creates another variable | It only changes the existing value | Remember variables occupy one memory location |
| Trying to use `let` loop variables outside the loop | `let` is block-scoped | Use the variable only inside its scope |
| Assuming the final value is the last printed value | The update occurs before the final condition check | Trace the update separately from the output |
| Forgetting that multiple variables can change in a loop | Every assignment updates memory | Track each variable independently |

---

# Performance

| Operation | Time | Space |
|-----------|------|-------|
| Update variable | O(1) | O(1) |
| Read variable | O(1) | O(1) |
| Create variable | O(1) | O(1) |
| Destroy variable | O(1) | O(1) |

---

# Best Practices

- Think of variables as memory locations.
- Separate the **variable** from its **value**.
- Trace memory after every iteration.
- Remember that `let` variables disappear after their block ends.
- Never assume the printed value is the final stored value.

> **Tip**
>
> Whenever debugging a loop, draw a memory table. It is one of the fastest ways to find logic errors.

---

# Exercises

## Beginner

- [ ] Draw the memory after each line:

```js
let x = 5;
x++;
x++;
```

- [ ] What is the final value?

---

## Intermediate

Without running the code:

```js
let total = 0;

for (let i = 1; i <= 4; i++) {
    total += i;
}
```

Draw the memory after every iteration.

---

## Advanced

Predict the final values of:

```js
let a = 2;
let b = 10;

a++;
b--;

a += b;
```

Draw the memory after each statement.

---

## Expert

Explain why this throws an error:

```js
for (let i = 0; i < 5; i++) {}

console.log(i);
```

Do not simply say "scope."

Describe exactly what happens to the variable in memory.

---

# Mini Project

Create a memory timeline for:

```js
let sum = 0;

for (let i = 2; i <= 8; i += 2) {
    sum += i;
}
```

Create a table with:

| Iteration | Value of `i` | Value of `sum` |
|-----------|--------------|----------------|

Fill it manually.

---

# Challenge

Without running the code:

```js
let x = 1;

for (let i = 0; i < 4; i++) {
    x = x * 2;
}
```

Answer:

1. What is `x` after every iteration?
2. What is the final value?
3. What is the final value of `i`?
4. Why isn't `i` accessible after the loop?

Explain using memory diagrams.

---

# Summary

Variables do not magically appear and disappear on every iteration. In most loops, JavaScript creates the loop variable once, repeatedly updates its value, and finally removes it when the loop's scope ends.

By visualizing variables as named memory locations, you can better understand assignments, updates, loop execution, and why scope rules exist.

---

# Key Takeaways

- A variable is a memory location that stores a value.
- Updating a variable changes its value, not its identity.
- Most loops reuse the same loop variable.
- `let` creates block-scoped variables.
- Loop variables declared with `let` are destroyed when the loop ends.
- The final stored value of a loop variable may differ from the last value printed.

---

# Glossary

| Term | Definition |
|------|------------|
| Memory Location | A place where a value is stored. |
| Assignment | Replacing the current value of a variable. |
| Scope | The region of code where a variable can be accessed. |
| Block Scope | Scope limited to code inside `{}`. |
| Loop Variable | The variable that controls loop execution. |

---

# Further Reading

- JavaScript Variables
- Block Scope (`let` and `const`)
- Memory Model
- Variable Lifetime
- Lexical Environments (preview)

---

# Practice Problems

- [ ] Draw memory changes for three different loops.
- [ ] Explain why variables are updated rather than recreated.
- [ ] Predict final variable values without executing code.
- [ ] Practice tracing multiple variables simultaneously.

---

# Common Interview Questions

1. What happens in memory when a variable is incremented?
2. Does `i++` create a new variable?
3. Why can't a `let` loop variable be accessed outside the loop?
4. What is block scope?
5. What is the difference between a variable and its value?

---

# References

- ECMAScript Language Specification
- MDN Web Docs
- JavaScript.info
- Microsoft Learn
- Node.js Documentation

---

# What's Next

**Module 0 — Lesson 0.4: Condition Evaluation and Boolean Logic**

In the next lesson, you'll learn exactly how JavaScript evaluates loop conditions. We'll explore truthy and falsy values, comparison operators, logical operators, and short-circuit evaluation. Understanding these concepts will help you predict precisely when a loop continues or stops.

> **Stop here.** Complete the exercises and challenge before continuing. When you're ready, type **`Next`** to begin **Lesson 0.4: Condition Evaluation and Boolean Logic**.
