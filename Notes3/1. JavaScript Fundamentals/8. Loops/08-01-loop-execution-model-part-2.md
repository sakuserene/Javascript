# JavaScript Mastery Course - Topic 8: Loops and Iteration

# Lesson 8.1 - Part 2: The JavaScript Loop Execution Model

**Focus:** Execution contexts, lexical environments, scope, per-iteration bindings, closures, execution order, and abrupt control flow.

**Prerequisite:** [Part 1 - Foundations of Loops and Iteration](08-01-loops-foundations-part-1.md)

**Next:** Part 3 - Loop Correctness, Termination, Complexity, and Advanced Debugging (forthcoming).

## Table of Contents

- [Learning objectives](#learning-objectives)
- [8.1.15 Execution contexts](#8115-execution-contexts)
- [8.1.16 Lexical environments and identifier resolution](#8116-lexical-environments-and-identifier-resolution)
- [8.1.17 Block scope and loop scope](#8117-block-scope-and-loop-scope)
- [8.1.18 var, let, and const in loops](#8118-var-let-and-const-in-loops)
- [8.1.19 Per-iteration lexical environments](#8119-per-iteration-lexical-environments)
- [8.1.20 Closures inside loops](#8120-closures-inside-loops)
- [8.1.21 Exact for loop execution order](#8121-exact-for-loop-execution-order)
- [8.1.22 while and do...while execution order](#8122-while-and-dowhile-execution-order)
- [8.1.23 Normal and abrupt completion](#8123-normal-and-abrupt-completion)
- [8.1.24 break, continue, and labels](#8124-break-continue-and-labels)
- [8.1.25 return, throw, and try...finally](#8125-return-throw-and-tryfinally)
- [8.1.26 Debugging loop execution](#8126-debugging-loop-execution)
- [8.1.27 Interview questions with answers](#8127-interview-questions-with-answers)
- [8.1.28 Coding exercises with solutions](#8128-coding-exercises-with-solutions)
- [Glossary](#glossary)
- [Review and navigation](#review-and-navigation)

## Learning objectives

After completing Part 2, you should be able to:

1. Distinguish an execution context from a lexical environment.
2. Explain identifier resolution and variable shadowing.
3. Predict the scope of `var`, `let`, and `const` in different loops.
4. Explain why closures created in `for (var ...)` and `for (let ...)` often behave differently.
5. Trace initialization, testing, body execution, and updates precisely.
6. Explain how `break`, `continue`, `return`, and `throw` change control flow.
7. Predict when `finally` executes and why it can override a return.
8. Debug shared bindings, shadowing, skipped updates, and early termination.

---

## 8.1.15 Execution contexts

### 8.1.15.1 Definition

An **execution context** is an ECMAScript specification mechanism that represents the state needed to execute code. It includes information about the current lexical environment and other execution state. It is not a JavaScript object you can directly inspect as an ordinary program value.

Questions an execution context helps the engine answer include: What code is running? What binding does a name refer to? Which function invocation is active? Where does control return after a function call?

### 8.1.15.2 A loop is not automatically a new function call

```javascript
let counter = 0;

for (let i = 0; i < 3; i++) {
  counter += 1;
}

console.log(counter); // 3
```

The loop normally runs within the surrounding execution context. Depending on its syntax, new lexical environments and bindings may be established during iterations. This is **not** the same as creating a new function execution context for every iteration.

Token-by-token:

- `let` introduces a mutable lexical binding.
- `counter` names a binding in the surrounding scope.
- `=` initializes it with the numeric value `0`.
- `for` introduces an iteration statement.
- `i` is the counter identifier, scoped to the loop because of `let` in the header.
- `<` compares `i` with the numeric boundary `3`.
- `i++` increments `i` after a normal iteration, producing the old value as the expression result.
- `counter += 1` reads the current counter, adds one, and assigns the result.

Trace:

| Iteration | `i` | `counter` before | `counter` after |
|---|---:|---:|---:|
| 1 | 0 | 0 | 1 |
| 2 | 1 | 1 | 2 |
| 3 | 2 | 2 | 3 |

### 8.1.15.3 Function calls create function execution contexts

```javascript
function double(number) {
  return number * 2;
}

for (let i = 1; i <= 3; i++) {
  console.log(double(i));
}
```

Expected output:

```text
2
4
6
```

Each invocation of `double` establishes a function execution context with its own parameter binding named `number`. The `for` loop controls when these calls occur. The loop itself does not become a separate function call.

| Body iteration | `i` passed | `number` in called function | Returned |
|---|---:|---:|---:|
| 1 | 1 | 1 | 2 |
| 2 | 2 | 2 | 4 |
| 3 | 3 | 3 | 6 |

### 8.1.15.4 Global code versus modules

Top-level declarations behave differently depending on the host and whether the code is a classic script or an ECMAScript module. In particular, do not assume that a top-level `let` creates a property on `globalThis`. Scope explanations should distinguish the language rules from browser- or Node.js-specific behavior.

---

## 8.1.16 Lexical environments and identifier resolution

### 8.1.16.1 Definition

A **lexical environment** is a specification mechanism associating identifier bindings with lexical scope and an outer environment. It need not be implemented as a literal dictionary or JavaScript object.

```javascript
let outside = 10;

{
  let inside = 20;
  console.log(outside); // 10
  console.log(inside);  // 20
}

// console.log(inside); // ReferenceError
```

The inner block can resolve `outside` through the outer environment. The binding `inside` is not accessible outside its block.

### 8.1.16.2 Shadowing

```javascript
let value = 100;

{
  let value = 200;
  console.log(value); // 200
}

console.log(value); // 100
```

The inner declaration creates a **new binding** named `value`. It shadows the outer binding rather than overwriting it.

### 8.1.16.3 Reassignment is different

```javascript
let count = 5;

{
  count = 10;
}

console.log(count); // 10
```

There is no inner `let count`. Identifier resolution finds the outer binding, so the assignment changes that binding.

### 8.1.16.4 Trace: shadowing versus reassignment

| Code inside block | New inner binding? | Outer value afterward |
|---|---|---:|
| `let count = 10` | Yes | 5 |
| `count = 10` | No | 10 |

### 8.1.16.5 Temporal dead zone

```javascript
{
  // console.log(score); // ReferenceError
  let score = 80;
  console.log(score); // 80
}
```

A lexical binding exists for the block but cannot be accessed before initialization. The period before initialization is commonly called the **temporal dead zone** (TDZ). This is different from an identifier that has never been declared in any accessible scope.

---

## 8.1.17 Block scope and loop scope

### 8.1.17.1 Body block scope

```javascript
for (let i = 0; i < 3; i++) {
  let message = "Iteration " + i;
  console.log(message);
}
```

Output:

```text
Iteration 0
Iteration 1
Iteration 2
```

The `message` binding belongs to the loop body's block scope. The body is entered repeatedly; its lexical declarations are instantiated for each entry.

### 8.1.17.2 Loop header scope

```javascript
for (let i = 0; i < 3; i++) {
  console.log(i);
}

// console.log(i); // ReferenceError
```

`i` is available within the loop but not afterward.

### 8.1.17.3 An externally declared counter

```javascript
let i = 0;

for (; i < 3; i++) {
  console.log(i);
}

console.log("Final:", i); // Final: 3
```

The first header clause is omitted; the external `i` binding remains accessible afterward.

### 8.1.17.4 Common mistake: inner accumulator shadows outer one

```javascript
let total = 0;

for (let i = 1; i <= 3; i++) {
  let total = i;
}

console.log(total); // 0
```

Fix:

```javascript
let total = 0;

for (let i = 1; i <= 3; i++) {
  total += i;
}

console.log(total); // 6
```

The fix removes the inner declaration, so the assignment updates the outer accumulator.

---

## 8.1.18 var, let, and const in loops

### 8.1.18.1 `var` and visibility

```javascript
for (var i = 0; i < 3; i++) {
  console.log(i);
}

console.log("After loop:", i);
```

Output:

```text
0
1
2
After loop: 3
```

`var` is scoped to the containing function or script variable environment, not to the loop block.

### 8.1.18.2 `let` and visibility

```javascript
for (let i = 0; i < 3; i++) {
  console.log(i);
}

// console.log(i); // ReferenceError
```

The loop's `let` binding is not accessible after the loop.

### 8.1.18.3 `const` in `for...of`

```javascript
const names = ["Ada", "Ben", "Cara"];

for (const name of names) {
  console.log(name);
}
```

Output:

```text
Ada
Ben
Cara
```

The `for...of` statement establishes an appropriate binding for each element. The binding need not be reassigned within the iteration.

### 8.1.18.4 `const` in a counted loop

```javascript
for (const i = 0; i < 3; i++) {
  console.log(i);
}
```

The first body execution prints `0`. The update then attempts to change the constant binding and throws a `TypeError`. Use `let` for a mutable counted-loop variable.

### 8.1.18.5 Comparison

| Characteristic | `var` | `let` | `const` |
|---|---|---|---|
| Block scoped | No | Yes | Yes |
| Can reassign binding | Yes | Yes | No |
| Same-scope redeclaration | Generally yes | No | No |
| Lexical TDZ | No, unlike `let` | Yes | Yes |
| Typical counted loop counter | Possible | Preferred | Usually unsuitable |
| Typical `for...of` binding | Possible | Yes | Yes |

Important: `const` prevents **reassignment of a binding**, not mutation of an object referenced by that binding.

---

## 8.1.19 Per-iteration lexical environments

### 8.1.19.1 The shared `var` binding

```javascript
const functions = [];

for (var i = 0; i < 3; i++) {
  functions.push(function () {
    return i;
  });
}

console.log(functions[0]()); // 3
console.log(functions[1]()); // 3
console.log(functions[2]()); // 3
```

The functions are **created** during the loop but **called** afterward. All reference the same `var i` binding, whose final value is `3`.

Timeline:

```text
i = 0 --> create function A referencing shared i
i = 1 --> create function B referencing shared i
i = 2 --> create function C referencing shared i
i = 3 --> loop ends
call A --> 3
call B --> 3
call C --> 3
```

### 8.1.19.2 The per-iteration `let` bindings

```javascript
const functions = [];

for (let i = 0; i < 3; i++) {
  functions.push(function () {
    return i;
  });
}

console.log(functions[0]()); // 0
console.log(functions[1]()); // 1
console.log(functions[2]()); // 2
```

The standard `for` loop's per-iteration lexical environments allow functions created in different iterations to reference distinct `i` bindings.

### 8.1.19.3 A binding is not a frozen snapshot

```javascript
const readers = [];

for (let i = 0; i < 3; i++) {
  readers.push(() => i);
  i += 10;
}

console.log(readers[0]()); // 10
```

The function closes over the current iteration's binding. The body changes that binding from `0` to `10`. The loop then establishes the next iteration binding and performs the `i++` update, making the condition false. Therefore the saved function reads `10`, not `0`.

**Rule:** Closures capture bindings. A captured `let` binding can still change.

---

## 8.1.20 Closures inside loops

### 8.1.20.1 Closure fundamentals

A closure is a function together with access to the lexical environment in which it was created.

```javascript
function createCounter() {
  let count = 0;

  return function () {
    count++;
    return count;
  };
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```

The returned function retains access to `count` even after `createCounter()` has returned.

### 8.1.20.2 Factory functions in loops

```javascript
const multipliers = [];

for (let factor = 1; factor <= 3; factor++) {
  multipliers.push(function (value) {
    return value * factor;
  });
}

console.log(multipliers[0](10)); // 10
console.log(multipliers[1](10)); // 20
console.log(multipliers[2](10)); // 30
```

Each closure retains the appropriate `factor` binding.

### 8.1.20.3 Timers and callbacks

```javascript
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}
```

In a normal host with `setTimeout`, the callbacks log `0`, `1`, `2` after the synchronous loop completes. With `var`, they would log `3`, `3`, `3` because the binding is shared. `setTimeout` is a host-provided API, not part of the ECMAScript language itself.

### 8.1.20.4 Older workaround using an IIFE

```javascript
const functions = [];

for (var i = 0; i < 3; i++) {
  (function (savedValue) {
    functions.push(function () {
      return savedValue;
    });
  })(i);
}

console.log(functions.map(fn => fn())); // [0, 1, 2]
```

The immediately invoked function expression (IIFE) creates a separate `savedValue` parameter binding for each invocation.

---

## 8.1.21 Exact for loop execution order

### 8.1.21.1 Anatomy

```javascript
for (let i = 0; i < 3; i++) {
  console.log(i);
}
```

| Token | Role |
|---|---|
| `for` | Iteration statement keyword |
| `(` and `)` | Delimit the control header |
| `let` | Declare a mutable lexical binding |
| `i` | Counter identifier |
| `=` | Initialize with a value |
| `0` | Numeric literal |
| First `;` | End initialization clause |
| `<` | Less-than comparison |
| `3` | Exclusive upper bound |
| Second `;` | End condition clause |
| `i++` | Postfix increment expression |
| `{` and `}` | Delimit the body block |
| `console.log(i)` | Log current counter |

### 8.1.21.2 Normal lifecycle

```text
Initialize once
    |
Evaluate condition
    |-- falsy --> exit
    |
    `-- truthy --> execute body
                     |
                     `-- establish next iteration binding if applicable
                           |
                           `-- evaluate update --> evaluate condition again
```

### 8.1.21.3 Full trace

| Step | Action | `i` | Result |
|---:|---|---:|---|
| 1 | Initialization | 0 | Initialized |
| 2 | Condition | 0 | `0 < 3` true |
| 3 | Body | 0 | Print 0 |
| 4 | Update | 1 | Increment |
| 5 | Condition | 1 | `1 < 3` true |
| 6 | Body | 1 | Print 1 |
| 7 | Update | 2 | Increment |
| 8 | Condition | 2 | `2 < 3` true |
| 9 | Body | 2 | Print 2 |
| 10 | Update | 3 | Increment |
| 11 | Condition | 3 | `3 < 3` false |
| 12 | Exit | - | Loop complete |

### 8.1.21.4 Omitted clauses

```javascript
let i = 0;

for (;;) {
  console.log(i);
  i++;
  if (i === 3) break;
}
```

Output: `0`, `1`, `2` on separate lines. All three header clauses are optional; `break` supplies the exit.

### 8.1.21.5 Multiple variables

```javascript
for (let left = 0, right = 4;
     left < right;
     left++, right--) {
  console.log(left, right);
}
```

Output:

```text
0 4
1 3
```

The comma-separated initialization creates two bindings; the update clause changes both after each normal body execution.

---

## 8.1.22 while and do...while execution order

### 8.1.22.1 Pre-test `while`

```javascript
let count = 1;

while (count <= 3) {
  console.log(count);
  count++;
}
```

The condition is checked **before** every body execution. Output: `1`, `2`, `3`.

### 8.1.22.2 Post-test `do...while`

```javascript
let count = 1;

do {
  console.log(count);
  count++;
} while (count <= 3);
```

The body runs before the condition is checked. Output: `1`, `2`, `3`.

### 8.1.22.3 Condition counts

| Form | Normal body executions | Condition checks |
|---|---:|---:|
| `for` | 3 | 4 |
| `while` | 3 | 4 |
| `do...while` | 3 | 3 |

These counts assume ordinary completion and no early `break`, exception, or other abrupt exit.

### 8.1.22.4 Zero iterations versus one

```javascript
let count = 10;
while (count < 5) {
  console.log(count);
}
// No output.
```

```javascript
let count = 10;
do {
  console.log(count);
} while (count < 5);
// Prints 10 once.
```

---

## 8.1.23 Normal and abrupt completion

JavaScript's specification models statement execution using **completion records**. A completion may be normal or abrupt. This explains why some statements stop a loop, skip work, leave a function, or propagate an exception.

### 8.1.23.1 Normal completion

```javascript
for (let i = 0; i < 3; i++) {
  console.log(i);
}
```

Each body finishes normally, allowing the loop to proceed to its next-iteration work.

### 8.1.23.2 Abrupt completion with `break`

```javascript
for (let i = 0; i < 5; i++) {
  if (i === 2) break;
  console.log(i);
}
```

Output:

```text
0
1
```

The update expression is **not** executed after the `break` that exits the loop.

### 8.1.23.3 Abrupt completion summary

| Statement | Meaning |
|---|---|
| `break` | Exit targeted loop or switch |
| `continue` | Start next iteration of targeted loop |
| `return` | Leave enclosing function with optional result |
| `throw` | Raise an exception |

---

## 8.1.24 break, continue, and labels

### 8.1.24.1 `break`

```javascript
for (let number = 1; number <= 10; number++) {
  if (number === 4) break;
  console.log(number);
}
```

Output: `1`, `2`, `3` on separate lines. When the condition is true, the loop exits.

### 8.1.24.2 `continue` in a `for` loop

```javascript
for (let number = 1; number <= 5; number++) {
  if (number === 3) continue;
  console.log(number);
}
```

Output: `1`, `2`, `4`, `5`. The `for` update still runs for a normal `continue` targeting that loop.

### 8.1.24.3 `continue` can skip a `while` update

**Bug (do not run without an interrupt mechanism):**

```javascript
let number = 0;
while (number < 5) {
  if (number === 2) continue;
  console.log(number);
  number++;
}
```

After printing `0` and `1`, `number` remains `2` forever because the increment is skipped.

**Fix:**

```javascript
let number = 0;
while (number < 5) {
  number++;
  if (number === 2) continue;
  console.log(number);
}
```

Output: `1`, `3`, `4`, `5`.

### 8.1.24.4 Labeled break

```javascript
outer:
for (let row = 1; row <= 3; row++) {
  for (let column = 1; column <= 3; column++) {
    if (row === 2 && column === 2) break outer;
    console.log(row, column);
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

`break outer` exits the labeled outer loop; an unlabeled `break` would exit only the inner loop.

---

## 8.1.25 return, throw, and try...finally

### 8.1.25.1 `return` ends the function

```javascript
function findFirstEven(numbers) {
  for (const number of numbers) {
    if (number % 2 === 0) return number;
  }
  return null;
}

console.log(findFirstEven([3, 7, 10, 12])); // 10
```

The function returns at the first matching number. The later `12` is never examined.

### 8.1.25.2 `throw` propagates an exception

```javascript
function sumPositiveNumbers(numbers) {
  let total = 0;
  for (const number of numbers) {
    if (number < 0) throw new Error("Negative number encountered");
    total += number;
  }
  return total;
}
```

Unlike `break`, `throw` raises an exception that must be handled or it propagates out of the function.

### 8.1.25.3 `finally` runs during abrupt exits

```javascript
for (let i = 0; i < 3; i++) {
  try {
    if (i === 1) break;
    console.log("Body:", i);
  } finally {
    console.log("Finally:", i);
  }
}
```

Output:

```text
Body: 0
Finally: 0
Finally: 1
```

The `finally` block executes before the break completes.

### 8.1.25.4 `finally` can override a return

```javascript
function example() {
  try {
    return "First";
  } finally {
    return "Second";
  }
}

console.log(example()); // Second
```

A return from `finally` overrides the earlier return. Avoid this pattern in normal application code because it obscures control flow and can suppress errors.

---

## 8.1.26 Debugging loop execution

### Case 1: Shared bindings

```javascript
const callbacks = [];
for (var i = 0; i < 3; i++) callbacks.push(() => i);
console.log(callbacks.map(callback => callback())); // [3, 3, 3]
```

**Diagnosis:** The callbacks close over one shared `var i` binding.

**Fix:** Use `let i` in the header; the result becomes `[0, 1, 2]`.

### Case 2: Shadowed accumulator

```javascript
let total = 0;
for (let i = 1; i <= 3; i++) {
  let total = i;
}
console.log(total); // 0
```

**Diagnosis:** `let total` inside the body creates a separate binding.

**Fix:** Replace `let total = i` with `total += i` to obtain `6`.

### Case 3: Returning too early

```javascript
function countItems(items) {
  let count = 0;
  for (const item of items) {
    if (item === null) return count;
    count++;
  }
  return count;
}

console.log(countItems(["A", null, "B", "C"])); // 1
```

**Diagnosis:** `return` exits the function on the first `null`.

**Fix:** Use `continue` to skip nulls and count the remaining values; result `3`.

### General debugging checklist

1. Write down every binding and its scope.
2. Record each variable's value before the condition.
3. Evaluate the condition explicitly.
4. Identify which statements run in the body.
5. Record any `break`, `continue`, `return`, or `throw`.
6. Check whether the update executes.
7. Distinguish function creation from function invocation.
8. Determine which binding a closure reads when it eventually runs.
9. Check `finally` blocks for additional control-flow changes.
10. Test zero iterations, one iteration, and boundary cases.

---

## 8.1.27 Interview questions with answers

### Q1. Does each loop iteration create a new execution context?

**Answer:** Not generally. A loop executes within its surrounding execution context, although lexical environments and bindings may be created per iteration. Function calls made inside the loop establish function execution contexts.

### Q2. Why does `var` behave differently from `let` inside a loop?

**Answer:** `var` is function- or script-scoped and typically uses one shared binding for the counter. A standard `for` loop with `let` in the header can establish distinct per-iteration bindings.

### Q3. What is variable shadowing?

**Answer:** An inner declaration uses the same identifier as an outer declaration. References inside the inner scope resolve to the nearer binding, leaving the outer binding unchanged unless it is separately reassigned.

### Q4. What is the temporal dead zone?

**Answer:** The interval in a lexical binding's scope before it has been initialized. Access during that interval raises a `ReferenceError`.

### Q5. Why do callbacks created with `var` often return the final counter value?

**Answer:** They close over the same shared counter binding and are invoked after its value has changed.

### Q6. Does `continue` run the update expression of a standard `for` loop?

**Answer:** For an ordinary `continue` targeting that loop, yes. The loop performs its next-iteration processing and update before evaluating the condition again.

### Q7. How are `break` and `return` different?

**Answer:** `break` exits the targeted loop or switch. `return` exits the enclosing function, optionally producing a result.

### Q8. Does `finally` run when a `break` is executed inside `try`?

**Answer:** Yes. The `finally` block runs before control completes the transfer out of the `try` statement.

### Q9. Can `return` inside `finally` override a previous return?

**Answer:** Yes. An abrupt completion from `finally` can replace the previous return or exception.

### Q10. Why is changing a counter in the loop body risky?

**Answer:** It can invalidate assumptions about iteration count, skip values, change termination behavior, and make reasoning about the algorithm more difficult.

### Q11. Why might a closure return `10` rather than `0` even with `let`?

**Answer:** The captured binding can be mutated within the same iteration. `let` provides mutable per-iteration bindings, not immutable snapshots.

### Q12. Is a top-level `let` always a property of `globalThis`?

**Answer:** No. Top-level binding behavior depends on script/module context, and lexical declarations do not automatically become properties of the global object.

### Q13. Why can `continue` cause an infinite `while` loop?

**Answer:** If it skips the statement that updates the condition-controlling state, the condition can remain truthy indefinitely.

### Q14. What happens to a traditional `for` loop's update when `break` exits the loop?

**Answer:** It is skipped. The loop terminates directly.

### Q15. When should a `for...of` loop use `const`?

**Answer:** When the per-iteration binding itself does not need reassignment. Each iteration gets the current element binding, and `const` prevents reassignment within that iteration.

---

## 8.1.28 Coding exercises with solutions

### Exercise 1 - Capture iteration values

**Task:** Create three functions returning `10`, `20`, and `30`.

```javascript
const functions = [];
for (let i = 1; i <= 3; i++) {
  functions.push(() => i * 10);
}
console.log(functions[0]()); // 10
console.log(functions[1]()); // 20
console.log(functions[2]()); // 30
```

**Explanation:** Each function captures the corresponding per-iteration `i` binding; the multiplication occurs when the function is called.

### Exercise 2 - Stop at first negative number

```javascript
function sumUntilNegative(numbers) {
  let total = 0;
  for (const number of numbers) {
    if (number < 0) break;
    total += number;
  }
  return total;
}
console.log(sumUntilNegative([5, 10, 20, -3, 100])); // 35
```

**Trace:** Add `5` (total 5), add `10` (15), add `20` (35), encounter `-3`, exit loop, return 35. The `100` is never processed.

### Exercise 3 - Skip null entries

```javascript
function countNonNull(values) {
  let count = 0;
  for (const value of values) {
    if (value === null) continue;
    count++;
  }
  return count;
}
console.log(countNonNull([1, null, 3, null, 5])); // 3
```

**Explanation:** `continue` skips the increment for null values but does not end the loop.

### Exercise 4 - Return from nested loops

```javascript
function findPair(numbers, target) {
  for (let i = 0; i < numbers.length; i++) {
    for (let j = i + 1; j < numbers.length; j++) {
      if (numbers[i] + numbers[j] === target) {
        return [numbers[i], numbers[j]];
      }
    }
  }
  return null;
}
console.log(findPair([2, 7, 11, 15], 9)); // [2, 7]
```

**Explanation:** `return` exits the entire function, not only the inner loop. The nested-loop solution has quadratic worst-case time complexity, `O(n^2)`.

### Exercise 5 - Fix a closure bug

**Broken code:**

```javascript
const readers = [];
for (var i = 1; i <= 3; i++) readers.push(() => i);
console.log(readers.map(fn => fn())); // [4, 4, 4]
```

**Solution:**

```javascript
const readers = [];
for (let i = 1; i <= 3; i++) readers.push(() => i);
console.log(readers.map(fn => fn())); // [1, 2, 3]
```

**Explanation:** Replace the shared `var` binding with per-iteration `let` bindings.

### Exercise 6 - Skip an element without skipping the update

```javascript
let number = 0;
while (number < 5) {
  number++;
  if (number === 3) continue;
  console.log(number);
}
```

Output:

```text
1
2
4
5
```

**Explanation:** Increment before the conditional `continue`, so the loop always progresses toward termination.

### Exercise 7 - Break out of both loops

```javascript
let found = null;
outer:
for (let row = 0; row < 3; row++) {
  for (let col = 0; col < 3; col++) {
    if (row + col === 3) {
      found = [row, col];
      break outer;
    }
  }
}
console.log(found); // [1, 2]
```

**Explanation:** The first matching pair encountered in row-major order is `(1, 2)`. `break outer` exits both nested loops.

### Exercise 8 - Explain `finally` with early return

```javascript
function inspect(values) {
  for (const value of values) {
    try {
      if (value === 0) return "zero";
    } finally {
      console.log("checked", value);
    }
  }
  return "missing";
}
console.log(inspect([2, 0, 3]));
```

Output:

```text
checked 2
checked 0
zero
```

**Explanation:** `finally` executes for each entered `try`, including before the return at `value === 0`. The final element is never examined.

### Exercise 9 - Count without accidental shadowing

```javascript
function sumNumbers(numbers) {
  let total = 0;
  for (const number of numbers) {
    total += number;
  }
  return total;
}
console.log(sumNumbers([4, 6, 8])); // 18
```

**Explanation:** The loop updates the one accumulator declared in the function; it does not redeclare `total` inside the body.

### Exercise 10 - Predict the value of a mutated captured binding

```javascript
const functions = [];
for (let i = 0; i < 3; i++) {
  functions.push(() => i);
  if (i === 0) i += 1;
}
console.log(functions.map(fn => fn())); // [1, 2]
```

**Explanation:** On the first iteration the closure captures a binding initially `0`, then the body changes it to `1`. The update advances the next iteration's binding to `2`. On the second body execution the closure captures `2`; the next update reaches `3`, ending the loop. Thus only two functions are created.

---

## Glossary

| Term | Meaning |
|---|---|
| Execution context | Specification state for executing code |
| Lexical environment | Mechanism for resolving lexical bindings |
| Binding | Association of an identifier with a stored value or state |
| Shadowing | Inner binding hides an outer binding with the same name |
| TDZ | Period before a lexical binding is initialized |
| Closure | Function with access to its creation environment |
| Per-iteration binding | Distinct binding associated with a particular loop iteration |
| Normal completion | Execution finishes without abrupt control transfer |
| Abrupt completion | Execution transfers due to break, continue, return, or throw |
| IIFE | Immediately invoked function expression |
| Short-circuit | Evaluation skips later operands when earlier result determines outcome |

## Review and navigation

You should now be able to explain **why** a loop behaves a particular way, not merely reproduce its syntax. Review the closure examples and the `continue` bug until you can trace them without running the code.

- **Previous:** [Part 1 - Foundations of Loops and Iteration](08-01-loops-foundations-part-1.md)
- **Current:** Part 2 - The JavaScript Loop Execution Model
- **Next:** Part 3 - Loop Correctness, Termination, Complexity, and Advanced Debugging (forthcoming)

> This is Part 2 of the ongoing comprehensive Lesson 8.1 textbook. It is not the completed 4,000+ line combined chapter.
