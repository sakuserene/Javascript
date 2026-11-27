# Lesson 08 - Controlling Loops

*JavaScript Plain Loops*

Previous: [Loop Ranges, Boundaries and Step Sizes](07-loop-ranges-boundaries-and-step-sizes.md) | Curriculum: Plain Loops | Next: Variable Scope in Loops (planned)

**Filename:** `08-controlling-loops.md`

## Table of contents

1. [Introduction](#1-introduction)
2. [Learning objectives](#2-learning-objectives)
3. [Breaking out of a loop](#3-breaking-out-of-a-loop)
4. [Skipping an iteration](#4-skipping-an-iteration)
5. [Labels for break and continue](#5-labels-for-break-and-continue)
6. [Returning from a function inside a loop](#6-returning-from-a-function-inside-a-loop)
7. [Throwing exceptions inside loops](#7-throwing-exceptions-inside-loops)
8. [Control flow and finally](#8-control-flow-and-finally)
9. [Combining loop control statements](#9-combining-loop-control-statements)
10. [Common mistakes and edge cases](#10-common-mistakes-and-edge-cases)
11. [Best practices](#11-best-practices)
12. [Key takeaways](#12-key-takeaways)
13. [Practice exercises](#13-practice-exercises)

## 1. Introduction

In previous lessons, we learned how loop conditions and update expressions control repetition.

However, not every decision about repetition can be conveniently expressed in a loop header. Sometimes a program must examine data or perform calculations inside the body before deciding what should happen next.

For example, a program may need to stop searching immediately after finding a matching record, skip invalid values, exit several nested loops, return a calculated result or report an unexpected error.

JavaScript provides several statements that allow programmers to change the normal execution flow:

| Statement | Purpose |
|---|---|
| `break` | Exit a loop or another valid break target |
| `continue` | Skip the remaining work in the current iteration |
| `return` | Exit the current function |
| `throw` | Raise an exception and transfer control to an applicable handler |

These statements are collectively associated with control transfer. Unlike ordinary statements, they can interrupt the normal sequential execution of a loop body.

Understanding their behavior is essential for implementing efficient searches, filtering data, validating inputs and solving more complex programming problems.

This lesson covers the complete Controlling Loops curriculum branch. Run each example independently.

## 2. Learning objectives

- Exit loops early and skip selected iterations.
- Trace control transfers in all three traditional loop types.
- Control nested loops using labels.
- Distinguish loop exits, function returns and exceptions.
- Explain how finally affects pending control transfers.
- Combine control statements without introducing termination errors.

## 3. Breaking out of a loop

### 3.1 The break statement

An unlabeled `break` exits the nearest enclosing loop or switch statement. Execution normally resumes after that statement.

```javascript
for (let i = 1; i <= 5; i++) {
  if (i === 3) {
    break;
  }
  console.log(i);
}
console.log("Done");
```

Output:

```text
1
2
Done
```

| i | Action | Header update |
|---:|---|---|
| 1 | Print 1 | Runs |
| 2 | Print 2 | Runs |
| 3 | Break | Skipped |

The body is entered three times, but only two values are printed. The third iteration ends abruptly; there is no subsequent condition check.

### 3.2 Searching with early termination

```javascript
const values = [8, 4, 9, 2];
let foundIndex = -1;
for (let i = 0; i < values.length; i++) {
  if (values[i] === 9) {
    foundIndex = i;
    break;
  }
}
console.log(foundIndex);
```

Output:

```text
2
```

The loop examines only the first three elements. If the target is absent, the result remains -1. Breaking early avoids unnecessary work.

### 3.3 Breaking an unconditional loop

```javascript
let n = 1;
while (true) {
  console.log(n);
  if (n === 3) break;
  n++;
}
```

Output:

```text
1
2
3
```

The exit condition is inside the body. Use this structure when the stopping decision naturally follows work performed there.

## 4. Skipping an iteration

### 4.1 The continue statement

`continue` skips the remaining statements in the current iteration and transfers control to the loop's continuation point.

```javascript
for (let i = 1; i <= 5; i++) {
  if (i % 2 === 0) continue;
  console.log(i);
}
```

Output:

```text
1
3
5
```

| i | Action | Update |
|---:|---|---|
| 1 | Print | Runs |
| 2 | Continue | Runs |
| 3 | Print | Runs |
| 4 | Continue | Runs |
| 5 | Print | Runs |

Unlike break, continue does not terminate the loop.

### 4.2 Continuation points

| Loop | Where continue transfers control |
|---|---|
| for | Update expression, then condition |
| while | Condition |
| do...while | Condition at the end |

```javascript
let i = 0;
while (i < 5) {
  i++;
  if (i === 3) continue;
  console.log(i);
}
```

Output:

```text
1
2
4
5
```

Updating before continue ensures progress. Moving the update below continue would require redesigning the counter and could trap the loop at 3.

```javascript
let i = 0;
do {
  i++;
  if (i === 2) continue;
  console.log(i);
} while (i < 3);
```

Output:

```text
1
3
```

The condition is checked even when the second iteration executes continue.

## 5. Labels for break and continue

A label is an identifier followed by a colon. It names a statement so that a control transfer can target it explicitly.

### 5.1 Breaking out of nested loops

```javascript
outer: for (let row = 1; row <= 3; row++) {
  for (let col = 1; col <= 3; col++) {
    if (row === 2 && col === 2) break outer;
    console.log(row, col);
  }
}
```

Output:

```text
1 1
1 2
1 3
2 1
```

At row 2, column 2, break outer exits both loops. An unlabeled break would exit only the inner loop, allowing the outer loop to advance to row 3.

### 5.2 Continuing an outer loop

```javascript
outer: for (let row = 1; row <= 3; row++) {
  for (let col = 1; col <= 3; col++) {
    if (col === 2) continue outer;
    console.log(row, col);
  }
}
```

Output:

```text
1 1
2 1
3 1
```

Each time column 2 is reached, the remaining inner and outer body work is skipped. The outer for update executes, advancing row.

### 5.3 Valid label targets

A labeled break can exit an enclosing labeled statement, including a block. A labeled continue must target an enclosing iteration statement. Labels cannot transfer execution into unrelated statements or across function boundaries.

## 6. Returning from a function inside a loop

`return` exits the entire current function, optionally supplying a result. It does not merely exit the loop.

```javascript
function findIndex(values, target) {
  for (let i = 0; i < values.length; i++) {
    if (values[i] === target) return i;
  }
  return -1;
}
console.log(findIndex([4, 7, 9], 7));
console.log(findIndex([4, 7, 9], 2));
```

Output:

```text
1
-1
```

The first call returns immediately when index 1 matches. The second reaches the fallback return after the loop. Use break when the function must perform additional work after the loop; use return when the function's result is ready. A return statement is not valid at the top level of an ordinary script or module.

## 7. Throwing exceptions inside loops

`throw` signals an exceptional condition. Execution transfers to the nearest applicable catch handler, potentially unwinding function calls. Prefer throwing Error objects with useful messages.

```javascript
try {
  const prices = [10, -5, 20];
  for (let i = 0; i < prices.length; i++) {
    if (prices[i] < 0) throw new Error("Negative price");
    console.log(prices[i]);
  }
} catch (error) {
  console.log(error.message);
}
```

Output:

```text
10
Negative price
```

The exception exits the loop because the handler is outside it. If the catch is inside the body, processing can continue after handling the error. Exceptions are not a substitute for ordinary filtering or expected loop termination.

## 8. Control flow and finally

A finally block executes when control leaves its associated try or catch, including through break, continue, return or throw. If finally completes normally, the pending transfer resumes.

```javascript
for (let i = 1; i <= 3; i++) {
  try {
    if (i === 2) break;
    console.log("Work", i);
  } finally {
    console.log("Cleanup", i);
  }
}
console.log("Done");
```

Output:

```text
Work 1
Cleanup 1
Cleanup 2
Done
```

The break at i = 2 is pending until cleanup completes. The header update is then skipped.

```javascript
for (let i = 1; i <= 2; i++) {
  try {
    continue;
  } finally {
    console.log("Cleanup", i);
  }
}
```

Output:

```text
Cleanup 1
Cleanup 2
```

Finally runs before the for update on each iteration. A return or throw in finally can override a pending transfer:

```javascript
function example() {
  try {
    return 1;
  } finally {
    return 2;
  }
}
console.log(example());
```

Output:

```text
2
```

Avoid control transfers from finally because they can hide results and exceptions. Use finally primarily for cleanup.

## 9. Combining loop control statements

Suppose a sequence contains invalid negative values and a zero sentinel. Sum valid positive values until the sentinel.

```javascript
const values = [4, -2, 7, 0, 9];
let total = 0;
for (let i = 0; i < values.length; i++) {
  const value = values[i];
  if (value === 0) break;
  if (value < 0) continue;
  total += value;
}
console.log(total);
```

Output:

```text
11
```

| Index | Value | Action | Total |
|---:|---:|---|---:|
| 0 | 4 | Accumulate | 4 |
| 1 | -2 | Continue | 4 |
| 2 | 7 | Accumulate | 11 |
| 3 | 0 | Break | 11 |

The sentinel stops all processing; invalid values merely skip one iteration. Ordering matters: if a broader skip rule includes the sentinel and executes first, the loop may miss its stopping signal.

## 10. Common mistakes and edge cases

- Confusing break with continue: break ends repetition, while continue skips remaining body work.
- Skipping a necessary while update: place progress logic where every continuing path reaches it.
- Assuming break exits all nested loops: use a valid label or restructure the code.
- Forgetting switch: an unlabeled break inside a switch nested in a loop exits the switch, not the loop.
- Returning too early: place fallback returns after a search loop, not inside its first nonmatching iteration.
- Using a newline immediately after return: automatic semicolon insertion can make it return undefined. Keep the expression on the same line or begin a parenthesized expression there. A newline immediately after throw is a syntax error.
- Overriding pending transfers in finally: avoid return, throw, break or continue there when they would mask an earlier result or exception.

## 11. Best practices

- Use early exits when the result is already known.
- Use continue to express clear filtering rules.
- Ensure every continuing path makes progress.
- Prefer descriptive labels and use them sparingly.
- Use return for completed function results and throw for exceptional failures.
- Test first-match, last-match, absent-target and empty-input cases.

## 12. Key takeaways

- Break exits its target; continue advances to the continuation point.
- A for loop normally evaluates its update after continue but not after break.
- Labels allow explicit control of enclosing statements.
- Return exits the current function; throw transfers control to an applicable exception handler.
- Finally executes before a pending transfer completes and can override it.

## 13. Practice exercises

1. Trace a for loop from 1 through 10 that breaks at 6. Count body entries, printed values and updates.
2. Print integers from 1 through 20, skipping multiples of three with continue.
3. Rewrite exercise 2 using while, ensuring progress on every continuing path.
4. Trace a do...while loop that continues on its second iteration. Identify the next condition check.
5. Find the first negative value in an array using break; handle empty arrays and absent matches.
6. Write nested loops that exit both loops when row + col equals 5.
7. Use a labeled continue to skip the remaining columns when a row contains a forbidden value.
8. Write a function that returns the first even value in an array, or undefined if none exists.
9. Validate an array of prices, throwing an Error for negative values. Compare handlers inside and outside the loop.
10. Trace the break and continue examples involving finally.
11. Explain what happens when finally returns a different value from try.
12. Sum positive values until a zero sentinel, skipping negative values. Test a sentinel at the beginning and an absent sentinel.
13. Explain why break inside a switch nested in a loop does not necessarily exit the loop.
14. Design a bounded search that skips invalid records, returns a matching record and throws for malformed input structure.

---

Previous: [Loop Ranges, Boundaries and Step Sizes](07-loop-ranges-boundaries-and-step-sizes.md) | Curriculum: Plain Loops | Next: Variable Scope in Loops (planned)
