# Lesson 01 â€” Loop Basics

*JavaScript Plain Loops Â· Foundations*

Previous: None | Curriculum: Plain Loops | Next: JavaScript Prerequisites (planned)

## Table of contents

1. [Introduction](#1-introduction)
2. [Learning objectives](#2-learning-objectives)
3. [Repeating code](#3-repeating-code)
4. [Parts of a loop](#4-parts-of-a-loop)
5. [Types of iteration](#5-types-of-iteration)
6. [How loops work](#6-how-loops-work)
7. [Practical applications](#7-practical-applications)
8. [Common mistakes](#8-common-mistakes)
9. [Key takeaways](#9-key-takeaways)
10. [Practice exercises](#10-practice-exercises)

## 1. Introduction

One of the fundamental purposes of programming is to automate repetitive tasks.

Imagine developing an application that needs to calculate the total price of hundreds of products, validate thousands of records or display a list of registered users.

Writing a separate instruction for every product, record or user would be inefficient, difficult to maintain and sometimes impossible because the number of items might not be known beforehand.

JavaScript provides **loops** to solve this problem.

A loop is a control-flow construct that repeatedly executes a statement or block of statements according to a condition or repetition rule.

Loops are used extensively in:

- Processing arrays, strings and other collections.
- Performing repeated mathematical calculations.
- Searching for information.
- Validating data.
- Generating sequences and patterns.
- Processing records and transactions.
- Implementing algorithms.

Understanding loops involves more than memorizing their syntax. You must understand how execution flows, how values change between iterations, how repetition is controlled and how a loop eventually terminates.

This lesson establishes those foundations before we study individual loop statements in greater depth.

## 2. Learning objectives

By the end of this lesson, you should be able to:

- Explain loops, iteration, loop bodies and iteration state.
- Identify programming tasks that require repetition.
- Replace repetitive statements with loops.
- Identify the different components of a loop.
- Distinguish count-controlled, condition-controlled and sentinel-controlled iteration.
- Distinguish definite and indefinite iteration.
- Explain pre-test and post-test loops.
- Trace loop execution and changing variable values.
- Predict loop output and final variable values.
- Determine how many times a loop executes.

Basic familiarity with variables, arithmetic and `console.log()` is helpful. We will revisit the necessary JavaScript operators and expressions in the next lesson.

## 3. Repeating code

### 3.1 Loops and iteration

Without a loop, printing five numbers requires five separate instructions.

```javascript
console.log(1);
console.log(2);
console.log(3);
console.log(4);
console.log(5);
```

Using a loop, we can express the same task as a repetition rule.

```javascript
for (let i = 1; i <= 5; i++) {
  console.log(i);
}
```

Output of both examples:

```text
1
2
3
4
5
```

The loop starts with `i` equal to 1, prints its current value and increases it by one. It repeats while `i <= 5` is true. Changing the upper limit to 100 would require no additional print statements.

An **iteration** is one execution of the loop body. The loop above performs five iterations. The loop is the entire construct; an iteration is one pass through its body.

### 3.2 The loop body

The loop body contains the instructions that are repeated. It may contain multiple statements.

```javascript
for (let i = 1; i <= 3; i++) {
  console.log("Number:", i);
  console.log("Square:", i * i);
}
```

Output:

```text
Number: 1
Square: 1
Number: 2
Square: 4
Number: 3
Square: 9
```

Both statements execute during each iteration, in order. There are three iterations and six calls to `console.log()`.

### 3.3 Iteration state

Iteration state is the information maintained or changed as a loop runs. It can include a counter, a running total, a current position or a Boolean flag.

```javascript
let total = 0;
for (let i = 1; i <= 4; i++) {
  total += i; // Add the current number to the running total
}
console.log(total);
```

Output:

```text
10
```

| Iteration | i | Total before | Total after |
|---:|---:|---:|---:|
| 1 | 1 | 0 | 1 |
| 2 | 2 | 1 | 3 |
| 3 | 3 | 3 | 6 |
| 4 | 4 | 6 | 10 |

Here `i` controls repetition, while `total` preserves the result of previous iterations. This is the foundation of accumulation, which we will study later.

### 3.4 Recognizing repetition

To turn repeated instructions into a loop, identify what stays the same, what changes, where to start and when to stop. For example, displaying the multiples of three uses the same multiplication operation with a changing operand.

```javascript
for (let i = 1; i <= 4; i++) {
  console.log(i * 3);
}
```

Output:

```text
3
6
9
12
```

## 4. Parts of a loop

A typical loop has initialization, a condition, a body, a state update and termination. These are conceptual components, not necessarily five separate pieces of syntax.

```javascript
let i = 1; // Initialization
while (i <= 3) { // Condition
  console.log(i); // Body operation
  i++; // State update
}
console.log("Finished"); // After termination
```

Output:

```text
1
2
3
Finished
```

| Component | Purpose |
|---|---|
| Initialization | Establish the starting state. |
| Condition | Determine whether repetition should continue. |
| Body | Perform the repeated work. |
| State update | Change information used by later iterations. |
| Termination | End repetition and transfer control onward. |

Initialization occurs once in this example. The condition is checked repeatedly. The update changes `i` so that the condition eventually becomes false. Termination is an event, not a separate required statement. Some loops terminate through `break` or other control transfers instead.

## 5. Types of iteration

### 5.1 Count-controlled iteration

Use count-controlled iteration when repetition is governed by a specified count.

```javascript
const repetitions = 3;
for (let i = 0; i < repetitions; i++) {
  console.log("Hello");
}
```

Output:

```text
Hello
Hello
Hello
```

The counter visits 0, 1 and 2. Starting at zero does not mean the loop performs zero iterations.

### 5.2 Condition-controlled iteration

Repetition can depend on changing state rather than an explicit target count.

```javascript
let balance = 95;
while (balance >= 30) {
  balance -= 30;
  console.log(balance);
}
```

Output:

```text
65
35
5
```

| Check | Balance before | Condition | Balance after |
|---:|---:|---|---:|
| 1 | 95 | true | 65 |
| 2 | 65 | true | 35 |
| 3 | 35 | true | 5 |
| 4 | 5 | false | â€” |

The loop stops when there is insufficient balance for another deduction. Although the count can be calculated for this particular input, the stopping rule is expressed in terms of balance.

### 5.3 Sentinel-controlled iteration

A sentinel is a special value that signals the end of processing. It must be distinguishable from ordinary data under the chosen input rules.

```javascript
const values = [12, 7, 4, -1, 20];
let index = 0;
while (index < values.length && values[index] !== -1) {
  console.log(values[index]);
  index++;
}
```

Output:

```text
12
7
4
```

An array stores a sequence of values. `values[index]` reads a value at a zero-based position, and `values.length` gives its length. The loop stops at `-1`, without processing it. The length check also allows termination if the sentinel is absent. The `&&` operator requires both conditions to be truthy.

### 5.4 Definite and indefinite iteration

Definite iteration has a repetition count known before execution of the loop. Indefinite iteration has a count that depends on information discovered or changed during execution.

A loop that processes exactly 100 records is definite. A loop that searches records until it finds a matching value is generally indefinite: the matching position may not be known in advance.

These classifications describe the task, not the JavaScript keyword. A `while` loop can be definite, and a `for` loop can be indefinite.

### 5.5 Pre-test and post-test loops

A pre-test loop checks its condition before entering the body. JavaScript's `for` and `while` loops are pre-test loops.

```javascript
let n = 5;
while (n < 3) {
  console.log(n);
  n++;
}
console.log("Done");
```

Output:

```text
Done
```

A post-test loop enters its body before checking the condition. JavaScript's `do...while` is a post-test loop.

```javascript
let n = 5;
do {
  console.log(n);
  n++;
} while (n < 3);
console.log("Done");
```

Output:

```text
5
Done
```

The second loop enters its body once even though the condition would initially have been false. These examples are separate programs.

| Classification | What it describes |
|---|---|
| Count-controlled | Repetition governed by a count |
| Condition-controlled | Repetition governed by a condition |
| Sentinel-controlled | Repetition stopped by a special value |
| Definite or indefinite | Whether the count is known in advance |
| Pre-test or post-test | When the condition is checked |

These classifications overlap. A count-controlled `for` loop can be definite and pre-test at the same time.

## 6. How loops work

### 6.1 Execution order

For a normally completing `for` loop, execution follows this cycle:

```text
Initialize once
      â†“
Check condition â†â”€â”€â”€â”€â”€â”€â”€â”€â”
      â†“                  â”‚
   Truthy? â”€â”€ No â†’ Exit  â”‚
      â”‚                  â”‚
     Yes                 â”‚
      â†“                  â”‚
 Execute body            â”‚
      â†“                  â”‚
 Evaluate update â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```

JavaScript executes the body sequentially. After the update, it checks the condition again. A falsy condition prevents the next body execution.

### 6.2 Tracing a loop

```javascript
let i = 2;
for (; i <= 8; i += 2) {
  console.log(i);
}
console.log("Final:", i);
```

Output:

```text
2
4
6
8
Final: 10
```

| Check | i before body | i <= 8 | Output | i after update |
|---:|---:|---|---:|---:|
| 1 | 2 | true | 2 | 4 |
| 2 | 4 | true | 4 | 6 |
| 3 | 6 | true | 6 | 8 |
| 4 | 8 | true | 8 | 10 |
| 5 | 10 | false | â€” | â€” |

There are four iterations, four updates and five condition checks. The final value of `i` is 10, but 10 is not printed by the loop body. The variable is declared outside the loop so it remains accessible afterward.

### 6.3 Zero, one and multiple iterations

```javascript
for (let i = 0; i < 0; i++) {
  console.log(i);
}
```

Output: none. The initial condition is false.

```javascript
for (let i = 0; i < 1; i++) {
  console.log(i);
}
```

Output:

```text
0
```

```javascript
for (let i = 0; i < 3; i++) {
  console.log(i);
}
```

Output:

```text
0
1
2
```

For a normally terminating pre-test loop with an explicit condition and no early exit, the condition is evaluated one more time than the body is executed. A normally terminating `do...while` loop checks its condition once per completed iteration.

### 6.4 Determining the iteration count

For an integer counter starting at `a`, increasing by one and stopping at an exclusive upper bound `b`, the count is `Math.max(0, b - a)`. For an inclusive upper bound, it is `Math.max(0, b - a + 1)`, assuming exact arithmetic and no early exit or other state changes.

| Loop header | Visited values | Iterations |
|---|---|---:|
| `for (let i = 0; i < 4; i++)` | 0, 1, 2, 3 | 4 |
| `for (let i = 1; i <= 4; i++)` | 1, 2, 3, 4 | 4 |
| `for (let i = 3; i < 6; i++)` | 3, 4, 5 | 3 |
| `for (let i = 4; i < 4; i++)` | None | 0 |
| `for (let i = 1; i <= 7; i += 2)` | 1, 3, 5, 7 | 4 |

For more complicated updates, tracing the visited values is a useful starting point. We will derive more general counting rules in the lesson on loop ranges and step sizes.

## 7. Practical applications

### 7.1 Calculating a shopping total

```javascript
const prices = [120, 250, 80];
let total = 0;
for (let i = 0; i < prices.length; i++) {
  total += prices[i];
}
console.log("Total:", total);
```

Output:

```text
Total: 450
```

| Index | Price | Running total |
|---:|---:|---:|
| 0 | 120 | 120 |
| 1 | 250 | 370 |
| 2 | 80 | 450 |

Using the array length rather than a fixed number allows the same loop to process different numbers of products.

### 7.2 Generating a countdown

```javascript
let remaining = 3;
while (remaining > 0) {
  console.log(remaining);
  remaining--;
}
console.log("Go!");
```

Output:

```text
3
2
1
Go!
```

The changing state is `remaining`. Each update moves it toward zero. This is a logical countdown, not a timed countdown: the loop does not wait one second between iterations.

## 8. Common mistakes

### 8.1 Missing the update

```javascript
// Incorrect: do not run this loop as written.
let i = 1;
while (i <= 3) {
  console.log(i);
}
```

The loop repeatedly prints 1 because `i` never changes. The condition remains true. Add `i++` inside the body to make progress toward termination.

### 8.2 Incorrect boundaries

```javascript
for (let i = 1; i < 5; i++) {
  console.log(i);
}
```

Output:

```text
1
2
3
4
```

If the intended range is 1 through 5 inclusive, the condition should be `i <= 5`. This is an off-by-one error relative to that requirement.

### 8.3 Resetting persistent state

```javascript
for (let i = 1; i <= 3; i++) {
  let total = 0;
  total += i;
  console.log(total);
}
```

Output:

```text
1
2
3
```

If the intended output is running totals of 1, 3 and 6, the initialization of `total` must occur before the loop. Declaring it inside the body creates a fresh variable on each iteration.

## 9. Key takeaways

- A loop expresses repeated work; an iteration is one execution of its body.
- Iteration state carries information through the repetition process.
- Initialization, conditions, body operations and updates cooperate to control repetition.
- Count, sentinel and condition classifications describe how repetition is controlled.
- Pre-test loops may execute zero times; post-test loops enter their body at least once.
- Tracing conditions and state changes helps predict output, iteration counts and termination.

## 10. Practice exercises

### Understanding and tracing

1. Explain the difference between a loop, its body and an iteration.
2. Identify the initialization, condition, body and update in the countdown example.
3. Trace the following code, recording every condition check, printed value and update. Determine the iteration count.

```javascript
for (let i = 3; i <= 12; i += 3) {
  console.log(i);
}
```

4. Predict the output and final value of `total`.

```javascript
let total = 0;
for (let i = 1; i <= 4; i++) {
  total += i * 2;
  console.log(total);
}
```

### Writing loops

5. Replace five separate statements printing multiples of 10 with a loop.
6. Write a loop that prints the numbers from 6 down to 1.
7. Starting with 50, repeatedly subtract 7 while the value is at least 7. Print the remaining value after each deduction.
8. Process `[3, 8, 2, 0, 9]` until the sentinel `0` or the end of the array. Do not print the sentinel.

### Debugging and problem-solving

9. Explain why the following loop does not terminate normally and correct it without running the original code.

```javascript
let i = 5;
while (i > 0) {
  console.log(i);
  i++;
}
```

10. Design a loop that calculates the total of `[40, 60, 100, 25]`. Trace its running total.
11. Classify these tasks by repetition rule and whether their count is known in advance: printing 20 labels, searching until a match is found, and reading values until a sentinel is encountered.
12. Write a pre-test loop that executes zero times and a post-test loop with the same initial state and condition that executes once. Explain the difference.

---

Previous: None | Curriculum: Plain Loops | Next: JavaScript Prerequisites (planned)
