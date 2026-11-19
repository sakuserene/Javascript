# Unit 1 - Lesson 2: JavaScript Operators and Expressions

**Level:** Basic knowledge to strong fundamentals  
**Teaching approach:** Detailed explanations, line-by-line execution, practical examples, and exercises  
**Previous lesson:** [Lesson 1 - Variables, Values, and Data Types](01-variables-and-data-types.md)  
**Next lesson:** [Lesson 3 - Type Conversion and Type Coercion](03-type-conversion-and-type-coercion.md)

> **Study note:** This document includes the full lesson, all ten knowledge-check questions, eight coding exercises, and model solutions. Try the questions before reading their solutions. All diagrams and punctuation use ASCII-compatible characters.

## Table of Contents

- [Learning objectives](#learning-objectives)
- [1. What are JavaScript operators?](#1-what-are-javascript-operators)
- [2. Categories of JavaScript operators](#2-categories-of-javascript-operators)
- [3. Arithmetic operators](#3-arithmetic-operators)
- [4. Assignment operators](#4-assignment-operators)
- [5. Increment and decrement operators](#5-increment-and-decrement-operators)
- [6. Comparison operators](#6-comparison-operators)
- [7. Logical operators](#7-logical-operators)
- [8. Truthy and falsy values](#8-truthy-and-falsy-values)
- [9. Short-circuit evaluation](#9-short-circuit-evaluation)
- [10. Nullish coalescing operator](#10-nullish-coalescing-operator)
- [11. The ternary conditional operator](#11-the-ternary-conditional-operator)
- [12. Operator precedence and associativity](#12-operator-precedence-and-associativity)
- [13. Combining multiple operators](#13-combining-multiple-operators)
- [14. Common mistakes beginners make](#14-common-mistakes-beginners-make)
- [15. Knowledge assessment](#15-knowledge-assessment)
- [16. Practical coding exercises](#16-practical-coding-exercises)
- [17. Model solutions and explanations](#17-model-solutions-and-explanations)
- [18. Lesson summary](#18-lesson-summary)
- [Lesson navigation](#lesson-navigation)

---

## Learning objectives

By the end of this lesson, you should be able to:

1. Explain the difference between operators, operands, and expressions.
2. Use arithmetic operators correctly.
3. Understand assignment and compound assignment operators.
4. Compare values using equality and relational operators.
5. Explain the difference between `==` and `===`.
6. Use logical operators such as `&&`, `||`, and `!`.
7. Understand truthy and falsy values.
8. Use the ternary conditional operator.
9. Understand increment and decrement operators.
10. Explain operator precedence, associativity, and short-circuit evaluation.
11. Predict JavaScript outputs without executing the code.
12. Solve problems using multiple approaches.

In Lesson 1, we learned how JavaScript declares variables, stores values, and handles different data types. This lesson explains how JavaScript performs calculations, compares values, makes logical decisions, updates variables, and evaluates expressions. These concepts prepare you for conditions, loops, functions, and array methods.

---

## 1. What are JavaScript operators?

An **operator** is a symbol or keyword that instructs JavaScript to perform an operation on one or more values.

```javascript
let result = 10 + 5;
console.log(result);
```

Output:

```text
15
```

Consider the expression `10 + 5`:

| Component | Name | Explanation |
|---|---|---|
| `10` | Operand | First value |
| `+` | Operator | Performs addition |
| `5` | Operand | Second value |
| `10 + 5` | Expression | Evaluates to `15` |

An **operand** is a value or expression an operator works with. An **expression** is code that evaluates to a value.

### 1.1 Operators versus expressions versus statements

```javascript
let total = 10 + 5;
```

- `+` is an operator.
- `10 + 5` is an expression.
- `let total = 10 + 5;` is a variable declaration statement.

```javascript
let price = 100;
let quantity = 3;
let total = price * quantity;
console.log(total);
```

Output:

```text
300
```

Line by line:

1. `price` is initialized to `100`.
2. `quantity` is initialized to `3`.
3. JavaScript evaluates `price * quantity`, obtaining `300`, and assigns it to `total`.
4. `console.log(total)` prints `300`.

---

## 2. Categories of JavaScript operators

| Category | Operators | Purpose |
|---|---|---|
| Arithmetic | `+`, `-`, `*`, `/`, `%`, `**` | Mathematical calculations |
| Assignment | `=`, `+=`, `-=`, `*=`, `/=`, `%=` | Assign and update values |
| Comparison | `==`, `===`, `!=`, `!==`, `>`, `<`, `>=`, `<=` | Compare values |
| Logical | `&&`, `\|\|`, `!` | Combine or negate conditions |
| Increment/decrement | `++`, `--` | Increase or decrease by one |
| Conditional | `? :` | Select between two expressions |
| Nullish coalescing | `??` | Fallback for nullish values |
| Optional chaining | `?.` | Safely access properties or call methods |
| Type-related | `typeof`, `instanceof` | Inspect types or object relationships |
| Bitwise | `&`, `\|`, `^`, `~`, `<<`, `>>` | Work with integer bits |

We will focus on the operators most important for your current learning level. Specialized operators will receive more attention in later lessons.

---

## 3. Arithmetic operators

Arithmetic operators perform mathematical operations.

### 3.1 Addition (`+`)

```javascript
let a = 10;
let b = 5;
let result = a + b;
console.log(result);
```

Output: `15`

The `+` operator also concatenates strings:

```javascript
let firstName = "Alice";
let lastName = "Johnson";
let fullName = firstName + " " + lastName;
console.log(fullName);
```

Output: `Alice Johnson`

```javascript
console.log(10 + 5);
console.log("10" + 5);
console.log(10 + "5");
```

Output:

```text
15
105
105
```

In the first expression, JavaScript adds two numbers. In the second and third, a string operand causes concatenation: the numeric operand is converted to a string. This is **type coercion**, which Lesson 3 explores further.

### 3.2 Subtraction (`-`)

```javascript
let price = 500;
let discount = 100;
let finalPrice = price - discount;
console.log(finalPrice);
```

Output: `400`

Unlike `+`, subtraction does not concatenate strings:

```javascript
console.log("10" - 5);
```

Output: `5`. JavaScript converts the numeric string to a number.

### 3.3 Multiplication (`*`)

```javascript
let price = 200;
let quantity = 4;
let total = price * quantity;
console.log(total);
```

Output: `800`

### 3.4 Division (`/`)

```javascript
let total = 100;
let people = 4;
let share = total / people;
console.log(share);
```

Output: `25`

Division by zero:

```javascript
console.log(10 / 0);
console.log(-10 / 0);
console.log(0 / 0);
```

Output:

```text
Infinity
-Infinity
NaN
```

Ordinary JavaScript `number` division by zero does not throw an exception. `NaN` means "Not a Number" and represents an invalid numeric result.

### 3.5 Remainder (`%`)

```javascript
console.log(10 % 3);
console.log(20 % 5);
console.log(17 % 4);
```

Output:

```text
1
0
1
```

Visual explanation of `10 % 3`:

```text
10 items

Group 1:  o o o
Group 2:  o o o
Group 3:  o o o
Leftover: o

10 = (3 * 3) + 1
Remainder = 1
```

Checking even or odd numbers:

```javascript
let number = 8;
console.log(number % 2);
```

Output: `0`

```javascript
let number = 8;
if (number % 2 === 0) {
  console.log("Even number");
} else {
  console.log("Odd number");
}
```

Output: `Even number`

**Important:** JavaScript `%` is a remainder operator, not a mathematical modulo operator. With negative operands:

```javascript
console.log(-10 % 3);
```

Output: `-1`

### 3.6 Exponentiation (`**`)

```javascript
console.log(2 ** 3);
console.log(5 ** 2);
console.log(10 ** 3);
```

Output:

```text
8
25
1000
```

`2 ** 3` means `2 * 2 * 2 = 8`.

### Arithmetic operator summary

| Operator | Example | Result |
|---|---|---|
| `+` | `10 + 3` | `13` |
| `-` | `10 - 3` | `7` |
| `*` | `10 * 3` | `30` |
| `/` | `10 / 2` | `5` |
| `%` | `10 % 3` | `1` |
| `**` | `2 ** 3` | `8` |

---

## 4. Assignment operators

Assignment operators assign or update variable values.

### 4.1 Basic assignment (`=`)

```javascript
let score = 50;
```

This assigns `50` to `score`.

### 4.2 Addition assignment (`+=`)

```javascript
let score = 50;
score += 10;
console.log(score);
```

Output: `60`

Equivalent to `score = score + 10`.

Execution:

1. Read the current `score`: `50`.
2. Add `10`, producing `60`.
3. Assign `60` back to `score`.

### 4.3 Subtraction assignment (`-=`)

```javascript
let balance = 1000;
balance -= 250;
console.log(balance);
```

Output: `750`. Equivalent to `balance = balance - 250`.

### 4.4 Multiplication assignment (`*=`)

```javascript
let number = 5;
number *= 3;
console.log(number);
```

Output: `15`.

### 4.5 Division assignment (`/=`)

```javascript
let amount = 100;
amount /= 4;
console.log(amount);
```

Output: `25`.

### 4.6 Remainder assignment (`%=`)

```javascript
let number = 17;
number %= 5;
console.log(number);
```

Output: `2`.

### 4.7 Exponentiation assignment (`**=`)

```javascript
let number = 3;
number **= 2;
console.log(number);
```

Output: `9`.

| Operator | Example | Equivalent |
|---|---|---|
| `=` | `x = 10` | Assign `10` |
| `+=` | `x += 5` | `x = x + 5` |
| `-=` | `x -= 5` | `x = x - 5` |
| `*=` | `x *= 5` | `x = x * 5` |
| `/=` | `x /= 5` | `x = x / 5` |
| `%=` | `x %= 5` | `x = x % 5` |
| `**=` | `x **= 2` | `x = x ** 2` |

---

## 5. Increment and decrement operators

### 5.1 Increment (`++`)

```javascript
let count = 5;
count++;
console.log(count);
```

Output: `6`. In this numeric example, the update is equivalent to `count += 1`.

### 5.2 Decrement (`--`)

```javascript
let count = 5;
count--;
console.log(count);
```

Output: `4`.

### 5.3 Postfix increment (`x++`)

```javascript
let x = 5;
let result = x++;
console.log(result);
console.log(x);
```

Output:

```text
5
6
```

`x++` produces the old value as the expression's result, then increments `x`.

### 5.4 Prefix increment (`++x`)

```javascript
let x = 5;
let result = ++x;
console.log(result);
console.log(x);
```

Output:

```text
6
6
```

`++x` increments first, then produces the updated value.

```text
Initial x = 5

Postfix x++: result = 5, final x = 6
Prefix  ++x: result = 6, final x = 6
```

### 5.5 Prefix and postfix decrement

```javascript
let a = 10;
console.log(a--);
console.log(a);
```

Output:

```text
10
9
```

Compare:

```javascript
let a = 10;
console.log(--a);
console.log(a);
```

Output:

```text
9
9
```

---

## 6. Comparison operators

Comparison operators compare values and generally produce `true` or `false`.

### 6.1 Greater than (`>`)

```javascript
console.log(10 > 5);
console.log(3 > 8);
```

Output: `true`, then `false`.

### 6.2 Less than (`<`)

```javascript
console.log(10 < 5);
console.log(3 < 8);
```

Output: `false`, then `true`.

### 6.3 Greater than or equal (`>=`)

```javascript
console.log(10 >= 10);
console.log(8 >= 10);
```

Output: `true`, then `false`.

### 6.4 Less than or equal (`<=`)

```javascript
console.log(10 <= 10);
console.log(15 <= 10);
```

Output: `true`, then `false`.

### 6.5 Loose equality (`==`)

`==` uses JavaScript's abstract equality rules, which may involve coercion.

```javascript
console.log(10 == 10);
console.log(10 == "10");
```

Output:

```text
true
true
```

### 6.6 Strict equality (`===`)

`===` does not perform the type coercion used by `==`.

```javascript
console.log(10 === 10);
console.log(10 === "10");
```

Output:

```text
true
false
```

| Values compared | `==` | `===` |
|---|---|---|
| `10` and `10` | `true` | `true` |
| `10` and `"10"` | `true` | `false` |
| `0` and `false` | `true` | `false` |
| `null` and `undefined` | `true` | `false` |
| `""` and `0` | `true` | `false` |

```javascript
console.log(false == 0);
console.log(false === 0);
```

Output: `true`, then `false`.

**Best practice:** Prefer `===` unless you specifically intend the coercion rules of `==`.

### 6.7 Inequality (`!=`) and strict inequality (`!==`)

```javascript
console.log(10 != "10");
console.log(10 !== "10");
```

Output:

```text
false
true
```

### 6.8 Comparing objects

```javascript
const a = { value: 10 };
const b = { value: 10 };
console.log(a === b);
```

Output: `false`, because these are distinct objects.

```javascript
const a = { value: 10 };
const b = a;
console.log(a === b);
```

Output: `true`, because both variables reference the same object.

---

## 7. Logical operators

The three principal logical operators are `&&` (AND), `||` (OR), and `!` (NOT).

### 7.1 Logical AND (`&&`)

With Boolean operands, AND is `true` only if both are `true`.

```javascript
console.log(true && true);
console.log(true && false);
console.log(false && true);
console.log(false && false);
```

Output:

```text
true
false
false
false
```

Practical example:

```javascript
let age = 20;
let hasTicket = true;
if (age >= 18 && hasTicket) {
  console.log("Entry allowed");
} else {
  console.log("Entry denied");
}
```

Output: `Entry allowed`.

### 7.2 Logical OR (`||`)

With Boolean operands, OR is `true` if at least one is `true`.

```javascript
console.log(true || true);
console.log(true || false);
console.log(false || true);
console.log(false || false);
```

Output:

```text
true
true
true
false
```

```javascript
let isAdmin = false;
let isModerator = true;
if (isAdmin || isModerator) {
  console.log("Access granted");
}
```

Output: `Access granted`.

### 7.3 Logical NOT (`!`)

NOT converts its operand to a Boolean and reverses it.

```javascript
console.log(!true);
console.log(!false);
```

Output: `false`, then `true`.

```javascript
let isLoggedIn = false;
if (!isLoggedIn) {
  console.log("Please log in");
}
```

Output: `Please log in`.

### Logical truth table

| A | B | `A && B` | `A \|\| B` |
|---|---|---|---|
| `true` | `true` | `true` | `true` |
| `true` | `false` | `false` | `true` |
| `false` | `true` | `false` | `true` |
| `false` | `false` | `false` | `false` |

---

## 8. Truthy and falsy values

A **truthy** value behaves like `true` in a Boolean context. A **falsy** value behaves like `false`.

### 8.1 Falsy values

Common falsy values:

```javascript
false
0
-0
0n
""
null
undefined
NaN
```

The special browser value `document.all` is also falsy for historical compatibility reasons.

```javascript
console.log(Boolean(0));
console.log(Boolean(""));
console.log(Boolean(null));
console.log(Boolean(undefined));
```

Output:

```text
false
false
false
false
```

### 8.2 Truthy values

```javascript
console.log(Boolean(10));
console.log(Boolean("Hello"));
console.log(Boolean([]));
console.log(Boolean({}));
```

Output:

```text
true
true
true
true
```

**Important:** Empty arrays and empty objects are truthy.

```javascript
if ([]) {
  console.log("Executed");
}
```

Output: `Executed`.

### 8.3 Why truthiness matters

```javascript
let username = "";
if (username) {
  console.log("Welcome");
} else {
  console.log("Username required");
}
```

Output: `Username required`, because an empty string is falsy.

---

## 9. Short-circuit evaluation

JavaScript's AND and OR operators may skip evaluation of the second operand. They also return operand values, not necessarily Booleans.

### 9.1 AND short-circuiting

```javascript
console.log(0 && "Hello");
```

Output: `0`. The first operand is falsy, so JavaScript returns it without evaluating the second.

```javascript
console.log(10 && "Hello");
```

Output: `Hello`. The first operand is truthy, so JavaScript evaluates and returns the second.

```javascript
let isLoggedIn = true;
isLoggedIn && console.log("Welcome back");
```

Output: `Welcome back`. If `isLoggedIn` were false, the logging call would be skipped.

### 9.2 OR short-circuiting

```javascript
console.log("Alice" || "Guest");
```

Output: `Alice`.

```javascript
console.log("" || "Guest");
```

Output: `Guest`.

```javascript
let username = "";
let displayName = username || "Guest";
console.log(displayName);
```

Output: `Guest`.

Caution: `||` also replaces valid falsy values such as `0` and `false`.

---

## 10. Nullish coalescing operator

`??` returns its right operand only if the left operand is `null` or `undefined`.

```javascript
let username = null;
let displayName = username ?? "Guest";
console.log(displayName);
```

Output: `Guest`.

Compare:

```javascript
console.log(0 || 100);
console.log(0 ?? 100);
```

Output:

```text
100
0
```

`||` uses truthiness. `??` checks only for nullish values.

| Value | `value \|\| "Default"` | `value ?? "Default"` |
|---|---|---|
| `null` | `"Default"` | `"Default"` |
| `undefined` | `"Default"` | `"Default"` |
| `0` | `"Default"` | `0` |
| `false` | `"Default"` | `false` |
| `""` | `"Default"` | `""` |
| `"Alice"` | `"Alice"` | `"Alice"` |

Use `??` when `0`, `false`, or empty strings should be preserved.

---

## 11. The ternary conditional operator

The ternary (conditional) operator chooses between two expressions based on a condition.

### 11.1 Syntax

```javascript
condition ? valueIfTrue : valueIfFalse;
```

The three operands are the condition, the truthy branch, and the falsy branch.

### 11.2 Basic example

```javascript
let age = 20;
let result = age >= 18 ? "Adult" : "Minor";
console.log(result);
```

Output: `Adult`.

Step by step:

1. Evaluate `age >= 18`.
2. `20 >= 18` is `true`.
3. Select `"Adult"`.
4. Assign that string to `result`.
5. Print `Adult`.

ASCII decision diagram:

```text
                 age >= 18 ?
                    |
          +---------+---------+
          |                   |
         true                false
          |                   |
       "Adult"             "Minor"
```

### 11.3 Ternary versus if...else

Using `if...else`:

```javascript
let age = 20;
let result;
if (age >= 18) {
  result = "Adult";
} else {
  result = "Minor";
}
console.log(result);
```

Using a ternary:

```javascript
let age = 20;
let result = age >= 18 ? "Adult" : "Minor";
console.log(result);
```

Both print `Adult`.

Use a ternary for a straightforward choice between two values. Prefer `if...else` when you need multiple statements or complex logic.

### 11.4 Nested ternary operators

```javascript
let score = 75;
let grade =
  score >= 80 ? "A" :
  score >= 60 ? "B" :
  score >= 40 ? "C" :
  "F";
console.log(grade);
```

Output: `B`.

Execution:

1. Is `75 >= 80`? No.
2. Is `75 >= 60`? Yes.
3. Select `"B"`.
4. Skip the remaining alternatives.

Nested ternaries can be hard to read; `if...else if` or `switch` may be clearer for complicated rules.

---

## 12. Operator precedence and associativity

**Precedence** determines how operators group. **Associativity** determines grouping when operators have the same precedence.

### 12.1 Arithmetic precedence

```javascript
console.log(10 + 5 * 2);
```

Output: `20`, because `5 * 2 = 10` is evaluated before adding `10`.

### 12.2 Parentheses

```javascript
console.log((10 + 5) * 2);
```

Output: `30`.

### 12.3 Multiple arithmetic operators

```javascript
let result = 20 - 4 / 2 + 3;
console.log(result);
```

Output: `21`.

```text
4 / 2 = 2
20 - 2 + 3
18 + 3 = 21
```

### 12.4 Associativity

Subtraction is left-associative:

```javascript
console.log(20 - 5 - 3);
```

Output: `12`, because it groups as `(20 - 5) - 3`.

Exponentiation is right-associative:

```javascript
console.log(2 ** 3 ** 2);
```

Output: `512`, because it groups as `2 ** (3 ** 2) = 2 ** 9`.

### Simplified precedence table

| Priority | Operators | Example |
|---|---|---|
| Higher | Parentheses `()` | `(2 + 3) * 4` |
| | Exponentiation `**` | `2 ** 3` |
| | Multiplication, division, remainder | `*`, `/`, `%` |
| | Addition and subtraction | `+`, `-` |
| | Relational comparisons | `<`, `>`, `<=`, `>=` |
| | Equality comparisons | `==`, `===`, `!=`, `!==` |
| | Logical AND | `&&` |
| | Logical OR / nullish coalescing | `\|\|`, `??` |
| | Conditional | `? :` |
| Lower | Assignment | `=`, `+=`, `-=` |

This is a simplified table. JavaScript has additional precedence levels and syntax restrictions. For example, you must use parentheses when directly mixing `??` with `&&` or `||`.

**Best practice:** Add parentheses when they improve clarity.

---

## 13. Combining multiple operators

### Example 1 - Checking eligibility

```javascript
let age = 22;
let hasID = true;
let isBanned = false;
let canEnter = age >= 18 && hasID && !isBanned;
console.log(canEnter);
```

Output: `true`.

Execution:

1. `age >= 18` becomes `22 >= 18`, which is `true`.
2. `hasID` is `true`.
3. `!isBanned` is `!false`, which is `true`.
4. `true && true && true` is `true`.

### Example 2 - Calculating a discounted price

```javascript
let price = 1000;
let discountPercent = 20;
let discount = price * discountPercent / 100;
let finalPrice = price - discount;
console.log(finalPrice);
```

Output: `800`.

1. Price is `1000`.
2. Discount percentage is `20`.
3. Discount amount is `1000 * 20 / 100 = 200`.
4. Final price is `1000 - 200 = 800`.

### Example 3 - Ternary with logical operators

```javascript
let age = 25;
let hasMembership = true;
let access =
  age >= 18 && hasMembership
    ? "Access granted"
    : "Access denied";
console.log(access);
```

Output: `Access granted`.

The logical condition is evaluated before the ternary chooses the result string.

---

## 14. Common mistakes beginners make

### Mistake 1 - Confusing assignment and equality

Incorrect:

```javascript
let x = 10;
if (x = 5) {
  console.log("Matched");
}
```

This assigns `5` to `x`; because `5` is truthy, the message prints.

Correct:

```javascript
let x = 10;
if (x === 5) {
  console.log("Matched");
}
```

Now the code checks equality without changing `x`.

### Mistake 2 - Using `==` without understanding coercion

```javascript
console.log(0 == false);
```

Output: `true`.

Prefer:

```javascript
console.log(0 === false);
```

Output: `false`.

### Mistake 3 - Assuming `&&` always returns a Boolean

```javascript
console.log("Hello" && 100);
```

Output: `100`.

### Mistake 4 - Assuming an empty array is falsy

```javascript
console.log(Boolean([]));
```

Output: `true`.

### Mistake 5 - Confusing prefix and postfix increment

```javascript
let x = 5;
console.log(x++);
```

Output: `5`, even though `x` becomes `6` after the expression.

---

## 15. Knowledge assessment

**Instructions:** Predict each answer before running the code. Model answers and explanations follow the questions.

### Question 1

What is the output?

```javascript
console.log(10 + 5 * 2);
```

A. `30`  
B. `20`  
C. `25`  
D. `15`

### Question 2

```javascript
console.log(10 === '10');
```

A. `true`  
B. `false`  
C. `undefined`  
D. Error

### Question 3

```javascript
let x = 5;
console.log(x++);
console.log(x);
```

A. `6` then `6`  
B. `5` then `5`  
C. `5` then `6`  
D. `6` then `5`

### Question 4

```javascript
console.log(0 || 50);
```

A. `0`  
B. `50`  
C. `false`  
D. `undefined`

### Question 5

```javascript
console.log(0 ?? 50);
```

A. `0`  
B. `50`  
C. `null`  
D. `false`

### Question 6

```javascript
console.log(17 % 5);
```

A. `3`  
B. `2`  
C. `5`  
D. `0`

### Question 7

```javascript
let age = 16;
console.log(age >= 18 ? 'Adult' : 'Minor');
```

A. `Adult`  
B. `Minor`  
C. `false`  
D. `undefined`

### Question 8

```javascript
console.log(2 ** 3 ** 2);
```

A. `64`  
B. `512`  
C. `256`  
D. `36`

### Question 9

```javascript
console.log(Boolean([]));
```

A. `false`  
B. `true`  
C. `undefined`  
D. `null`

### Question 10

```javascript
let x = 10;
x += 5;
x *= 2;
console.log(x);
```

A. `20`  
B. `25`  
C. `30`  
D. `15`

### Knowledge assessment - answers and explanations

| Question | Correct option | Explanation |
|---|---|---|
| 1 | B - `20` | Multiplication first: `5 * 2 = 10`, then `10 + 10 = 20`. |
| 2 | B - `false` | Strict equality does not coerce a number and string into matching types. |
| 3 | C - `5` then `6` | Postfix increment returns the old value before incrementing `x`. |
| 4 | B - `50` | `0` is falsy, so `||` returns the second operand. |
| 5 | A - `0` | `0` is not `null` or `undefined`, so `??` preserves it. |
| 6 | B - `2` | `17 = 5 * 3 + 2`. |
| 7 | B - `Minor` | `16 >= 18` is false, so the falsy branch is selected. |
| 8 | B - `512` | Exponentiation is right-associative: `2 ** (3 ** 2) = 512`. |
| 9 | B - `true` | Empty arrays are objects and are truthy. |
| 10 | C - `30` | `10 + 5 = 15`, then `15 * 2 = 30`. |

**Suggested self-assessment:** 10/10 - excellent; 8-9 - very good; 6-7 - revisit weak areas; 0-5 - repeat the examples and try again.

---

## 16. Practical coding exercises

Try to complete all eight exercises independently before reading the solutions in Section 17.

### Exercise 1 - Arithmetic operators

Create:

```javascript
let a = 25;
let b = 4;
```

Calculate and print the sum, difference, product, quotient, remainder, and `a` raised to the power of `b`.

### Exercise 2 - Assignment operators

Create a variable `balance` with initial value `1000`. Using compound assignment operators:

1. Add `500`.
2. Subtract `200`.
3. Multiply by `2`.
4. Divide by `4`.
5. Print the final balance.

### Exercise 3 - Comparison operators

Create:

```javascript
let a = 10;
let b = "10";
```

Print and explain:

```javascript
a == b
a === b
a != b
a !== b
```

### Exercise 4 - Logical operators

Create:

```javascript
let age = 20;
let hasTicket = true;
let isBanned = false;
```

Print `"Entry allowed"` only if the person is at least 18, has a ticket, and is not banned.

### Exercise 5 - Ternary operator

Create a variable `score`. Use a ternary expression to print `"Pass"` for scores of at least `50`, otherwise `"Fail"`.

Then extend your solution:

| Score | Grade |
|---|---|
| 80 or above | A |
| 60-79 | B |
| 40-59 | C |
| Below 40 | F |

### Exercise 6 - Prefix and postfix operators

Predict the output without running:

```javascript
let x = 5;
let a = x++;
let b = ++x;
let c = x--;
console.log(a);
console.log(b);
console.log(c);
console.log(x);
```

Explain `x` after each operation.

### Exercise 7 - Short-circuit evaluation

Predict:

```javascript
let username = "";
let age = 0;
let nameResult = username || "Guest";
let ageResult = age ?? 18;
console.log(nameResult);
console.log(ageResult);
```

Explain the difference between `||` and `??`.

### Exercise 8 - Debugging

Find and fix the problem:

```javascript
let score = 40;
if (score = 50) {
  console.log("Score is 50");
} else {
  console.log("Score is not 50");
}
```

The corrected code must check whether `score` equals `50` without changing it.

---

## 17. Model solutions and explanations

These are **model solutions**, not graded answers submitted by the learner. Multiple correct solutions may exist.

### Solution 1 - Arithmetic operators

```javascript
let a = 25;
let b = 4;

console.log("Sum:", a + b);
console.log("Difference:", a - b);
console.log("Product:", a * b);
console.log("Quotient:", a / b);
console.log("Remainder:", a % b);
console.log("Power:", a ** b);
```

Output:

```text
Sum: 29
Difference: 21
Product: 100
Quotient: 6.25
Remainder: 1
Power: 390625
```

Explanation:

1. `25 + 4 = 29`.
2. `25 - 4 = 21`.
3. `25 * 4 = 100`.
4. `25 / 4 = 6.25`.
5. `25 % 4 = 1`, because `4 * 6 = 24` and one remains.
6. `25 ** 4 = 25 * 25 * 25 * 25 = 390625`.

### Solution 2 - Assignment operators

```javascript
let balance = 1000;

balance += 500;
balance -= 200;
balance *= 2;
balance /= 4;

console.log(balance);
```

Output:

```text
650
```

Line-by-line trace:

| Operation | Calculation | Balance |
|---|---|---|
| Initialize | - | `1000` |
| `+= 500` | `1000 + 500` | `1500` |
| `-= 200` | `1500 - 200` | `1300` |
| `*= 2` | `1300 * 2` | `2600` |
| `/= 4` | `2600 / 4` | `650` |

### Solution 3 - Comparison operators

```javascript
let a = 10;
let b = "10";

console.log(a == b);
console.log(a === b);
console.log(a != b);
console.log(a !== b);
```

Output:

```text
true
false
false
true
```

Explanation:

- `a == b` is `true`: abstract equality permits coercion.
- `a === b` is `false`: one operand is a number and the other a string.
- `a != b` is `false`: loose inequality is the negation of loose equality.
- `a !== b` is `true`: strict inequality is the negation of strict equality.

**Best practice:** Prefer strict equality and inequality unless coercion is intentional.

### Solution 4 - Logical operators

```javascript
let age = 20;
let hasTicket = true;
let isBanned = false;

if (age >= 18 && hasTicket && !isBanned) {
  console.log("Entry allowed");
} else {
  console.log("Entry denied");
}
```

Output:

```text
Entry allowed
```

Trace:

1. `20 >= 18` is `true`.
2. `hasTicket` is `true`.
3. `!isBanned` becomes `!false`, which is `true`.
4. `true && true && true` is `true`, so the first branch runs.

An alternative is to compute a named Boolean first:

```javascript
const canEnter = age >= 18 && hasTicket && !isBanned;
console.log(canEnter ? "Entry allowed" : "Entry denied");
```

### Solution 5 - Ternary operator

```javascript
let score = 75;

let result = score >= 50 ? "Pass" : "Fail";
console.log(result);

let grade =
  score >= 80 ? "A" :
  score >= 60 ? "B" :
  score >= 40 ? "C" :
  "F";

console.log(grade);
```

Output:

```text
Pass
B
```

Explanation:

1. `75 >= 50` is true, so `result` is `"Pass"`.
2. `75 >= 80` is false.
3. `75 >= 60` is true, so `grade` is `"B"`.
4. The remaining grade conditions are skipped.

Alternative using `if...else if`:

```javascript
let grade;

if (score >= 80) {
  grade = "A";
} else if (score >= 60) {
  grade = "B";
} else if (score >= 40) {
  grade = "C";
} else {
  grade = "F";
}

console.log(grade);
```

This is often clearer if the grading logic becomes more complex. Both examples assume the score is a valid number; input validation would be a separate requirement.

### Solution 6 - Prefix and postfix operators

Original code:

```javascript
let x = 5;
let a = x++;
let b = ++x;
let c = x--;
console.log(a);
console.log(b);
console.log(c);
console.log(x);
```

Output:

```text
5
7
7
6
```

Detailed trace:

| Statement | Expression result | New `x` | Explanation |
|---|---|---|---|
| `let x = 5` | - | `5` | Initialize `x` |
| `let a = x++` | `5` | `6` | Postfix returns old value, then increments |
| `let b = ++x` | `7` | `7` | Prefix increments first, then returns new value |
| `let c = x--` | `7` | `6` | Postfix returns old value, then decrements |

Thus `a = 5`, `b = 7`, `c = 7`, and final `x = 6`.

### Solution 7 - Short-circuit evaluation

```javascript
let username = "";
let age = 0;
let nameResult = username || "Guest";
let ageResult = age ?? 18;
console.log(nameResult);
console.log(ageResult);
```

Output:

```text
Guest
0
```

Explanation:

1. `username` is an empty string, which is falsy.
2. `username || "Guest"` therefore returns `"Guest"`.
3. `age` is `0`, which is falsy but is **not** nullish.
4. `age ?? 18` therefore returns `0`.

Use `||` for a truthy fallback and `??` for a fallback only when a value is `null` or `undefined`.

### Solution 8 - Debugging

Original problem:

```javascript
let score = 40;
if (score = 50) {
  console.log("Score is 50");
} else {
  console.log("Score is not 50");
}
```

The condition uses the **assignment operator** `=` rather than an equality operator. It changes `score` to `50`. The assignment expression evaluates to `50`, which is truthy, so the first branch runs incorrectly.

Corrected code:

```javascript
let score = 40;

if (score === 50) {
  console.log("Score is 50");
} else {
  console.log("Score is not 50");
}
```

Output:

```text
Score is not 50
```

Why this works:

1. `score` starts as `40`.
2. `score === 50` compares without changing `score`.
3. The comparison is false.
4. The `else` branch runs.
5. `score` remains `40`.

---

## 18. Lesson summary

| Concept | Key takeaway |
|---|---|
| Operator | Performs an operation on operands |
| Expression | Code that evaluates to a value |
| Arithmetic | Mathematical calculations |
| Assignment | Assigns or updates variable values |
| Comparison | Compares values |
| Strict equality | `===` compares without coercion |
| Logical AND | `&&` returns the first falsy operand, or the last operand |
| Logical OR | `\|\|` returns the first truthy operand, or the last operand |
| Logical NOT | `!` converts to Boolean and reverses it |
| Truthy/falsy | How values behave in Boolean contexts |
| Nullish coalescing | `??` falls back only for `null` or `undefined` |
| Ternary | Selects between two expressions |
| Prefix increment | Updates first, then produces the new value |
| Postfix increment | Produces the old value, then updates |
| Precedence | Determines operator grouping |
| Short-circuiting | Skips unnecessary operand evaluation |

### Final revision checklist

- [ ] I can explain the difference between an operator, operand, expression, and statement.
- [ ] I can calculate expressions with arithmetic and assignment operators.
- [ ] I can explain prefix and postfix increment/decrement.
- [ ] I understand why `==` and `===` can return different results.
- [ ] I can evaluate expressions with `&&`, `||`, `!`, and `??`.
- [ ] I know why `[]` and `{}` are truthy.
- [ ] I can rewrite a simple `if...else` as a ternary expression.
- [ ] I can use parentheses to clarify operator precedence.
- [ ] I can solve all eight coding exercises without looking at the model solutions.

---

## Lesson navigation

| Direction | Lesson |
|---|---|
| Previous | [Unit 1 - Lesson 1: Variables, Values, and Data Types](01-variables-and-data-types.md) |
| Current | **Unit 1 - Lesson 2: Operators and Expressions** |
| Next | [Unit 1 - Lesson 3: Type Conversion and Type Coercion](03-type-conversion-and-type-coercion.md) |

[Back to Table of Contents](#table-of-contents)

> The next lesson link will work once `03-type-conversion-and-type-coercion.md` is added to the same directory.
