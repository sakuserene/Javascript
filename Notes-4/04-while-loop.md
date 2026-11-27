# Lesson 04 - The `while` Loop

*JavaScript Plain Loops*

Previous: The for Loop | Curriculum: Plain Loops | Next: The do...while Loop (planned)

**Filename:** `04-while-loop.md`

## Table of contents

1. [Introduction](#1-introduction)
2. [Learning objectives](#2-learning-objectives)
3. [Syntax and structure](#3-syntax-and-structure)
4. [How the while loop works](#4-how-the-while-loop-works)
5. [Managing iteration state](#5-managing-iteration-state)
6. [Count-controlled while loops](#6-count-controlled-while-loops)
7. [Condition-controlled while loops](#7-condition-controlled-while-loops)
8. [Looping with an unknown iteration count](#8-looping-with-an-unknown-iteration-count)
9. [Sentinel-controlled while loops](#9-sentinel-controlled-while-loops)
10. [Practical applications](#10-practical-applications)
11. [Common mistakes and edge cases](#11-common-mistakes-and-edge-cases)
12. [Best practices](#12-best-practices)
13. [Key takeaways](#13-key-takeaways)
14. [Practice exercises](#14-practice-exercises)

## 1. Introduction

The `while` loop is a JavaScript control-flow statement that repeatedly executes a statement or block while a specified condition remains truthy.

Unlike the `for` loop, which provides dedicated positions for initialization, condition and update expressions, the `while` loop places only the condition in its header.

This makes it particularly suitable for problems where the continuation condition is more important than the number of repetitions.

Consider a program that repeatedly deducts money from an account until there is insufficient balance for another payment.

```javascript
let balance = 100;

while (balance >= 30) {
  balance -= 30;
  console.log(balance);
}
```

Output:

```text
70
40
10
```

Rather than calculating the number of payments beforehand, the program checks whether another payment is possible before each iteration.

Common applications of `while` loops include searching for information, processing digits, consuming available resources, validating data, processing values until a sentinel is encountered and performing calculations until a target is reached.

This lesson covers the complete `while` loop curriculum branch. Run each example independently.

## 2. Learning objectives

- Explain the syntax, condition and body of a `while` loop.
- Trace condition checks, state changes and termination.
- Construct count-controlled, condition-controlled and sentinel-controlled loops.
- Handle unknown iteration counts and empty inputs.
- Update iteration state correctly and diagnose termination errors.

## 3. Syntax and structure

```javascript
while (condition) {
  // Repeated statements
}
```

The condition is a required expression enclosed in parentheses. JavaScript converts its result to Boolean. If truthy, the body executes; otherwise, execution continues after the loop. Initialization and updates are not dedicated header clauses.

```javascript
let i = 1; // Initialization
while (i <= 3) {
  console.log(i); // Repeated work
  i++; // State update
}
```

Output:

```text
1
2
3
```

A single statement is also valid as a body:

```javascript
let n = 3;
while (n > 0) console.log(n--);
```

Output:

```text
3
2
1
```

Prefer braces and separate updates for readability. Unlike `for (;;)` the syntax `while ()` is invalid. Use `while (true)` for intentional unconditional repetition, with an exit mechanism when termination is required.

## 4. How the while loop works

### 4.1 Execution order

1. Evaluate the condition and convert its result to Boolean.
2. Exit if falsy.
3. Execute the body if truthy.
4. Return to the condition check after normal body completion.

```text
Check condition <---------+
      |                   |
      +-- Falsy --> Exit  |
      |                   |
    Truthy                |
      |                   |
 Execute body ------------+
```

### 4.2 Tracing execution

```javascript
let i = 2;
while (i <= 6) {
  console.log(i);
  i += 2;
}
console.log("Final:", i);
```

Output:

```text
2
4
6
Final: 8
```

| Check | i before | Condition | Printed | i after |
|---:|---:|---|---:|---:|
| 1 | 2 | true | 2 | 4 |
| 2 | 4 | true | 4 | 6 |
| 3 | 6 | true | 6 | 8 |
| 4 | 8 | false | None | 8 |

There are three iterations and four condition checks. The final failed check does not execute the body. For normal condition-based termination, a `while` loop checks its condition once more than it enters its body.

### 4.3 Zero iterations

```javascript
let i = 5;
while (i < 3) {
  console.log(i);
  i++;
}
console.log("Done:", i);
```

Output:

```text
Done: 5
```

The initial condition is false, so neither the body nor its update executes. This is why `while` is a pre-test loop.

## 5. Managing iteration state

### 5.1 Updating state explicitly

Unlike a `for` loop, a `while` loop has no automatic update phase. The programmer must arrange any changes necessary for termination.

```javascript
let remaining = 3;
while (remaining > 0) {
  console.log(remaining);
  remaining--;
}
```

Output:

```text
3
2
1
```

### 5.2 Tracking multiple variables

```javascript
let i = 1;
let total = 0;
while (i <= 4) {
  total += i;
  i++;
}
console.log(total);
```

Output:

```text
10
```

| Iteration | i before | Total before | Total after | i after |
|---:|---:|---:|---:|---:|
| 1 | 1 | 0 | 1 | 2 |
| 2 | 2 | 1 | 3 | 3 |
| 3 | 3 | 3 | 6 | 4 |
| 4 | 4 | 6 | 10 | 5 |

The counter controls repetition; the accumulator preserves results. Both are initialized outside the body so their values persist.

### 5.3 Update order

```javascript
let i = 0;
while (i < 3) {
  i++;
  console.log(i);
}
```

Output:

```text
1
2
3
```

Moving `i++` after `console.log(i)` changes the output to 0, 1, 2. Both loops perform three iterations, but they observe different states.

## 6. Count-controlled while loops

A count-controlled loop uses a counter to govern the number of repetitions.

```javascript
let count = 0;
const limit = 3;
while (count < limit) {
  console.log("Processing");
  count++;
}
```

Output:

```text
Processing
Processing
Processing
```

For a nonnegative safe integer limit, this structure repeats exactly `limit` times, assuming normal execution and no other changes to the counter.

Backward counting is also possible:

```javascript
let i = 6;
while (i >= 2) {
  console.log(i);
  i -= 2;
}
```

Output:

```text
6
4
2
```

The comparison must agree with the direction of the update. A `for` loop is often more compact for these tasks, but `while` is equally capable.

## 7. Condition-controlled while loops

The continuation condition can depend on state other than a counter.

```javascript
let value = 1;
let steps = 0;
while (value < 20) {
  value *= 2;
  steps++;
}
console.log(value, steps);
```

Output:

```text
32 5
```

| Iteration | Value before | Value after | Steps |
|---:|---:|---:|---:|
| 1 | 1 | 2 | 1 |
| 2 | 2 | 4 | 2 |
| 3 | 4 | 8 | 3 |
| 4 | 8 | 16 | 4 |
| 5 | 16 | 32 | 5 |

The target is a threshold, not an exact destination. Using `value !== 20` would be incorrect because doubling 1 never reaches 20.

Compound conditions can impose multiple stopping rules:

```javascript
let balance = 100;
let payments = 0;
while (balance >= 20 && payments < 3) {
  balance -= 20;
  payments++;
}
console.log(balance, payments);
```

Output:

```text
40 3
```

The loop stops when either requirement fails, even though sufficient balance remains.

## 8. Looping with an unknown iteration count

A search may stop at a position that is not known before examining the data.

```javascript
const values = [7, 12, 5, 18];
const target = 5;
let index = 0;
while (index < values.length && values[index] !== target) {
  index++;
}
console.log(index < values.length ? index : -1);
```

Output:

```text
2
```

| Check | Index | Current value | Continue? |
|---:|---:|---:|---|
| 1 | 0 | 7 | Yes |
| 2 | 1 | 12 | Yes |
| 3 | 2 | 5 | No |

The loop body advances past nonmatching values. It executes twice, not three times. If the target is absent, the index reaches the array length and the conditional expression prints -1. If the array is empty, the body never executes. The bounds check comes first so the second operand is skipped when the index is out of range.

## 9. Sentinel-controlled while loops

A sentinel is a special value that signals the end of processing. Unlike a search target, it commonly marks the end of a meaningful input sequence.

```javascript
const readings = [12, 8, 15, -1, 20];
let index = 0;
let total = 0;
while (index < readings.length && readings[index] !== -1) {
  total += readings[index];
  index++;
}
console.log(total);
```

Output:

```text
35
```

| Index | Reading | Total after | Action |
|---:|---:|---:|---|
| 0 | 12 | 12 | Process |
| 1 | 8 | 20 | Process |
| 2 | 15 | 35 | Process |
| 3 | -1 | 35 | Stop |

The sentinel is not included. If it is the first value, the result is zero. If absent, the length check stops processing at the end. Choose a sentinel that cannot be confused with valid data.

## 10. Practical applications

### 10.1 Processing digits

```javascript
let number = 472;
let sum = 0;
while (number > 0) {
  const digit = number % 10;
  sum += digit;
  number = Math.floor(number / 10);
}
console.log(sum);
```

Output:

```text
13
```

| Number before | Digit | Sum after | Number after |
|---:|---:|---:|---:|
| 472 | 2 | 2 | 47 |
| 47 | 7 | 9 | 4 |
| 4 | 4 | 13 | 0 |

Remainder extracts the last digit and integer division removes it. This example assumes a nonnegative safe integer. Zero correctly produces a digit sum of zero.

### 10.2 Bounded validation attempts

```javascript
const inputs = [-2, 0, 8];
let index = 0;
let valid = false;
while (index < inputs.length && !valid) {
  valid = inputs[index] > 0;
  index++;
}
console.log("Valid:", valid);
console.log("Attempts:", index);
```

Output:

```text
Valid: true
Attempts: 3
```

The Boolean flag records whether a valid input has been found. The index limits attempts to the available inputs. This is a synchronous simulation, not a real interactive or asynchronous retry system.

## 11. Common mistakes and edge cases

### 11.1 Missing updates

```javascript
// Incorrect: do not run.
let i = 1;
while (i <= 3) {
  console.log(i);
}
```

The condition remains true because `i` never changes. Add `i++` inside the body.

### 11.2 Updating in only one branch

```javascript
// Incorrect: do not run.
let i = 1;
while (i <= 4) {
  if (i % 2 === 0) {
    console.log(i);
    i++;
  }
}
```

At i = 1, the inner condition is false and the update never runs. Move `i++` outside the `if` block but keep it inside the loop.

### 11.3 Accidental empty body

```javascript
let i = 3;
while (i-- > 0); // Empty body
console.log(i);
```

Output:

```text
-1
```

The semicolon is the body. The condition itself decrements the variable, including on the failed check. Avoid this pattern when repeated work is intended.

### 11.4 Truthiness and negative values

`while (n)` does not mean `while (n > 0)`. Negative numbers are truthy. When processing positive values, use an explicit positive comparison.

### 11.5 Exact equality as a stopping rule

```javascript
// Incorrect: do not run.
let i = 0;
while (i !== 5) {
  i += 2;
}
```

The progression skips 5. If the requirement is to stop upon reaching or exceeding 5, use `i < 5` instead.

## 12. Best practices

- Initialize persistent state before the loop.
- Make the continuation condition express the actual requirement.
- Ensure every continuing execution path makes appropriate progress.
- Use bounds checks before reading indexed data.
- Prefer explicit updates over hidden side effects in conditions.
- Test empty inputs, immediate success, missing sentinels and unreachable targets.
- Avoid busy-waiting for external events: synchronous loops can block JavaScript execution.

## 13. Key takeaways

- `while` is a pre-test loop with a required condition expression.
- Its body may execute zero, one or many times.
- It has no dedicated initialization or update clauses.
- It supports count-controlled, condition-controlled and sentinel-controlled iteration.
- Termination depends on the condition and the evolution of iteration state.
- Tracing both successful and failed checks reveals the actual execution behavior.

## 14. Practice exercises

### Understanding and tracing

1. Explain why a `while` loop can execute zero times.
2. Trace the following code, including the failed condition check.

```javascript
let n = 10;
while (n > 0) {
  console.log(n);
  n -= 3;
}
```

3. Predict the output and final values of both variables.

```javascript
let i = 1;
let total = 0;
while (i <= 5) {
  total += i;
  i += 2;
}
console.log(i, total);
```

### Constructing loops

4. Print the integers from 1 through 20 using `while`.
5. Print the even integers from 20 down to 2.
6. Calculate the sum of integers from 1 through 100.
7. Starting at 3, repeatedly triple the value until it reaches or exceeds 500. Report the value and iteration count.
8. Search `[9, 4, 7, 2]` for 7 and return its index, or -1 if absent.
9. Sum `[5, 8, 0, 12]` until the sentinel zero or the end of the array.
10. Calculate the digit sum of 5839 without converting it to a string.
11. Simulate validation of `[-4, -1, 0, 6]` until a positive value is found or all inputs are exhausted.

### Debugging and reasoning

12. Correct a loop starting at 1 with condition `i <= 5` and update `i--`.
13. Explain why a counter initialized to zero and updated by two cannot reach exactly 7. Write an appropriate threshold condition.
14. Explain how the search example behaves with an empty array, a target at index zero and an absent target.
15. Design a loop that deducts 45 from a balance of 200 at most three times, without allowing a negative balance. Trace the state.

---

Previous: The for Loop | Curriculum: Plain Loops | Next: The do...while Loop (planned)
