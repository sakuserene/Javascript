# Lesson 05 - The `do...while` Loop

*JavaScript Plain Loops*

Previous: [The while Loop](04-while-loop.md) | Curriculum: Plain Loops | Next: Choosing a Loop (planned)

**Filename:** `05-do-while-loop.md`

## Table of contents

1. [Introduction](#1-introduction)
2. [Learning objectives](#2-learning-objectives)
3. [Syntax and structure](#3-syntax-and-structure)
4. [How the do...while loop works](#4-how-the-dowhile-loop-works)
5. [Post-test iteration](#5-post-test-iteration)
6. [Count-controlled do...while loops](#6-count-controlled-dowhile-loops)
7. [Condition-controlled do...while loops](#7-condition-controlled-dowhile-loops)
8. [Practical applications](#8-practical-applications)
9. [Common mistakes and edge cases](#9-common-mistakes-and-edge-cases)
10. [Best practices](#10-best-practices)
11. [Key takeaways](#11-key-takeaways)
12. [Practice exercises](#12-practice-exercises)

## 1. Introduction

The `do...while` loop is a JavaScript control-flow statement that executes its body before evaluating its continuation condition.

In the previous lessons, we studied the `for` and `while` loops. Both evaluate their conditions before entering their bodies. Consequently, both can execute zero times.

However, some programming tasks require an operation to be performed at least once before determining whether it should be repeated.

Consider the following situations:

- Performing an initial validation attempt.
- Displaying a menu before checking the user's selection.
- Attempting an operation before checking whether it succeeded.
- Processing the digits of an integer, including zero.
- Performing an initial calculation before checking whether further calculations are necessary.

The `do...while` loop is particularly suitable for these situations.

```javascript
let attempts = 0;

do {
  attempts++;
  console.log("Attempt:", attempts);
} while (attempts < 3);
```

Output:

```text
Attempt: 1
Attempt: 2
Attempt: 3
```

The defining characteristic of this loop is not the number of repetitions but **the position of its condition check in the execution cycle**.

This lesson covers the complete `do...while` curriculum branch. Run each example independently.

## 2. Learning objectives

- Explain the syntax and execution order of a `do...while` loop.
- Trace post-test iteration and predict its output.
- Construct count-controlled and condition-controlled loops.
- Implement operations that require an initial execution.
- Recognize termination errors and inappropriate uses of post-test iteration.

## 3. Syntax and structure

```javascript
do {
  // Repeated statements
} while (condition);
```

The `do` keyword introduces the body. The `while` keyword introduces the required continuation condition. JavaScript converts the condition result to Boolean: a truthy result repeats the body, and a falsy result ends the loop.

Initialization normally occurs before the statement, and updates occur inside the body.

```javascript
let i = 1; // Initialization
do {
  console.log(i); // Repeated operation
  i++; // Update
} while (i <= 3); // Continuation condition
```

Output:

```text
1
2
3
```

A single statement can also serve as the body:

```javascript
let n = 3;
do console.log(n--); while (n > 0);
```

Output:

```text
3
2
1
```

Prefer braces for clarity. The trailing semicolon is part of the statement syntax. JavaScript's automatic semicolon insertion can supply it when omitted, including after the closing condition parenthesis even without a line break. Write it explicitly. Unlike an accidental semicolon after a `while` header, this trailing semicolon does not create an empty loop body.

## 4. How the do...while loop works

### 4.1 Execution order

```text
Enter loop
    |
    v
Execute body <---------+
    |                  |
    v                  |
Check condition -- Truthy
    |
  Falsy
    |
    v
   Exit
```

The body executes first. After normal completion, the condition is evaluated. A truthy result returns execution to the beginning of the body. A falsy result transfers control to the next statement.

### 4.2 Tracing execution

```javascript
let i = 2;
do {
  console.log(i);
  i += 2;
} while (i <= 6);
console.log("Final:", i);
```

Output:

```text
2
4
6
Final: 8
```

| Iteration | i before body | Printed | i after update | Condition |
|---:|---:|---:|---:|---|
| 1 | 2 | 2 | 4 | true |
| 2 | 4 | 4 | 6 | true |
| 3 | 6 | 6 | 8 | false |

There are three body executions and three condition checks. Unlike a normally terminating pre-test loop, a normally terminating post-test loop evaluates its condition once per completed iteration.

### 4.3 Condition side effects

```javascript
let i = 0;
do {
  console.log(i);
} while (++i < 3);
console.log("Final:", i);
```

Output:

```text
0
1
2
Final: 3
```

The prefix increment changes `i` before each comparison, including the failed comparison. Keeping updates inside the body is often easier to understand.

## 5. Post-test iteration

A post-test loop enters its body at least once, regardless of whether its continuation condition would initially be true.

```javascript
let n = 10;
do {
  console.log(n);
  n++;
} while (n < 5);
```

Output:

```text
10
```

The condition is checked only after `n` becomes 11. Compare this with a pre-test loop:

```javascript
let n = 10;
while (n < 5) {
  console.log(n);
  n++;
}
```

Output: none.

| Property | while | do...while |
|---|---|---|
| First condition check | Before body | After body |
| Minimum body entries | 0 | 1 |
| Normal checks for n iterations | n + 1 | n |

An early control transfer, such as `break`, `return` or an exception, can prevent the post-test condition from being evaluated. The guarantee concerns entering the body, not completing every statement in it.

## 6. Count-controlled do...while loops

```javascript
let count = 0;
const limit = 3;
do {
  console.log("Processing");
  count++;
} while (count < limit);
```

Output:

```text
Processing
Processing
Processing
```

For a positive safe integer limit, this performs exactly that many iterations under normal execution. A zero limit still produces one iteration, so this structure is inappropriate when zero repetitions must be allowed unless guarded separately.

Backward iteration works too:

```javascript
let i = 5;
do {
  console.log(i);
  i -= 2;
} while (i >= 1);
```

Output:

```text
5
3
1
```

The update and comparison must agree with the direction of progression.

## 7. Condition-controlled do...while loops

### 7.1 Repeating until success

```javascript
let attempts = 0;
let success;
do {
  attempts++;
  success = attempts === 3; // Simulated operation result
} while (!success && attempts < 5);
console.log(success, attempts);
```

Output:

```text
true 3
```

| Attempt | Success | !success | attempts < 5 | Continue? |
|---:|---|---|---|---|
| 1 | false | true | true | Yes |
| 2 | false | true | true | Yes |
| 3 | true | false | Not evaluated | No |

The logical AND operator short-circuits when success is true. The attempt limit also prevents endless retries if the operation never succeeds. This example simulates synchronous work, not an actual network retry.

### 7.2 Repeating until a threshold

```javascript
let value = 3;
do {
  value *= 2;
  console.log(value);
} while (value < 20);
```

Output:

```text
6
12
24
```

The first multiplication is mandatory. The loop stops after reaching or exceeding the threshold. If the starting value were already 20, the body would still double it once.

## 8. Practical applications

### 8.1 Counting digits, including zero

```javascript
let number = 507;
let digits = 0;
do {
  number = Math.floor(number / 10);
  digits++;
} while (number > 0);
console.log(digits);
```

Output:

```text
3
```

| Iteration | Number before | Number after | Digits |
|---:|---:|---:|---:|
| 1 | 507 | 50 | 1 |
| 2 | 50 | 5 | 2 |
| 3 | 5 | 0 | 3 |

For a starting value of zero, the body still executes once and correctly counts one digit. This implementation assumes a nonnegative safe integer.

### 8.2 Simulating input validation

```javascript
const inputs = [-3, 0, 8];
let index = 0;
let value;
do {
  value = inputs[index];
  index++;
} while (!(value > 0) && index < inputs.length);
console.log(value > 0 ? "Accepted" : "No valid input");
console.log("Attempts:", index);
```

Output:

```text
Accepted
Attempts: 3
```

The body reads an input before checking its validity. This example assumes at least one input is available. For potentially empty arrays, guard the loop with a length check or use a pre-test loop.

### 8.3 Simulating a menu

```javascript
const choices = [1, 2, 0];
let index = 0;
let choice;
do {
  choice = choices[index++];
  console.log("Selected:", choice);
} while (choice !== 0 && index < choices.length);
```

Output:

```text
Selected: 1
Selected: 2
Selected: 0
```

The sentinel zero represents an exit selection. Unlike a sentinel checked before processing, this sentinel is observed inside the body. The example assumes a nonempty list and simulates menu selections rather than collecting live input.

## 9. Common mistakes and edge cases

### 9.1 Assuming zero iterations are possible

```javascript
let i = 0;
const limit = 0;
do {
  console.log(i);
  i++;
} while (i < limit);
```

Output:

```text
0
```

If zero repetitions are required, choose `while` or guard the entire `do...while` statement with an initial condition.

### 9.2 Missing state updates

```javascript
// Incorrect: do not run.
let i = 1;
do {
  console.log(i);
} while (i < 3);
```

The loop repeatedly prints 1 because the condition never changes. Add `i++` inside the body.

### 9.3 Declaring condition variables inside the body

```javascript
do {
  let count = 1;
  console.log(count);
} while (count < 3);
```

Observable behavior: prints 1, then throws a `ReferenceError` if no outer `count` binding exists. The body-local binding is not visible in the condition. Declare persistent state before the loop.

### 9.4 Reversing the continuation condition

When repeating until success, use `while (!success)`, not `while (success)`. The condition describes when to continue, not when to stop. Include a suitable attempt limit where needed.

## 10. Best practices

- Choose post-test iteration only when an initial body entry is appropriate.
- Initialize persistent state outside the body.
- Use braces and the trailing semicolon.
- Express the continuation condition clearly.
- Ensure state changes make progress toward termination.
- Guard empty inputs and zero attempt limits when necessary.
- Avoid synchronous busy-waiting for external events.

## 11. Key takeaways

- `do...while` is a post-test loop with a required condition.
- The body is entered before the first condition check.
- Normal termination involves one condition check per iteration.
- The loop supports count-controlled and condition-controlled repetition.
- It is useful when an operation must occur before its result can be tested.
- It is unsuitable for unguarded tasks that may require zero executions.

## 12. Practice exercises

### Understanding and tracing

1. Explain why `do...while` is called a post-test loop.
2. Trace the following code, including every condition check.

```javascript
let n = 8;
do {
  console.log(n);
  n -= 3;
} while (n > 0);
```

3. Predict the output and final value of `i`.

```javascript
let i = 0;
do {
  console.log(i);
} while (i++ < 2);
```

4. Explain the difference between entering a body and completing an iteration normally.

### Writing loops

5. Print the integers from 1 through 10 using `do...while`.
6. Print the even integers from 10 down to 2.
7. Starting at 1, repeatedly triple the value until it reaches or exceeds 100. Report the final value and number of updates.
8. Count the digits of 5839 and zero without converting them to strings.
9. Simulate an operation that succeeds on the fourth attempt, with a maximum of five attempts.
10. Process the menu choices `[2, 1, 0, 3]` until zero is selected.

### Debugging and reasoning

11. Correct a loop whose counter starts at 1, whose condition is `i < 5`, and whose body never updates `i`.
12. Explain why a post-test loop with a zero repetition limit still enters its body. Rewrite it to allow zero iterations.
13. Modify the validation example to handle an empty input array safely.
14. Design and trace a bounded retry loop in which every attempt fails.

---

Previous: [The while Loop](04-while-loop.md) | Curriculum: Plain Loops | Next: Choosing a Loop (planned)
