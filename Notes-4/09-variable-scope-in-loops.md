# Lesson 09 - Variable Scope in Loops

*JavaScript Plain Loops*

Previous: [Controlling Loops](08-controlling-loops.md) | Curriculum: Plain Loops | Next: Conditions Inside Loops (planned)

**Filename:** `09-variable-scope-in-loops.md`

## Table of contents

1. [Introduction](#1-introduction)
2. [Learning objectives](#2-learning-objectives)
3. [Understanding variable scope](#3-understanding-variable-scope)
4. [Block scope](#4-block-scope)
5. [Function and global scope](#5-function-and-global-scope)
6. [Variable shadowing](#6-variable-shadowing)
7. [Temporal dead zone](#7-temporal-dead-zone)
8. [Variable lifetime](#8-variable-lifetime)
9. [Per-iteration bindings](#9-per-iteration-bindings)
10. [Closures inside loops](#10-closures-inside-loops)
11. [Choosing let, const and var](#11-choosing-let-const-and-var)
12. [Practical applications](#12-practical-applications)
13. [Common mistakes and edge cases](#13-common-mistakes-and-edge-cases)
14. [Best practices](#14-best-practices)
15. [Key takeaways](#15-key-takeaways)
16. [Practice exercises](#16-practice-exercises)

## 1. Introduction

Variables are essential to loop programming. They control repetition, store intermediate calculations, accumulate results and preserve information between iterations.

However, declaring a variable in different locations can significantly change how a loop behaves.

Consider these two examples:

```javascript
let i = 0;

for (; i < 3; i++) {
  console.log(i);
}

console.log(i);
```

Output:

```text
0
1
2
3
```

Now consider:

```javascript
for (let i = 0; i < 3; i++) {
  console.log(i);
}

console.log(i);
```

Observable behavior:

```text
0
1
2
ReferenceError
```

Both loops produce the same sequence, but their variables have different scopes.

The first variable is accessible after the loop, while the second belongs to the loop's lexical scope.

Variable declarations also influence whether information persists between iterations, whether nested loops interfere with one another and whether functions created inside loops capture the expected values.

Understanding these concepts will help you write predictable, maintainable loops and avoid subtle JavaScript bugs.

This lesson covers the complete Variable Scope in Loops curriculum branch. Run each example independently.

## 2. Learning objectives

- Identify lexical, block, function and global scope.
- Choose declaration locations based on visibility and persistence.
- Explain shadowing, hoisting and the temporal dead zone.
- Distinguish variable lifetime from scope.
- Trace per-iteration bindings and closures.
- Choose appropriate uses of `let`, `const` and `var`.

## 3. Understanding variable scope

Scope determines where an identifier can be resolved. A binding associates a name with a value. JavaScript uses lexical scope: lookup follows the nesting of the source code, starting with the nearest enclosing scope.

```javascript
const factor = 10;
for (let i = 1; i <= 3; i++) {
  const result = i * factor;
  console.log(result);
}
```

Output:

```text
10
20
30
```

The body can access its own `result`, the loop's `i` and the enclosing `factor`. The enclosing scope cannot access the inner `result` or `i`.

## 4. Block scope

`let` and `const` are block-scoped. A braced loop body creates a new lexical environment each time it is entered. A traditional `for` header with a lexical declaration also introduces a loop scope.

```javascript
for (let i = 1; i <= 3; i++) {
  let temporary = 0;
  temporary += i;
  console.log(temporary);
}
```

Output:

```text
1
2
3
```

`temporary` is initialized anew on each entry. It does not accumulate across iterations.

A body-local variable is not visible in the header or after the body. This applies to `while` and `do...while` too. In particular, a `do...while` condition cannot access a binding declared only inside its braced body.

## 5. Function and global scope

`var` is function-scoped rather than block-scoped. A `var` declaration inside a loop does not create a fresh binding on each iteration.

```javascript
function run() {
  for (var i = 0; i < 3; i++) {
    var square = i * i;
  }
  console.log(i, square);
}
run();
```

Output:

```text
3 4
```

Both bindings remain accessible throughout the function. They are not accessible outside it unless another binding exists there.

At the top level of a classic browser script, `var` generally creates a global-object property, while `let` and `const` create global lexical bindings rather than global-object properties. In ES modules, top-level declarations are module-scoped. Node.js CommonJS also has a module wrapper. Do not assume every top-level declaration is global.

## 6. Variable shadowing

Shadowing occurs when an inner binding has the same name as an outer binding. Lookup resolves the inner binding without changing the outer one.

```javascript
let i = 100;
for (let i = 1; i <= 2; i++) {
  console.log(i);
}
console.log(i);
```

Output:

```text
1
2
100
```

Nested loops can also use separate bindings with identical names, but descriptive names such as `row` and `col` are clearer. Reusing an existing variable without declaring a new binding is assignment, not shadowing.

## 7. Temporal dead zone

A lexical binding exists from the beginning of its scope but cannot be accessed before initialization. This interval is called the temporal dead zone (TDZ).

```javascript
let value = 10;
for (let i = 0; i < 1; i++) {
  console.log(value); // ReferenceError
  let value = 20;
}
```

The inner declaration shadows the outer binding throughout the body. The lookup does not fall back to 10. Even `typeof value` throws while this binding is in its TDZ.

By contrast, `var` bindings are initialized to `undefined` when their enclosing execution context is instantiated; their initializer assignments run later. Hoisting does not physically move source code.

```javascript
function run() {
  console.log(value);
  for (let i = 0; i < 1; i++) {
    var value = 20;
  }
  console.log(value);
}
run();
```

Output:

```text
undefined
20
```

## 8. Variable lifetime

Scope concerns visibility; lifetime concerns how long a binding and its associated state remain available. Closures can retain bindings beyond the execution of their original block or function.

```javascript
let total = 0;
for (let i = 1; i <= 3; i++) {
  const square = i * i;
  total += square;
  console.log(square, total);
}
```

Output:

```text
1 1
4 5
9 14
```

| Iteration | square | Total before | Total after |
|---:|---:|---:|---:|
| 1 | 1 | 0 | 1 |
| 2 | 4 | 1 | 5 |
| 3 | 9 | 5 | 14 |

The outer accumulator persists; the body-local binding is fresh each time. Declaring `total` inside the body would reset it. Leaving a scope does not guarantee immediate garbage collection.

## 9. Per-iteration bindings

A traditional `for` loop with a `let` declaration in its initialization clause creates separate per-iteration bindings. The next iteration's binding is initialized from the preceding binding before the update expression runs. This is different from repeatedly assigning one externally declared variable.

```javascript
const readers = [];
for (let i = 0; i < 3; i++) {
  readers.push(() => i);
}
console.log(readers.map(read => read()));
```

Output (array formatting varies):

```text
[0, 1, 2]
```

| Body entry | Captured binding | Value when called later |
|---:|---|---:|
| 1 | First iteration's i | 0 |
| 2 | Second iteration's i | 1 |
| 3 | Third iteration's i | 2 |

The final failed check uses a later binding with value 3. It does not overwrite the bindings captured by the three functions.

## 10. Closures inside loops

A closure is a function together with access to its enclosing lexical environment. Closures capture bindings, not frozen copies of values.

### 10.1 Shared var binding

```javascript
const readers = [];
for (var i = 0; i < 3; i++) {
  readers.push(() => i);
}
console.log(readers.map(read => read()));
```

Output:

```text
[3, 3, 3]
```

All functions refer to the same binding. When invoked after termination, they observe its final value. An externally declared `let i` used in the header without a new declaration has the same shared-binding behavior.

### 10.2 Capturing a body-local binding

```javascript
const readers = [];
let i = 0;
while (i < 3) {
  const current = i;
  readers.push(() => current);
  i++;
}
console.log(readers.map(read => read()));
```

Output:

```text
[0, 1, 2]
```

Each body entry creates a fresh `current` binding. This works in all three traditional loop types. The `map` calls in these examples merely invoke the stored functions for demonstration; array iteration methods are outside this curriculum.

## 11. Choosing let, const and var

| Declaration | Scope | Reassignment | Typical use |
|---|---|---|---|
| `let` | Lexical/block | Allowed | Changing counters and accumulators |
| `const` | Lexical/block | Not allowed | Stable references and intermediate values |
| `var` | Function or applicable top-level scope | Allowed | Understanding legacy code |

`const` prevents reassignment of the binding, not mutation of an object it references.

```javascript
const results = [];
for (let i = 1; i <= 3; i++) {
  const square = i * i;
  results.push(square);
}
console.log(results);
```

Output:

```text
[1, 4, 9]
```

A traditional `for (const i = 0; i < 3; i++)` enters the body once and then throws a TypeError at the attempted update, assuming normal body completion. Use `let` for a changing counter.

## 12. Practical applications

### 12.1 Independent nested counters

```javascript
for (let row = 1; row <= 2; row++) {
  for (let col = 1; col <= 3; col++) {
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
2 2
2 3
```

The inner counter is initialized for each outer iteration. Distinct names communicate independent responsibilities.

### 12.2 Creating deferred tasks

```javascript
const tasks = [];
for (let i = 1; i <= 3; i++) {
  tasks.push(() => console.log("Task", i));
}
for (let index = 0; index < tasks.length; index++) {
  tasks[index]();
}
```

Output:

```text
Task 1
Task 2
Task 3
```

The first loop creates functions; the second invokes them. Separate bindings preserve each task's number. This is a synchronous demonstration of deferred execution.

## 13. Common mistakes and edge cases

- Declaring an accumulator inside the body resets it on each entry.
- Reading a header-local `let` after the loop causes a ReferenceError if no outer binding exists.
- Reusing an outer counter in a nested loop without a new declaration changes the outer state.
- Reading a shadowing lexical binding before initialization causes a TDZ error.
- Capturing a shared counter in multiple closures can produce identical final values.
- Assigning an undeclared identifier throws in strict mode; sloppy scripts may accidentally create global properties. Always declare variables.

## 14. Best practices

- Use the narrowest scope that satisfies the task.
- Prefer `const` for bindings that do not need reassignment and `let` for changing state.
- Declare accumulators outside the body when results must persist.
- Use distinct, descriptive names in nested loops.
- Avoid `var` in new code unless a specific compatibility requirement demands it.
- Check whether deferred functions capture separate or shared bindings.

## 15. Key takeaways

- Scope determines identifier visibility; lifetime determines persistence.
- `let` and `const` are lexically scoped; `var` is not block-scoped.
- Shadowing hides an outer binding without changing it.
- The TDZ prevents access to uninitialized lexical bindings.
- Header-declared `let` in traditional `for` loops provides per-iteration bindings.
- Closures retain access to bindings, which may outlive their original execution scope.

## 16. Practice exercises

1. Predict the output of a loop with an outer `i = 50` and an inner `let i` counting from 1 through 3.
2. Compare the visibility of `var` and `let` counters after a loop inside a function.
3. Explain why an accumulator declared inside a body fails to preserve a running total. Correct it.
4. Write a loop that calculates squares using a body-local constant and an outer results array.
5. Create a shadowing example that produces a TDZ error. Correct it in two ways.
6. Trace three functions capturing a `var` counter and predict their results after termination.
7. Repeat exercise 6 using a header-declared `let` counter.
8. Repeat exercise 7 using an external `let` counter and a fresh body-local constant.
9. Write nested loops using independent row and column counters. Explain their scopes.
10. Explain why a body-local variable cannot be used in the trailing condition of `do...while`.
11. Create five deferred functions that each return a different square.
12. Explain why pushing into a `const` array is allowed but assigning a new array to its binding is not.

---

Previous: [Controlling Loops](08-controlling-loops.md) | Curriculum: Plain Loops | Next: Conditions Inside Loops (planned)
