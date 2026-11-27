# Lesson 03 - The for Loop

Previous: [JavaScript Prerequisites](02-javascript-prerequisites.md) | Curriculum: Plain Loops | Next: The while Loop (planned)

## Table of contents

- [Introduction](#1-introduction)
- [Syntax and structure](#2-syntax-and-structure)
- [Execution and tracing](#3-execution-and-tracing)
- [Optional expressions](#4-optional-expressions)
- [Loop variables](#5-loop-variables)
- [Count-controlled and condition-controlled loops](#6-count-controlled-and-condition-controlled-loops)
- [Modifying loops](#7-modifying-loops)
- [Practical applications](#8-practical-applications)
- [Common mistakes](#9-common-mistakes)
- [Practice](#10-practice)

## 1. Introduction

The for loop organizes initialization, condition and update in one header. It is useful for counters, ranges, array indices and changing state. It is not restricted to predetermined counts.

```javascript
for (let i = 1; i <= 5; i++) {
  console.log(i * i);
}
```

Output:

```text
1
4
9
16
25
```

## 2. Syntax and structure

```javascript
for (initialization; condition; afterthought) {
  // Repeated operations
}
```

Initialization runs once. The condition is converted to Boolean before each body entry. The update runs after normal body completion. Both semicolon separators are required. Braces group statements.

Initialization can declare a variable, assign an existing variable or initialize several variables. The condition can combine requirements. The update can use increment, decrement, arithmetic, assignment or comma-separated expressions.

```javascript
for (let x = 1, y = 10; x <= 3; x++, y += 10) {
  console.log(x, y);
}
```

Output:

```text
1 10
2 20
3 30
```

```javascript
for (let i = 1; i <= 10 && i !== 5; i++) {
  console.log(i);
}
```

Output: 1, 2, 3, 4 (on separate lines). A false condition stops the entire loop, rather than skipping one value.

```javascript
for (let i = 1; i <= 32; i *= 2) {
  console.log(i);
}
```

Output: 1, 2, 4, 8, 16, 32 (on separate lines).

```javascript
for (let i = 1; i <= 20; i = i * 2 + 1) {
  console.log(i);
}
```

Output: 1, 3, 7, 15 (on separate lines). The next value, 31, fails the condition.

Multiple updates execute left to right. For example, `x++, y = x * 2` uses the newly incremented x when assigning y.

## 3. Execution and tracing

```text
Initialize once
      |
      v
Check condition <---------+
      |                   |
      +-- Falsy --> Exit  |
      |                   |
    Truthy                |
      v                   |
Execute body              |
      v                   |
Evaluate update ----------+
```

```javascript
let i;
for (i = 1; i <= 3; i++) {
  console.log(i);
}
console.log("Final:", i);
```

Output:

```text
1
2
3
Final: 4
```

| Check | i | Condition | Output | After update |
|---|---|---|---|---|
| 1 | 1 | true | 1 | 2 |
| 2 | 2 | true | 2 | 3 |
| 3 | 3 | true | 3 | 4 |
| 4 | 4 | false | None | No update |

Three iterations, three updates and four condition checks. A false initial condition causes zero body executions and zero updates. Initialization still runs.

```javascript
let total = 0;
for (let i = 1; i <= 4; i++) {
  total += i;
  console.log(total);
}
```

Output: 1, 3, 6, 10 (on separate lines).

| i | Total before | Total after |
|---|---|---|
| 1 | 0 | 1 |
| 2 | 1 | 3 |
| 3 | 3 | 6 |
| 4 | 6 | 10 |

## 4. Optional expressions

All three clauses are optional; both semicolons remain required.

```javascript
let i = 2;
for (; i <= 5; i++) {
  console.log(i);
}
```

Output: 2, 3, 4, 5 (on separate lines).

```javascript
for (let i = 1; ; i++) {
  console.log(i);
  if (i === 3) {
    break;
  }
}
```

Output: 1, 2, 3 (on separate lines). An omitted condition provides no condition-based exit. Break exits immediately without the next update.

```javascript
for (let i = 1; i <= 10;) {
  console.log(i);
  if (i < 4) {
    i++;
  } else {
    i += 3;
  }
}
```

Output: 1, 2, 3, 4, 7, 10 (on separate lines).

```javascript
let i = 1;
for (;;) {
  console.log(i);
  if (i === 3) {
    break;
  }
  i++;
}
```

Output: 1, 2, 3 (on separate lines).

## 5. Loop variables

A let binding declared in the header is not accessible outside the loop unless a separate outer binding exists. Declare a variable outside when its final value is needed. Multiple variables can progress independently.

```javascript
for (let left = 1, right = 7; left < right; left++, right--) {
  console.log(left, right);
}
```

Output:

```text
1 7
2 6
3 5
```

The next condition compares 4 with 4 and fails.

```javascript
for (let x = 1, y = 1; x <= 4; x++, y *= 2) {
  console.log(x, y);
}
```

Output:

```text
1 1
2 2
3 4
4 8
```

## 6. Count-controlled and condition-controlled loops

`for (let i = 0; i < n; i++)` performs n iterations for a nonnegative safe integer n with exact arithmetic and no early exit or interfering state changes. Zero-based counting visits 0 through n-1; one-based counting can visit 1 through n.

```javascript
const products = ["Keyboard", "Mouse", "Monitor"];
for (let i = 0; i < products.length; i++) {
  console.log(products[i]);
}
```

Output: Keyboard, Mouse, Monitor (on separate lines).

```javascript
let balance = 100;
let payments = 0;
for (; balance >= 30; payments++) {
  balance -= 30;
}
console.log("Payments:", payments);
console.log("Remaining:", balance);
```

Output:

```text
Payments: 3
Remaining: 10
```

```javascript
const values = [8, 4, 6, -1, 9];
for (let i = 0; i < values.length && values[i] !== -1; i++) {
  console.log(values[i]);
}
```

Output: 8, 4, 6 (on separate lines). The sentinel is not processed.

```javascript
let value = 1;
let steps = 0;
for (; value < 100; steps++) {
  value *= 2;
}
console.log("Value:", value);
console.log("Steps:", steps);
```

Output: Value: 128; Steps: 7. The progression is 1, 2, 4, 8, 16, 32, 64, 128.

## 7. Modifying loops

| Header | Visited values |
|---|---|
| `for (let i = 1; i <= 5; i++)` | 1, 2, 3, 4, 5 |
| `for (let i = 3; i <= 5; i++)` | 3, 4, 5 |
| `for (let i = 1; i <= 8; i++)` | 1 through 8 |
| `for (let i = 1; i <= 5; i += 2)` | 1, 3, 5 |
| `for (let i = 5; i >= 1; i--)` | 5, 4, 3, 2, 1 |

Changing direction requires matching initialization, condition and update. Changing the body can transform output without changing the visited values.

## 8. Practical applications

```javascript
const number = 7;
for (let i = 1; i <= 5; i++) {
  console.log(`${number} x ${i} = ${number * i}`);
}
```

Output:

```text
7 x 1 = 7
7 x 2 = 14
7 x 3 = 21
7 x 4 = 28
7 x 5 = 35
```

```javascript
const number = 5;
let factorial = 1;
for (let i = 1; i <= number; i++) {
  factorial *= i;
}
console.log(factorial);
```

Output: 120. The accumulator starts at 1, the multiplicative identity. Zero factorial is 1.

| i | Before | After |
|---|---|---|
| 1 | 1 | 1 |
| 2 | 1 | 2 |
| 3 | 2 | 6 |
| 4 | 6 | 24 |
| 5 | 24 | 120 |

```javascript
const prices = [120, 250, 80, 50];
let total = 0;
for (let i = 0; i < prices.length; i++) {
  total += prices[i];
}
console.log("Total:", total);
```

Output: Total: 500. Running totals: 120, 370, 450, 500.

```javascript
const names = ["Alice", "Brian", "Carol"];
for (let i = names.length - 1; i >= 0; i--) {
  console.log(names[i]);
}
```

Output: Carol, Brian, Alice (on separate lines).

## 9. Common mistakes

- Off-by-one: use `i <= 5` rather than `i < 5` when starting at 1 and including 5.
- Incorrect direction: `for (let i = 1; i <= 5; i--)` does not progress toward termination. Do not run it.
- Empty body: a semicolon immediately after the header creates an empty statement.
- Out-of-bounds access: use `i < values.length`, not `i <= values.length`.
- Constant counter: `for (const i = 0; i < 3; i++)` enters the body once and then throws TypeError on update.
- Double update: incrementing i inside the body and header visits every second value.
- Empty arrays: ordinary forward traversal executes zero times, which is correct.

Prefer let for changing counters, braces for bodies, predictable updates, clear names and explicit conditions. Test empty inputs and boundaries.

## 10. Practice

1. Trace `for (let i = 2; i <= 12; i += 2) { console.log(i * 3); }`.
2. Trace `for (let x = 1, y = 10; x <= 4; x++, y -= 2) { console.log(x, y); }`.
3. Starting at 2, double i while i is less than 20. Predict the output and final value.
4. Explain why a loop starting at 8 with condition `i < 3` executes zero times.
5. Print 1 through 20.
6. Print 20 down to 1.
7. Print every third integer from 3 through 30.
8. Print powers of two from 1 through 128.
9. Sum integers from 1 through 100.
10. Calculate the product of integers from 1 through 6.
11. Print the multiplication table of 9 through multiplier 12.
12. Print 1 through 5 without an initialization clause.
13. Print 1 through 5 without an update clause.
14. Print 1 through 5 using `for (;;)` and break.
15. Generate pairs (1, 10), (2, 8), (3, 6), (4, 4), (5, 2).
16. Sum prices [150, 80, 200, 70, 100].
17. Process [5, 12, 8, 0, 17] until zero.
18. Starting at 500, deduct 75 while sufficient balance remains. Count deductions.
19. Reverse-traverse ["A", "B", "C", "D", "E"] without reverse().
20. Correct `for (let i = 10; i >= 1; i++)` without running it.
21. Correct a traversal condition `i <= numbers.length`.
22. Correct a loop that increments its counter both in the body and header.
23. Explain why a const counter fails when incremented.

Previous: [JavaScript Prerequisites](02-javascript-prerequisites.md) | Curriculum: Plain Loops | Next: The while Loop (planned)
