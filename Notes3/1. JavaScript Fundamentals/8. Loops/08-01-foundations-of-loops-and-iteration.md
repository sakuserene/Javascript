# JavaScript Mastery Course

## Unit 1 - Topic 8 - Lesson 8.1: Foundations of Loops and Iteration

### Part 1: Understanding Repetition, Program Execution, and Loop Control

> **Status:** Part 1 of a multi-part textbook chapter. This is not yet the completed 4,000+ line Topic 8.1 guide. Additional parts will be combined into the comprehensive chapter.
>
> **Level:** Beginner through intermediate, with foundations for advanced algorithm analysis.
>
> **Format:** GitHub-compatible Markdown; ASCII diagrams; executable JavaScript examples.

## Table of Contents

1. [Learning objectives](#learning-objectives)
2. [8.1.1 How JavaScript programs execute](#811-how-javascript-programs-execute)
3. [8.1.2 Sequential execution and repetition](#812-sequential-execution-and-repetition)
4. [8.1.3 Control flow](#813-control-flow)
5. [8.1.4 Five conceptual components of a loop](#814-five-conceptual-components-of-a-loop)
6. [8.1.5 Program state: counters, accumulators, and flags](#815-program-state-counters-accumulators-and-flags)
7. [8.1.6 Conditions and truthiness](#816-conditions-and-truthiness)
8. [8.1.7 Termination and infinite loops](#817-termination-and-infinite-loops)
9. [8.1.8 Zero, one, and many iterations](#818-zero-one-and-many-iterations)
10. [8.1.9 Mathematical reasoning about iteration](#819-mathematical-reasoning-about-iteration)
11. [8.1.10 Designing a loop from a problem](#8110-designing-a-loop-from-a-problem)
12. [8.1.11 Debugging loops](#8111-debugging-loops)
13. [8.1.12 Loop invariants and correctness](#8112-loop-invariants-and-correctness)
14. [8.1.13 Technical interview questions and answers](#8113-technical-interview-questions-and-answers)
15. [8.1.14 Coding exercises with worked solutions](#8114-coding-exercises-with-worked-solutions)
16. [8.1.15 Extended practice and review](#8115-extended-practice-and-review)
17. [Navigation](#navigation)

---

## Learning objectives

After completing Part 1, you should be able to:

- Distinguish repetition, iteration, a loop, and a loop body.
- Explain sequential execution, expressions, statements, and control flow.
- Identify a loop's initial state, continuation condition, body, state transition, and termination.
- Trace changing variables without executing the program.
- Distinguish a counter, accumulator, flag, and current element.
- Predict zero-iteration and single-iteration cases.
- Identify nonterminating loops and incompatible update directions.
- Recognize off-by-one errors and floating-point boundary hazards.
- Translate a repetitive real-world task into pseudocode and JavaScript.
- Begin using invariants to reason about correctness.

## 8.1.1 How JavaScript programs execute

### What is program execution?

JavaScript code consists of instructions evaluated by a JavaScript engine according to the language's rules. In a straightforward sequence, statements execute in source order. Function calls, branching, loops, exceptions, and asynchronous operations can change the observed order of work.

```javascript
let price = 200;
let quantity = 3;
let total = price * quantity;
console.log(total);
```

Expected output:

```text
600
```

**Line 1: `let price = 200;`**

- `let` declares a mutable variable binding.
- `price` is the identifier used to access the binding.
- `=` assigns the evaluated right-hand value during initialization; it is not a comparison.
- `200` is a numeric literal (JavaScript `Number`).
- `;` terminates the declaration statement.

**Line 2: `let quantity = 3;`** creates a separate binding with value 3.

**Line 3: `let total = price * quantity;`** evaluates `price * quantity`, which is `200 * 3`, producing 600. That value initializes `total`.

**Line 4: `console.log(total);`** accesses `console.log` and calls it with the current value of `total`. The output is 600.

| Step | Action | price | quantity | total |
|---|---|---:|---:|---:|
| Start | Before statements | Not initialized | Not initialized | Not initialized |
| 1 | Initialize price | 200 | - | - |
| 2 | Initialize quantity | 200 | 3 | - |
| 3 | Multiply and initialize total | 200 | 3 | 600 |
| 4 | Print total | 200 | 3 | 600 |

### Statements and expressions

A **statement** performs an action or controls execution. An **expression** is evaluated to produce a value. Expressions can also cause side effects.

```javascript
let age = 25;            // Declaration statement
age = age + 1;           // Expression statement
console.log(age);        // Function-call expression statement
```

In `age = age + 1`, the right-hand `age + 1` is evaluated first, and the resulting value is assigned to `age`. The whole assignment expression also produces the assigned value.

In `let total = 10 + 20;`, the whole line is a declaration; `10 + 20` is an expression that evaluates to 30.

### Why this matters for loops

```javascript
while (age < 30) {
  age++;
}
```

- `while` introduces a loop statement.
- `(age < 30)` contains the continuation condition.
- `<` compares the left and right operands and returns a boolean.
- `{ ... }` is the loop body block.
- `age++` changes state, allowing the condition eventually to become false.

The condition is reevaluated, not evaluated only once.

## 8.1.2 Sequential execution and repetition

### Manual repetition

```javascript
console.log("Invoice 1");
console.log("Invoice 2");
console.log("Invoice 3");
console.log("Invoice 4");
console.log("Invoice 5");
```

This produces the intended five lines, but manually duplicating a statement is difficult to maintain for hundreds or thousands of cases.

### Rule-based repetition

```javascript
for (let invoice = 1; invoice <= 5; invoice++) {
  console.log("Invoice " + invoice);
}
```

Output:

```text
Invoice 1
Invoice 2
Invoice 3
Invoice 4
Invoice 5
```

**Token-by-token:**

| Token | Meaning |
|---|---|
| `for` | Introduces a `for` loop statement |
| `(` and `)` | Enclose the loop header |
| `let invoice = 1` | Initializes the counter once |
| First `;` | Separates initialization and condition |
| `invoice <= 5` | Checks whether the current value is at most 5 |
| Second `;` | Separates condition and update |
| `invoice++` | Increments the counter after a normal body execution |
| `{` and `}` | Delimit the loop body |
| `console.log(...)` | Prints a value on each iteration |
| `"Invoice " + invoice` | Concatenates text with the number converted to a string |

### Loop, iteration, and repetition

- **Repetition:** Performing an operation multiple times.
- **Loop:** A language construct used to control repeated execution.
- **Iteration:** One cycle of the repeated processing.
- **Loop body:** The code that performs the repeated work.

```javascript
for (let number = 1; number <= 3; number++) {
  console.log(number);
}
```

| Iteration | number | Printed |
|---|---:|---:|
| 1 | 1 | 1 |
| 2 | 2 | 2 |
| 3 | 3 | 3 |

There are **three body executions**, **three updates**, and **four condition evaluations**. The final condition evaluation fails when `number` becomes 4.

**Important:** A condition evaluation that fails is not an additional body iteration.

## 8.1.3 Control flow

**Control flow** describes the order in which instructions execute.

```text
SEQUENCE                 SELECTION                  ITERATION
  Step A                    Condition?                 Condition?
    |                       /      \                   /      \
  Step B                  true     false              yes      no
    |                      |         |                 |        |
  Step C                  Path A    Path B             Body     Exit
                                                        |
                                                   Update state
                                                        |
                                                Check condition again
```

### Sequence

```javascript
let value = 2;
value = value * 3;
console.log(value);
```

Output: `6`.

### Selection

```javascript
let balance = 500;
if (balance >= 300) {
  console.log("Purchase allowed");
} else {
  console.log("Insufficient balance");
}
```

Output: `Purchase allowed`.

The `>=` operator means *greater than or equal to*. The `if` statement executes the first branch because `500 >= 300` is true.

### Iteration

```javascript
let count = 1;
while (count <= 3) {
  console.log(count);
  count++;
}
```

Output: 1, 2, and 3 on separate lines. After printing 3, the update changes `count` to 4 and the next check fails.

## 8.1.4 Five conceptual components of a loop

A useful mental model identifies:

1. **Initial state:** Values established before repetition.
2. **Continuation condition:** A rule deciding whether another iteration may run.
3. **Body:** The repeated operation.
4. **State transition:** A change in relevant values or progress.
5. **Termination:** The point where execution exits the loop.

These are conceptual roles, not necessarily five separate syntax elements in every JavaScript loop. For example, `for...of` uses an iterator internally.

```javascript
let count = 1;             // Initial state
while (count <= 5) {       // Continuation condition
  console.log(count);      // Body
  count = count + 1;       // State transition
}                         // Exit when condition becomes false
```

| Check | count before check | Condition | Body | count after update |
|---|---:|---|---|---:|
| 1 | 1 | true | Print 1 | 2 |
| 2 | 2 | true | Print 2 | 3 |
| 3 | 3 | true | Print 3 | 4 |
| 4 | 4 | true | Print 4 | 5 |
| 5 | 5 | true | Print 5 | 6 |
| 6 | 6 | false | Skipped | 6 |

### What can go wrong?

- Incorrect initial state may skip intended work.
- Incorrect condition may stop too early or never stop.
- Incorrect body may compute the wrong result.
- Incorrect state transition may prevent termination.
- Incorrect exit behavior may process too many or too few values.

## 8.1.5 Program state: counters, accumulators, and flags

### State

**Program state** is the information that describes the program at a particular moment. A loop often revisits the same code with different state.

```javascript
let counter = 0;
counter = counter + 1;
counter = counter + 1;
counter = counter + 1;
console.log(counter);
```

Output: `3`.

| Execution | Before | Calculation | After |
|---|---:|---|---:|
| 1 | 0 | 0 + 1 | 1 |
| 2 | 1 | 1 + 1 | 2 |
| 3 | 2 | 2 + 1 | 3 |

A loop expresses the repeated transition:

```javascript
let counter = 0;
for (let i = 0; i < 3; i++) {
  counter = counter + 1;
}
console.log(counter); // 3
```

Here `i` controls iteration; `counter` stores the result. Their values happen to increase together, but they have different responsibilities.

### Counters

```javascript
for (let day = 1; day <= 7; day++) {
  console.log(day);
}
```

`day` is a loop control variable. It takes the values 1 through 7, and becomes 8 before the loop terminates.

### Accumulators

```javascript
let total = 0;
for (let number = 1; number <= 4; number++) {
  total = total + number;
}
console.log(total); // 10
```

| Iteration | number | total before | total after |
|---|---:|---:|---:|
| 1 | 1 | 0 | 1 |
| 2 | 2 | 1 | 3 |
| 3 | 3 | 3 | 6 |
| 4 | 4 | 6 | 10 |

**Why zero?** Zero is the additive identity: `0 + x` equals `x`.

### Running products

```javascript
let product = 1;
for (let number = 1; number <= 4; number++) {
  product *= number;
}
console.log(product); // 24
```

`product *= number` means `product = product * number`. Start at 1 because 1 is the multiplicative identity. Starting at zero would make the product remain zero.

### Boolean flags

```javascript
const numbers = [4, 9, 12, 7];
let found = false;
for (const number of numbers) {
  if (number === 12) {
    found = true;
  }
}
console.log(found); // true
```

- `found` is a flag that records whether a match has occurred.
- `for...of` visits the array's values.
- `===` checks strict equality, without coercing the values to a common type.
- The `if` body changes the flag only on a match.

### State that remains unchanged

```javascript
const taxRate = 0.16;
const prices = [100, 200, 300];
let totalTax = 0;
for (const price of prices) {
  totalTax += price * taxRate;
}
console.log(totalTax); // 96
```

`taxRate` is not reassigned. `totalTax` is updated on each iteration. A `const` binding prevents reassignment of that binding; it does not deeply freeze an object referenced through it.

## 8.1.6 Conditions and truthiness

### Boolean conditions

```javascript
let count = 1;
while (count <= 3) {
  console.log(count);
  count++;
}
```

| count at check | `count <= 3` |
|---:|---|
| 1 | true |
| 2 | true |
| 3 | true |
| 4 | false |

### Truthiness

JavaScript converts a loop condition's result to a boolean for control-flow purposes.

```javascript
let remaining = 3;
while (remaining) {
  console.log(remaining);
  remaining--;
}
```

Output: 3, 2, 1. The loop stops when `remaining` becomes the falsy number 0.

| Value | Truthiness |
|---|---|
| `true` | Truthy |
| `false` | Falsy |
| `0`, `-0`, `0n` | Falsy |
| `1`, `-5` | Truthy |
| `""` | Falsy |
| `"0"`, `"false"` | Truthy |
| `null`, `undefined`, `NaN` | Falsy |
| `[]`, `{}` | Truthy |

**Surprise:** Empty arrays and empty objects are truthy.

### Incorrect update direction

Do not run this example without a way to interrupt execution:

```javascript
let count = -3;
while (count) {
  console.log(count);
  count--;
}
```

The count progresses away from zero. It eventually reaches `-Infinity` in Number arithmetic, which is truthy, so the loop does not terminate naturally.

Correct version for counting toward zero:

```javascript
let count = -3;
while (count < 0) {
  console.log(count);
  count++;
}
```

Output: -3, -2, -1.

### Compound conditions

```javascript
let attempts = 0;
let completed = false;
while (attempts < 3 && !completed) {
  attempts++;
  if (attempts === 2) {
    completed = true;
  }
  console.log("Attempt:", attempts);
}
```

Output:

```text
Attempt: 1
Attempt: 2
```

**Detailed condition breakdown:**

- `attempts < 3` checks the maximum-attempt constraint.
- `&&` is logical AND; it short-circuits if its left operand is falsy.
- `!completed` converts the flag to a boolean and negates it.
- The loop continues only when the combined expression is truthy.

| Check | attempts | completed | Continue? |
|---|---:|---|---|
| 1 | 0 | false | Yes |
| 2 | 1 | false | Yes |
| 3 | 2 | true | No |

## 8.1.7 Termination and infinite loops

A **finite loop** eventually exits. An **infinite loop** keeps executing unless interrupted or terminated externally. Some programs intentionally use long-running loops, but unintended nontermination is a bug.

### Missing update

**Warning: intentionally nonterminating example; do not execute without interruption controls.**

```javascript
let count = 1;
while (count <= 5) {
  console.log(count);
}
```

The body never changes `count`, so the condition stays true.

### Wrong update direction

**Warning: intentionally nonterminating example.**

```javascript
let count = 1;
while (count <= 5) {
  console.log(count);
  count--;
}
```

The counter moves away from the stopping boundary.

### Unreachable equality target

**Warning: intentionally nonterminating example.**

```javascript
let number = 1;
while (number !== 10) {
  number += 2;
}
```

The sequence starts 1, 3, 5, 7, 9, 11, ... and never reaches exactly 10. At very large values, Number precision also affects updates, but it still does not reach 10.

A boundary-based alternative:

```javascript
let number = 1;
while (number < 10) {
  number += 2;
}
console.log(number); // 11
```

The final value is 11, because the update crosses the upper boundary.

### Termination checklist

1. What state does the loop start with?
2. What condition allows another iteration?
3. What state changes during the body or update?
4. Does that change move toward an exit?
5. Can the exit actually be reached?
6. Could invalid input prevent progress?
7. Could numeric precision change the outcome?
8. Is an early exit or cancellation needed?

## 8.1.8 Zero, one, and many iterations

### Zero iterations with `while`

```javascript
let count = 10;
while (count < 5) {
  console.log(count);
  count++;
}
```

Output: no output. The condition fails before the body runs.

### Exactly one iteration

```javascript
let count = 5;
while (count <= 5) {
  console.log(count);
  count++;
}
```

Output: `5`. The update makes `count` equal to 6, so the next check fails.

### Post-test `do...while`

```javascript
let count = 10;
do {
  console.log(count);
  count++;
} while (count < 5);
```

Output: `10`. The body executes before the condition is checked.

| Feature | `for` | `while` | `do...while` |
|---|---|---|---|
| Checks condition before body | Yes | Yes | No |
| Can run body zero times | Yes | Yes | Not on normal entry |
| Has initialization slot in header | Optional | No | No |
| Has update slot in header | Optional | No | No |
| Typical use | Counted repetition | Condition-controlled work | Execute once, then decide |

## 8.1.9 Mathematical reasoning about iteration

### Exclusive and inclusive boundaries

```javascript
for (let i = 0; i < 5; i++) {
  console.log(i);
}
```

Prints 0, 1, 2, 3, 4: five body executions.

```javascript
for (let i = 0; i <= 5; i++) {
  console.log(i);
}
```

Prints 0, 1, 2, 3, 4, 5: six body executions.

| Condition | Values | Iterations |
|---|---|---:|
| `i < 5` from 0, step 1 | 0..4 | 5 |
| `i <= 5` from 0, step 1 | 0..5 | 6 |
| `i < 0` from 0, step 1 | None | 0 |
| `i <= 0` from 0, step 1 | 0 | 1 |

### Arithmetic progressions

```javascript
for (let i = 2; i <= 10; i += 2) {
  console.log(i);
}
```

Output: 2, 4, 6, 8, 10.

For a positive step `d`, inclusive upper bound `b`, and initial value `a`, assuming exact arithmetic and no other counter changes:

```text
N = max(0, floor((b - a) / d) + 1)
```

Here `a=2`, `b=10`, and `d=2`:

```text
N = floor((10 - 2) / 2) + 1
  = 4 + 1
  = 5
```

### Floating-point hazards

```javascript
for (let x = 0; x <= 0.3; x += 0.1) {
  console.log(x);
}
```

Typical output:

```text
0
0.1
0.2
```

After repeated additions, `x` is approximately `0.30000000000000004`, which is greater than 0.3. The loop stops before printing the expected 0.3.

Prefer an integer counter:

```javascript
for (let i = 0; i <= 3; i++) {
  const value = i / 10;
  console.log(value);
}
```

Output:

```text
0
0.1
0.2
0.3
```

**Important:** Integer counters are usually more predictable for discrete iteration. For extremely large counters, JavaScript Number's integer precision limits still matter.

## 8.1.10 Designing a loop from a problem

### Case study: total expenses

Input:

```javascript
const expenses = [250, 400, 150, 600];
```

**Problem:** Compute the sum.

**Step 1: Input.** An array of expenses.

**Step 2: Output.** One numeric total.

**Step 3: Repeated operation.** Add the current expense to a running total.

**Step 4: State.** An accumulator and a way to visit each expense.

**Step 5: Pseudocode.**

```text
SET total to 0
FOR each expense in expenses
    ADD expense to total
END FOR
DISPLAY total
```

**Step 6: JavaScript.**

```javascript
const expenses = [250, 400, 150, 600];
let total = 0;
for (const expense of expenses) {
  total += expense;
}
console.log(total); // 1400
```

| Iteration | expense | total before | total after |
|---|---:|---:|---:|
| 1 | 250 | 0 | 250 |
| 2 | 400 | 250 | 650 |
| 3 | 150 | 650 | 800 |
| 4 | 600 | 800 | 1400 |

### Empty input

```javascript
const expenses = [];
let total = 0;
for (const expense of expenses) {
  total += expense;
}
console.log(total); // 0
```

The body executes zero times; the initial total is preserved.

### Mixed-type input and string concatenation

```javascript
let total = 0;
total += 250;   // 250
total += "400"; // "250400"
total += 150;   // "250400150"
```

The `+` operator can concatenate strings. This is a reason to validate data instead of assuming all inputs are numbers.

### Reusable validated function

```javascript
function calculateExpenses(expenses) {
  if (!Array.isArray(expenses)) {
    throw new TypeError("Expected an array");
  }

  let total = 0;

  for (const expense of expenses) {
    if (typeof expense !== "number" ||
        !Number.isFinite(expense)) {
      throw new TypeError("Each expense must be a finite number");
    }
    total += expense;
  }

  return total;
}

console.log(calculateExpenses([250, 400, 150, 600])); // 1400
```

- `Array.isArray(expenses)` checks whether the argument is an array.
- `!` negates the result.
- `throw new TypeError(...)` stops normal execution and reports an invalid argument.
- `typeof expense !== "number"` rejects non-numeric values.
- `||` is logical OR, so either invalidity condition causes rejection.
- `Number.isFinite(expense)` rejects `NaN`, `Infinity`, and `-Infinity`.
- `return total` gives the accumulated value to the caller.

For exact financial arithmetic, integer minor units or an appropriate decimal arithmetic solution may be necessary; ordinary Number arithmetic can have rounding errors.

## 8.1.11 Debugging loops

### Trace state explicitly

```javascript
let total = 0;
for (let i = 1; i <= 3; i++) {
  console.log("Before:", i, total);
  total += i;
  console.log("After:", i, total);
}
```

Output:

```text
Before: 1 0
After: 1 1
Before: 2 1
After: 2 3
Before: 3 3
After: 3 6
```

The trace shows how `total` changes on each iteration.

### Off-by-one bug

```javascript
const values = [10, 20, 30, 40, 50];
for (let i = 0; i <= values.length; i++) {
  console.log(values[i]);
}
```

Output:

```text
10
20
30
40
50
undefined
```

An array of length 5 has ordinary element indices 0 through 4. `i <= values.length` allows index 5, which is beyond the array's existing elements.

Correct version:

```javascript
for (let i = 0; i < values.length; i++) {
  console.log(values[i]);
}
```

### Mutating the iterated array

```javascript
const numbers = [1, 2, 3, 4];
for (let i = 0; i < numbers.length; i++) {
  numbers.pop();
}
console.log(numbers); // [1, 2]
```

| Check | i | Array | length | Continue? |
|---|---:|---|---:|---|
| 1 | 0 | `[1,2,3,4]` | 4 | Yes |
| 2 | 1 | `[1,2,3]` | 3 | Yes |
| 3 | 2 | `[1,2]` | 2 | No |

The loop condition depends on `numbers.length`, which shrinks as the body executes. Mutation can change the number of iterations.

## 8.1.12 Loop invariants and correctness

A **loop invariant** is a property that remains true at a specified point in each iteration.

```javascript
const numbers = [3, 5, 7];
let total = 0;
for (let i = 0; i < numbers.length; i++) {
  total += numbers[i];
}
```

At the beginning of each condition check, `total` equals the sum of elements with indices smaller than `i`.

| i at condition | Elements already processed | total |
|---:|---|---:|
| 0 | None | 0 |
| 1 | 3 | 3 |
| 2 | 3, 5 | 8 |
| 3 | 3, 5, 7 | 15 |

### Three stages of an invariant argument

1. **Initialization:** Before the first iteration, `i=0` and `total=0`, matching the sum of no elements.
2. **Maintenance:** Processing `numbers[i]` adds exactly the next element to the total; the update advances `i`.
3. **Termination:** When `i === numbers.length`, all elements have been processed and `total` is their sum.

Correctness requires more than one successful test. Invariants help explain *why* an algorithm works across valid inputs.

## 8.1.13 Technical interview questions and answers

### Q1. What is the difference between repetition and iteration?

**Answer:** Repetition means doing work multiple times; iteration is one cycle of that repeated processing. A loop is a construct that can implement repetition.

### Q2. Can a `for` loop execute its body zero times?

**Answer:** Yes. If its condition is falsy before the first body execution, the body is skipped.

### Q3. Must a loop use a numeric counter?

**Answer:** No. Loops can use flags, sentinel values, iterators, or other state. `for...of` does not require an explicit numeric counter.

### Q4. What is an accumulator?

**Answer:** A variable that carries a result across iterations, such as a running sum, product, or concatenated string.

### Q5. Why can `i !== 10` cause nontermination?

**Answer:** If the update skips 10, equality is never reached. For example, starting at 1 and adding 2 yields odd numbers, not 10.

### Q6. How many condition checks occur for five normal body executions?

**Answer:** Typically six: one before each body execution and a final failing check. `break`, exceptions, or other abrupt control flow can change this.

### Q7. What is an off-by-one error?

**Answer:** Processing one too many or one too few iterations, commonly from a mistaken inclusive/exclusive boundary.

### Q8. What is a loop invariant?

**Answer:** A property that holds at a specified point in each iteration and supports reasoning about correctness.

### Q9. Why is changing an array during iteration risky?

**Answer:** The array's length, indices, and elements may change while the loop runs, affecting the condition and which values are processed.

### Q10. Why can floating-point increments be problematic?

**Answer:** Many decimal fractions cannot be represented exactly in binary floating point. Repeated additions may cross a boundary unexpectedly.

### Q11. What is the difference between a counter and an accumulator?

**Answer:** A counter tracks a count or progression. An accumulator combines results. Both may be numeric, but their responsibilities differ.

### Q12. Why is `while (remaining)` not equivalent to `while (remaining > 0)`?

**Answer:** Any nonzero number is truthy, including negative numbers. `remaining > 0` specifically checks positivity.

### Q13. Why might `do...while` run when a `while` loop does not?

**Answer:** `do...while` checks the condition after the body, so it executes once on normal entry even when the condition is initially falsy.

### Q14. Why should a running product usually start at 1?

**Answer:** One is the multiplicative identity. Starting at zero makes all subsequent products zero.

### Q15. Does `const` make an array immutable?

**Answer:** No. It prevents reassignment of the binding, not mutation of the array object.

## 8.1.14 Coding exercises with worked solutions

### Exercise 1: Count from 1 through 10

**Task:** Print the integers 1 to 10.

```javascript
for (let number = 1; number <= 10; number++) {
  console.log(number);
}
```

- Initialize `number` to 1.
- Check `number <= 10` before each iteration.
- Print the current number.
- Increment by one.
- Exit when the number becomes 11.

**Output:** The numbers 1 through 10, one per line.

### Exercise 2: Sum the integers 1 through 5

```javascript
let total = 0;
for (let number = 1; number <= 5; number++) {
  total += number;
}
console.log(total); // 15
```

`total += number` is equivalent here to `total = total + number`.

| number | total before | total after |
|---:|---:|---:|
| 1 | 0 | 1 |
| 2 | 1 | 3 |
| 3 | 3 | 6 |
| 4 | 6 | 10 |
| 5 | 10 | 15 |

### Exercise 3: Count down from 5 to 1

```javascript
for (let number = 5; number >= 1; number--) {
  console.log(number);
}
```

- `>=` means greater than or equal to.
- `number--` decreases the numeric value by one.
- The body executes for 5, 4, 3, 2, and 1.
- The next check sees 0 and stops.

### Exercise 4: Count positive values

```javascript
const values = [-3, 5, 0, 12, -1, 7];
let count = 0;
for (const value of values) {
  if (value > 0) {
    count++;
  }
}
console.log(count); // 3
```

| value | `value > 0` | count |
|---:|---|---:|
| -3 | false | 0 |
| 5 | true | 1 |
| 0 | false | 1 |
| 12 | true | 2 |
| -1 | false | 2 |
| 7 | true | 3 |

### Exercise 5: Find the largest number

```javascript
const numbers = [12, 45, 8, 67, 23];
let largest = numbers[0];
for (let i = 1; i < numbers.length; i++) {
  if (numbers[i] > largest) {
    largest = numbers[i];
  }
}
console.log(largest); // 67
```

Start with the first actual value, not zero, so an all-negative array can still be handled correctly.

| i | current | largest before | largest after |
|---:|---:|---:|---:|
| 1 | 45 | 12 | 45 |
| 2 | 8 | 45 | 45 |
| 3 | 67 | 45 | 67 |
| 4 | 23 | 67 | 67 |

**Precondition:** The array is nonempty and contains comparable numeric values.

### Exercise 6: Predict the output

```javascript
let count = 0;
for (let i = 0; i < 5; i++) {
  if (i % 2 === 0) {
    count += 2;
  } else {
    count += 1;
  }
}
console.log(count);
```

**Answer:** `8`.

- `%` returns the remainder.
- `i % 2 === 0` tests whether `i` is even.
- Even values add 2; odd values add 1.

| i | Even? | Addition | count after |
|---:|---|---:|---:|
| 0 | Yes | 2 | 2 |
| 1 | No | 1 | 3 |
| 2 | Yes | 2 | 5 |
| 3 | No | 1 | 6 |
| 4 | Yes | 2 | 8 |

### Exercise 7: Repair an infinite loop

**Bug (do not execute without interruption controls):**

```javascript
let number = 1;
while (number <= 10) {
  console.log(number);
  number--;
}
```

**Explanation:** The condition expects `number` eventually to exceed 10, but the update decreases it.

**Correction:**

```javascript
let number = 1;
while (number <= 10) {
  console.log(number);
  number++;
}
```

### Exercise 8: Calculate the average

```javascript
const scores = [60, 80, 70, 90];
let total = 0;
for (const score of scores) {
  total += score;
}
const average = total / scores.length;
console.log(average); // 75
```

Sum = 300; number of values = 4; average = 300 / 4 = 75. For a reusable function, explicitly decide what should happen when the array is empty.

## 8.1.15 Extended practice and review

### Predict before running

For each example, write down the initial state, condition result, body effect, update, and stopping state.

**Challenge A**

```javascript
let x = 2;
while (x < 10) {
  x *= 2;
}
console.log(x);
```

**Answer:** 16. The values progress 2 -> 4 -> 8 -> 16, and 16 is not less than 10.

**Challenge B**

```javascript
let sum = 0;
for (let i = 3; i >= 1; i--) {
  sum += i;
}
console.log(sum);
```

**Answer:** 6. Add 3, then 2, then 1.

**Challenge C**

```javascript
const items = [];
let visits = 0;
for (const item of items) {
  visits++;
}
console.log(visits);
```

**Answer:** 0. An empty array yields no elements to `for...of`.

**Challenge D**

```javascript
let attempts = 0;
do {
  attempts++;
} while (attempts < 0);
console.log(attempts);
```

**Answer:** 1. The `do` body executes before the first condition check.

**Challenge E**

```javascript
let count = 0;
for (let i = 0; i < 6; i += 2) {
  count++;
}
console.log(count);
```

**Answer:** 3. The counter `i` takes the values 0, 2, and 4.

### Debugging checklist

- [ ] Have I identified the intended number of iterations?
- [ ] Is the initial state correct?
- [ ] Is the boundary inclusive or exclusive?
- [ ] Does the update move toward termination?
- [ ] Can the condition become falsy?
- [ ] Am I using an accumulator initialized to the right identity value?
- [ ] Are input values of the expected type?
- [ ] Can the collection change during iteration?
- [ ] Does an empty input behave sensibly?
- [ ] Have I traced at least one boundary case manually?

### Summary

A loop is a control-flow construct for repetition. An iteration is one execution cycle. Effective loop design depends on correctly defining initial state, continuation, repeated work, state transition, and termination. Counters, accumulators, and flags represent different forms of state. Boundary conditions, truthiness, data mutation, and numeric precision can all affect correctness. Tracing and invariants provide a disciplined way to understand and validate iterative algorithms.

## Navigation

- **Previous:** [Topic 7 - Conditional Statements](07-conditional-statements-and-decision-making.md)
- **Current:** Lesson 8.1, Part 1 - Foundations of Loops and Iteration
- **Next:** Lesson 8.1, Part 2 - Execution Contexts, Lexical Environments, and Loop Control Mechanics (forthcoming)
- **Topic overview:** [Loops and Iteration](08-loops-and-iteration.md)

---

*End of Part 1. This file is an independently readable study chapter and will be incorporated into the complete 4,000+ line Lesson 8.1 textbook as subsequent parts are developed.*
