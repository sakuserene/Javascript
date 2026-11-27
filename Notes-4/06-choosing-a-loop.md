# Lesson 06 - Choosing a Loop

*JavaScript Plain Loops*

Previous: [The do...while Loop](05-do-while-loop.md) | Curriculum: Plain Loops | Next: Loop Ranges, Boundaries and Step Sizes (planned)

**Filename:** `06-choosing-a-loop.md`

## Table of contents

1. [Introduction](#1-introduction)
2. [Learning objectives](#2-learning-objectives)
3. [Comparing loop statements](#3-comparing-loop-statements)
4. [Choosing between for and while](#4-choosing-between-for-and-while)
5. [Choosing between while and do...while](#5-choosing-between-while-and-dowhile)
6. [Known and unknown iteration counts](#6-known-and-unknown-iteration-counts)
7. [Initialization and update placement](#7-initialization-and-update-placement)
8. [Readability and maintainability](#8-readability-and-maintainability)
9. [A loop selection strategy](#9-a-loop-selection-strategy)
10. [Practical problem-solving](#10-practical-problem-solving)
11. [Common misconceptions](#11-common-misconceptions)
12. [Key takeaways](#12-key-takeaways)
13. [Practice exercises](#13-practice-exercises)

## 1. Introduction

JavaScript provides three traditional loop statements: `for`, `while` and `do...while`.

In the previous lessons, we studied their syntax, execution behavior, conditions and state updates individually. However, knowing how to write each loop is different from knowing which loop to choose when solving a problem.

In many situations, the same problem can be solved using any of the three loop statements. Nevertheless, one implementation may communicate the programmer's intention more clearly than another.

Choosing an appropriate loop involves considering:

- What controls repetition.
- Whether the number of iterations is known beforehand.
- Whether the operation may execute zero times.
- Whether an initial operation must occur before checking a condition.
- Where initialization and updates naturally belong.
- How easily another developer can understand and maintain the implementation.

The objective is not simply to produce working code. It is to express the repetition requirements accurately, clearly and safely.

This lesson covers the complete Choosing a Loop curriculum branch. Run each example independently.

## 2. Learning objectives

- Compare the execution semantics of the three loop statements.
- Choose a loop based on repetition requirements and initial conditions.
- Distinguish definite and indefinite iteration from loop syntax.
- Evaluate initialization, update placement and readability.
- Implement equivalent solutions and justify the preferred implementation.

## 3. Comparing loop statements

| Property | for | while | do...while |
|---|---|---|---|
| Condition check | Before body | Before body | After body |
| Minimum body entries | 0 | 0 | 1 |
| Dedicated initialization clause | Yes | No | No |
| Dedicated update clause | Yes | No | No |
| Typical use | Regular progression | State-dependent repetition | Mandatory initial operation |

These typical uses are conventions, not restrictions. The minimum body entries assume execution reaches the loop normally and its initial evaluation does not throw.

## 4. Choosing between for and while

### 4.1 Regular counter progression

Print the integers from 1 through 4 using `for`:

```javascript
for (let i = 1; i <= 4; i++) {
  console.log(i);
}
```

Using `while`:

```javascript
let i = 1;
while (i <= 4) {
  console.log(i);
  i++;
}
```

Output of both:

```text
1
2
3
4
```

| Check | i | Condition | Printed | Next i |
|---:|---:|---|---:|---:|
| 1 | 1 | true | 1 | 2 |
| 2 | 2 | true | 2 | 3 |
| 3 | 3 | true | 3 | 4 |
| 4 | 4 | true | 4 | 5 |
| 5 | 5 | false | None | 5 |

The `for` version groups the counter's lifecycle in one header and limits its scope. Prefer it when the initialization, condition and update form a natural unit.

### 4.2 State-dependent repetition

```javascript
let balance = 95;
while (balance >= 30) {
  balance -= 30;
  console.log(balance);
}
```

An equivalent `for` version is:

```javascript
let balance = 95;
for (; balance >= 30;) {
  balance -= 30;
  console.log(balance);
}
```

Output of both:

```text
65
35
5
```

The balance is both the data being processed and the state controlling termination. The `while` version communicates the continuation rule without unused header clauses.

## 5. Choosing between while and do...while

### 5.1 When zero executions are valid

```javascript
let stock = 0;
while (stock > 0) {
  stock--;
  console.log("Sold one item");
}
console.log(stock);
```

Output:

```text
0
```

Replacing this with an unguarded `do...while` would sell an unavailable item and produce a stock value of -1. Choose a pre-test loop when a condition must authorize the first operation.

### 5.2 When the first operation is required

Count the decimal digits of a nonnegative safe integer, including zero:

```javascript
let number = 0;
let digits = 0;
do {
  number = Math.floor(number / 10);
  digits++;
} while (number > 0);
console.log(digits);
```

Output:

```text
1
```

Zero has one decimal digit. A pre-test loop with the same condition would count zero digits unless additional logic handled that case. Here the mandatory first operation naturally expresses the requirement.

## 6. Known and unknown iteration counts

A known count may be calculated at runtime. For example, `items.length` is known before traversal even if it was not known when the program was written.

```javascript
const items = ["A", "B", "C"];
for (let i = 0; i < items.length; i++) {
  console.log(items[i]);
}
```

Output:

```text
A
B
C
```

An unknown count depends on information discovered during repetition:

```javascript
const items = [8, 3, 7, 2];
let index = 0;
while (index < items.length && items[index] !== 7) {
  index++;
}
console.log(index < items.length ? index : -1);
```

Output:

```text
2
```

The search has a known maximum count but an unknown actual stopping position before examining the data. A `for` loop with the same condition is also valid. Known versus unknown count is a useful heuristic, not a mandatory syntax rule.

## 7. Initialization and update placement

### 7.1 Keeping related state together

```javascript
for (let left = 1, right = 5; left < right; left++, right--) {
  console.log(left, right);
}
```

Output:

```text
1 5
2 4
```

Both variables have predictable updates and are needed only inside the loop. A `for` header groups these responsibilities.

### 7.2 Updates that depend on the body

```javascript
let position = 0;
while (position < 7) {
  console.log(position);
  if (position < 3) {
    position++;
  } else {
    position += 2;
  }
}
```

Output:

```text
0
1
2
3
5
```

The update depends on the current state. Keeping the branching logic in the body makes the progression explicit. A `for` loop with an omitted update is possible, but `while` avoids an unnecessary clause.

### 7.3 State needed after termination

```javascript
let index = 0;
const values = [2, 4, 9];
for (; index < values.length && values[index] < 5; index++) {
  // Advance past small values.
}
console.log(index);
```

Output:

```text
2
```

Declaring `index` outside the loop preserves access to its final value. A header-local `let` would not be accessible afterward. Scope requirements can influence initialization placement independently of the loop type.

## 8. Readability and maintainability

A compact implementation is not necessarily clearer.

```javascript
let i = 0;
while (i++ < 3) {
  console.log(i);
}
```

Output:

```text
1
2
3
```

The failed condition also increments `i`, leaving it at 4. A clearer equivalent for the printed sequence is:

```javascript
for (let i = 1; i <= 3; i++) {
  console.log(i);
}
```

The second version makes the visited range explicit. It is equivalent in output, not in external variable scope or final state. When changing loop structures, preserve every behavior the surrounding program depends on.

## 9. A loop selection strategy

```text
Must the body run before its condition is checked?
  Yes: consider do...while.
  No: use a pre-test loop.
      Is there a natural initialization, condition and update?
        Yes: consider for.
        No: consider while.
```

Before implementing, identify the starting state, continuation rule, repeated operation, progress mechanism and required final state. Check empty inputs and immediate termination. If several loop types are correct, choose the one that expresses these requirements most clearly.

## 10. Practical problem-solving

### 10.1 Processing every product

```javascript
const prices = [100, 50, 75];
let total = 0;
for (let i = 0; i < prices.length; i++) {
  total += prices[i];
}
console.log(total);
```

Output:

```text
225
```

A regular index progression and known collection length favor `for`. An empty array correctly leaves the total at zero.

### 10.2 Consuming resources until insufficient

```javascript
let fuel = 23;
let trips = 0;
while (fuel >= 5) {
  fuel -= 5;
  trips++;
}
console.log(trips, fuel);
```

Output:

```text
4 3
```

| Iteration | Fuel before | Fuel after | Trips |
|---:|---:|---:|---:|
| 1 | 23 | 18 | 1 |
| 2 | 18 | 13 | 2 |
| 3 | 13 | 8 | 3 |
| 4 | 8 | 3 | 4 |

The resource condition must be checked before each trip. `while` emphasizes that requirement.

### 10.3 Attempting an operation before checking success

```javascript
let attempts = 0;
let success;
do {
  attempts++;
  success = attempts === 2; // Simulated result
} while (!success && attempts < 3);
console.log(success, attempts);
```

Output:

```text
true 2
```

The initial attempt is required and the result is known only afterward. A bounded `do...while` expresses that sequence naturally. This is a synchronous simulation, not a real network retry.

## 11. Common misconceptions

- A `for` loop is not restricted to known iteration counts.
- A `while` loop can perform an exact predetermined number of repetitions.
- A `do...while` loop guarantees initial body entry, not successful completion of every statement.
- No loop type automatically guarantees termination.
- No loop type is universally faster. Performance depends on the work, runtime and implementation; choose correctness and clarity first.
- Moving a `for` update into a `while` body is not always equivalent: `continue` can skip the body update, whereas it normally proceeds to the `for` update clause. We will study this in Controlling Loops.

## 12. Key takeaways

- Prefer `for` for regular progression with naturally grouped header clauses.
- Prefer `while` when the continuation condition dominates and zero executions are valid.
- Prefer `do...while` when an initial operation is required before testing continuation.
- Treat these preferences as guidelines rather than rigid rules.
- Preserve scope, state changes, condition timing and control-flow behavior when comparing implementations.

## 13. Practice exercises

### Selection and reasoning

1. Choose a loop for printing exactly 50 labels. Explain your choice.
2. Choose a loop for searching until a matching record is found or the collection ends.
3. Choose a loop for an operation that must be attempted once before its result is known.
4. Explain why an unguarded post-test loop is unsuitable for processing a potentially empty array.
5. Explain why a known maximum iteration count does not necessarily imply a known actual iteration count.

### Implementation and comparison

6. Print 1 through 10 using all three loop types. Compare their initialization, conditions and updates.
7. Sum an array using both `for` and `while`. Test an empty array.
8. Starting at 1, double a value until it reaches or exceeds 100. Choose and justify a loop.
9. Count the digits of 0 and 12345 using a suitable loop.
10. Search `[4, 8, 2, 9]` for a target using both pre-test loop types. Handle absent targets.
11. Simulate a mandatory initial attempt with a maximum of four attempts. Test success on the first attempt and total failure.

### Debugging and design

12. Explain why `do...while` can incorrectly deduct money from an insufficient balance. Correct the design.
13. Compare `while (i++ < 3)` with a conventional `for` loop. Trace their output and final state.
14. Design a loop whose update changes according to its current state. Justify the update placement.
15. Describe a problem where each of the three loop types is the clearest choice. Explain the stopping rule and edge cases for each.

---

Previous: [The do...while Loop](05-do-while-loop.md) | Curriculum: Plain Loops | Next: Loop Ranges, Boundaries and Step Sizes (planned)
