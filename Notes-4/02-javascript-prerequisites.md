# Lesson 02 - JavaScript Prerequisites

*JavaScript Plain Loops*

Previous: [Loop Basics](01-loop-basics.md) | Curriculum: Plain Loops | Next: The for Loop (planned)

Markdown filenames will use lowercase ASCII characters, hyphens between words, sequential lesson numbers and the `.md` extension, following GitHub-friendly naming conventions.

**Filename:** `02-javascript-prerequisites.md`

## Table of contents

1. [Introduction](#1-introduction)
2. [Learning objectives](#2-learning-objectives)
3. [Variables and assignment](#3-variables-and-assignment)
4. [Operators used in loops](#4-operators-used-in-loops)
5. [Conditions and Boolean conversion](#5-conditions-and-boolean-conversion)
6. [Statements and blocks](#6-statements-and-blocks)
7. [Combining the concepts](#7-combining-the-concepts)
8. [Common mistakes](#8-common-mistakes)
9. [Key takeaways](#9-key-takeaways)
10. [Practice exercises](#10-practice-exercises)

## 1. Introduction

Loops depend on ordinary JavaScript expressions and statements. A counter stores progress, an operator changes its value, and a condition decides whether execution should continue. Understanding these foundations makes loops predictable rather than something you write by trial and error.

This lesson covers the entire JavaScript Prerequisites branch, focusing on concepts needed for loops rather than the entire JavaScript language. Run each example independently.

## 2. Learning objectives

- Choose appropriate variable declarations and maintain changing state.
- Use arithmetic, assignment, comparison and logical operators.
- Predict prefix and postfix expression values.
- Evaluate truthy, falsy and compound conditions.
- Understand short-circuit evaluation and operator precedence.
- Construct statement blocks and recognize accidental empty bodies.

## 3. Variables and assignment

### 3.1 Declaring variables with let

A variable is a named binding to a value. Declaration introduces the binding, initialization gives it its initial value, and reassignment replaces that value. Use `let` when reassignment is required.

```javascript
let count = 0;
count = count + 1;
count = count + 1;
console.log(count);
```

Output:

```text
2
```

The right side of an assignment is evaluated before its result is stored in the variable. Thus `count = count + 1` is an update, not a mathematical equation.

### 3.2 Declaring constants with const

Use `const` when the binding should not be reassigned. It requires an initializer in an ordinary declaration.

```javascript
const limit = 3;
for (let i = 1; i <= limit; i++) {
  console.log(i);
}
```

Output:

```text
1
2
3
```

The limit remains unchanged, but the counter changes. A constant binding does not make an object or array immutable.

```javascript
const values = [];
values.push(10);
values.push(20);
console.log(values.length);
```

Output:

```text
2
```

The array changes without reassigning `values`.

### 3.3 Declaring variables with var

`var` is an older declaration form. Unlike `let` and `const`, it is not block-scoped. Inside a function it is function-scoped.

```javascript
function demonstrate() {
  for (var i = 0; i < 3; i++) {
    // Empty block
  }
  console.log(i);
}
demonstrate();
```

Output:

```text
3
```

Replacing `var` with `let` makes the final access to `i` throw a `ReferenceError`. Prefer `let` and `const` in modern code. We will examine scope and per-iteration bindings in a later lesson.

## 4. Operators used in loops

### 4.1 Arithmetic operators

| Operator | Meaning | Example | Result |
|---|---|---|---|
| `+` | Addition | `5 + 2` | 7 |
| `-` | Subtraction | `5 - 2` | 3 |
| `*` | Multiplication | `5 * 2` | 10 |
| `/` | Division | `5 / 2` | 2.5 |
| `%` | Remainder | `5 % 2` | 1 |
| `**` | Exponentiation | `5 ** 2` | 25 |

Arithmetic can generate values or control progression.

```javascript
for (let i = 1; i <= 3; i++) {
  console.log(i * 5);
}
```

Output:

```text
5
10
15
```

The counter increases by one; the calculated output increases by five. These are different operations.

### 4.2 The remainder operator

The remainder operator returns the remainder after division. For integer operands, a remainder of zero indicates divisibility by a nonzero divisor.

```javascript
for (let i = 1; i <= 6; i++) {
  if (i % 2 === 0) {
    console.log(i);
  }
}
```

Output:

```text
2
4
6
```

| i | i % 2 | Is remainder zero? | Printed |
|---:|---:|---|---|
| 1 | 1 | false | No |
| 2 | 0 | true | Yes |
| 3 | 1 | false | No |
| 4 | 0 | true | Yes |
| 5 | 1 | false | No |
| 6 | 0 | true | Yes |

JavaScript remainder follows the sign of the dividend: `-5 % 2` is `-1`. To test odd integers, use `n % 2 !== 0`, not `n % 2 === 1`. For Number operands, remainder by zero produces `NaN`.

### 4.3 Assignment and compound assignment

| Expression | Equivalent for a simple numeric variable |
|---|---|
| `x += 2` | `x = x + 2` |
| `x -= 2` | `x = x - 2` |
| `x *= 2` | `x = x * 2` |
| `x /= 2` | `x = x / 2` |
| `x %= 2` | `x = x % 2` |
| `x **= 2` | `x = x ** 2` |

```javascript
let total = 0;
for (let i = 1; i <= 3; i++) {
  total += i;
  console.log(total);
}
```

Output:

```text
1
3
6
```

| i | Total before | Total after |
|---:|---:|---:|
| 1 | 0 | 1 |
| 2 | 1 | 3 |
| 3 | 3 | 6 |

Compound assignment updates the existing value rather than replacing it with the current input alone. Note that `+=` can concatenate strings: `"2" + 1` produces `"21"`, not `3`.

### 4.4 Increment and decrement

`++` increases a numeric value by one and `--` decreases it by one. Prefix forms return the updated value; postfix forms return the previous numeric value. Both forms update the variable.

```javascript
let n = 5;
console.log(n++);
console.log(++n);
console.log(n--);
console.log(--n);
console.log(n);
```

Output:

```text
5
7
7
5
5
```

| Expression | Before | Returned | After |
|---|---:|---:|---:|
| `n++` | 5 | 5 | 6 |
| `++n` | 6 | 7 | 7 |
| `n--` | 7 | 7 | 6 |
| `--n` | 6 | 5 | 5 |

In an ordinary `for` update clause, `i++` and `++i` have the same effect on a numeric counter because the expression result is discarded. Inside a condition or function argument, the difference matters.

```javascript
let i = 0;
while (i++ < 3) {
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

| Check | Value compared | i after increment | Condition | Printed |
|---:|---:|---:|---|---|
| 1 | 0 | 1 | true | 1 |
| 2 | 1 | 2 | true | 2 |
| 3 | 2 | 3 | true | 3 |
| 4 | 3 | 4 | false | None |

Even the failed condition increments `i`. Prefer separate updates when combining operations makes code difficult to trace.

### 4.5 Comparison operators

| Operator | Meaning | Example | Result |
|---|---|---|---|
| `<` | Less than | `3 < 5` | true |
| `<=` | Less than or equal | `5 <= 5` | true |
| `>` | Greater than | `3 > 5` | false |
| `>=` | Greater than or equal | `5 >= 5` | true |
| `===` | Strict equality | `5 === "5"` | false |
| `!==` | Strict inequality | `5 !== "5"` | true |
| `==` | Loose equality | `5 == "5"` | true |
| `!=` | Loose inequality | `5 != "5"` | false |

Strict equality avoids implicit type conversion. Relational comparisons can still coerce operands; two strings are compared lexicographically. For example, `"10" < "2"` is true. Use numeric values for numeric loop boundaries.

```javascript
for (let i = 1; i < 3; i++) {
  console.log(i);
}
```

Output:

```text
1
2
```

Changing `<` to `<=` includes 3. Comparison operators determine which boundary values are visited.

## 5. Conditions and Boolean conversion

### 5.1 Truthy and falsy values

JavaScript converts a loop condition to a Boolean. The falsy values are `false`, `0`, `-0`, `0n`, `NaN`, `""`, `null` and `undefined`, plus the legacy browser-specific `document.all`. Other values are truthy, including `"0"`, `"false"`, empty arrays and empty objects.

```javascript
console.log(Boolean(0));
console.log(Boolean("0"));
console.log(Boolean([]));
console.log(Boolean(null));
```

Output:

```text
false
true
true
false
```

```javascript
let remaining = 3;
while (remaining) {
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

The condition becomes falsy at zero. Negative numbers are also truthy, so `while (remaining > 0)` is clearer when the intention is to process only positive values.

### 5.2 Logical operators

| Operator | Meaning | Evaluation rule |
|---|---|---|
| `&&` | AND | Return first falsy operand, or last operand |
| `||` | OR | Return first truthy operand, or last operand |
| `!` | NOT | Return the opposite Boolean value |

When operands are Boolean, AND requires both to be true; OR requires at least one to be true. NOT reverses the result.

```javascript
for (let i = 1; i <= 8; i++) {
  if (i >= 3 && i % 2 === 0) {
    console.log(i);
  }
}
```

Output:

```text
4
6
8
```

Both requirements must hold. Using `||` instead would accept every even value or every value at least 3.

### 5.3 Short-circuit evaluation

The right operand of `&&` is evaluated only if the left operand is truthy. The right operand of `||` is evaluated only if the left operand is falsy.

```javascript
let calls = 0;
function check() {
  calls++;
  return true;
}
console.log(false && check());
console.log(true || check());
console.log(true && check());
console.log(calls);
```

Output:

```text
false
true
true
1
```

Only the third expression calls the function. This behavior is useful for guarding array access or expensive calculations.

```javascript
const values = [4, 7, 0, 9];
let i = 0;
while (i < values.length && values[i] !== 0) {
  console.log(values[i]);
  i++;
}
```

Output:

```text
4
7
```

The bounds check runs first. If it fails, the second comparison is skipped. The sentinel check stops the loop before printing zero.

### 5.4 Negation and grouping conditions

`!` converts its operand to Boolean and negates it. `!!` converts a value to Boolean without reversing its truthiness.

```javascript
console.log(!0);
console.log(!5);
console.log(!!5);
console.log(0 || 10);
console.log(5 && 20);
```

Output:

```text
true
false
true
10
20
```

Logical AND and OR return operand values, not necessarily Booleans. In a loop condition, the returned value is then converted to Boolean.

Among the operators discussed here, arithmetic generally binds before comparisons, comparisons before `&&`, and `&&` before `||`. Unary `!` binds before comparisons. Use parentheses to express the intended grouping.

```javascript
for (let i = 1; i <= 6; i++) {
  if (i > 2 && (i % 2 === 0 || i === 5)) {
    console.log(i);
  }
}
```

Output:

```text
4
5
6
```

The value must exceed 2 and must either be even or equal 5. Parentheses make that requirement explicit.

## 6. Statements and blocks

### 6.1 Single statements and block statements

An expression produces a value. A statement performs an action or controls execution. An expression followed by a semicolon can form an expression statement. A block groups statements using braces.

```javascript
for (let i = 1; i <= 2; i++)
  console.log(i);
console.log("Done");
```

Output:

```text
1
2
Done
```

Only the first statement is the loop body. Indentation does not determine statement boundaries.

```javascript
for (let i = 1; i <= 2; i++) {
  console.log(i);
  console.log("Inside");
}
console.log("Done");
```

Output:

```text
1
Inside
2
Inside
Done
```

Both statements inside the braces repeat. Prefer braces even when the body contains only one statement.

### 6.2 Empty statements

A standalone semicolon is an empty statement. It can accidentally become the entire loop body.

```javascript
let i;
for (i = 0; i < 3; i++); // Accidental empty body
{
  console.log(i);
}
```

Output:

```text
3
```

The loop performs three empty iterations. The following block is separate and executes once, after termination. Remove the semicolon after the closing parenthesis to attach the block to the loop.

## 7. Combining the concepts

Suppose we want to count and sum the positive even numbers in a collection, stopping at the sentinel `-1`.

```javascript
const values = [4, -2, 3, 8, -1, 6];
let count = 0;
let total = 0;
let i = 0;
while (i < values.length && values[i] !== -1) {
  const value = values[i];
  if (value > 0 && value % 2 === 0) {
    count++;
    total += value;
  }
  i++;
}
console.log("Count:", count);
console.log("Total:", total);
```

Output:

```text
Count: 2
Total: 12
```

| i | Value | Accepted? | Count | Total |
|---:|---:|---|---:|---:|
| 0 | 4 | Yes | 1 | 4 |
| 1 | -2 | No | 1 | 4 |
| 2 | 3 | No | 1 | 4 |
| 3 | 8 | Yes | 2 | 12 |
| 4 | -1 | Loop stops | 2 | 12 |

The constant array binding stays unchanged. The mutable index advances on every iteration, while the count and total change only for accepted values. The sentinel prevents processing the remaining values. This example combines declarations, arithmetic, comparisons, logical operators, assignment and blocks.

## 8. Common mistakes

### 8.1 Assignment instead of comparison

```javascript
let i = 0;
if (i = 3) {
  console.log(i);
}
```

Output:

```text
3
```

The assignment changes `i` to 3 and returns 3, which is truthy. Use `i === 3` to compare without changing the variable. In a loop condition, accidental assignment can prevent termination.

### 8.2 Updating a constant

```javascript
const i = 0;
i++;
```

Result: `TypeError`, because the constant binding cannot be reassigned. Use `let` for a changing counter.

### 8.3 Incorrect range conditions

JavaScript does not interpret `1 <= i <= 5` as a mathematical chained comparison. It first evaluates `1 <= i`, then compares that Boolean with 5. Write `i >= 1 && i <= 5` instead.

### 8.4 Confusing addition and concatenation

```javascript
let i = "1";
i += 1;
console.log(i);
```

Output:

```text
11
```

The result is a string. Initialize numeric counters with numbers, or explicitly convert and validate external input before using it in arithmetic.

## 9. Key takeaways

- Use `let` for changing bindings and `const` for bindings that remain fixed.
- Arithmetic and assignment operators control progression and accumulation.
- Prefix and postfix updates differ in their returned values.
- Comparisons determine boundaries; prefer strict equality for equality tests.
- Conditions use Boolean conversion, and logical operators can short-circuit.
- Braces group repeated statements; an accidental semicolon can create an empty loop body.

## 10. Practice exercises

### Understanding and tracing

1. Explain the difference between declaration, initialization and reassignment.
2. Trace `let n = 4; console.log(n++); console.log(--n); console.log(n);`.
3. Predict the Boolean conversion of `0`, `"0"`, `[]`, `NaN`, `-1` and `null`.
4. Explain why `false && check()` does not call `check`.
5. Trace the following code, including the failed condition check.

```javascript
let i = 0;
while (++i < 4) {
  console.log(i);
}
```

### Writing code

6. Write a loop that prints multiples of three from 1 through 20.
7. Write a loop that doubles a value starting at 1 until it reaches 32, printing each visited value.
8. Count the negative odd integers in `[-5, -2, 3, -7, 0]`.
9. Sum values greater than 10 and less than or equal to 30 in `[5, 10, 15, 30, 35]`.

### Debugging

10. Explain and correct the condition `0 <= i < 10`.
11. Explain why a loop using `const i = 0` and `i++` fails.
12. Correct the accidental empty body in `for (let i = 0; i < 3; i++);` so it prints each counter value.

---

Previous: [Loop Basics](01-loop-basics.md) | Curriculum: Plain Loops | Next: The for Loop (planned)
