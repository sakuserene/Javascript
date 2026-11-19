# Unit 1 - Lesson 6: JavaScript Booleans, Truthy and Falsy Values, and Logical Expressions

**Level:** Beginner to intermediate  
**Previous:** [Lesson 5 - Numbers and Math Methods](05-numbers-and-math-methods.md)  
**Current:** Lesson 6 - Booleans, Truthy and Falsy Values, and Logical Expressions  
**Next:** [Lesson 7 - Conditional Statements and Decision Making](07-conditional-statements-and-decision-making.md)

## Table of Contents

1. [Lesson overview](#lesson-overview)
2. [Learning objectives](#learning-objectives)
3. [1. What is a Boolean?](#1-what-is-a-boolean-in-javascript)
4. [2. Boolean primitives versus objects](#2-boolean-primitives-versus-boolean-objects)
5. [3. Truthy and falsy values](#3-understanding-truthy-and-falsy-values)
6. [4. Truthiness explorer - static reference](#4-truthiness-explorer---static-reference)
7. [5. Explicit Boolean conversion](#5-explicit-boolean-conversion)
8. [6. Comparison operators](#6-comparison-operators-and-boolean-expressions)
9. [7. Logical operators](#7-logical-operators)
10. [8. Logical AND](#8-logical-and-)
11. [9. Logical OR](#9-logical-or-)
12. [10. Logical NOT](#10-logical-not-)
13. [11. Logical truth tables](#11-logical-operator-truth-tables)
14. [12. Non-Boolean return values](#12-logical-operators-can-return-non-boolean-values)
15. [13. Short-circuit evaluation](#13-short-circuit-evaluation)
16. [14. Nullish coalescing](#14-nullish-coalescing-operator-)
17. [15. Logical assignment](#15-logical-assignment-operators)
18. [16. Operator precedence](#16-operator-precedence-in-logical-expressions)
19. [17. Compound conditions](#17-combining-multiple-logical-conditions)
20. [18. De Morgan's laws](#18-de-morgans-laws)
21. [19. Practical examples](#19-practical-boolean-logic-examples)
22. [20. Common mistakes](#20-common-mistakes-beginners-make)
23. [21. Knowledge assessment](#21-knowledge-assessment)
24. [22. Practical coding exercises](#22-practical-coding-exercises)
25. [23. Assessment answers and explanations](#23-assessment-answers-and-explanations)
26. [24. Exercise model solutions](#24-exercise-model-solutions)
27. [25. Lesson summary](#25-lesson-summary)
28. [Lesson navigation](#lesson-navigation)

---

## Lesson overview

In previous lessons, we learned how JavaScript handles variables, data types, operators, type conversion, strings, and numbers. Now we study **Boolean logic**, which applications use to make decisions.

A program may need to determine whether a user is old enough to register, a password meets requirements, a student has passed an examination, a shopping cart contains products, two values are equal, or multiple conditions are satisfied.

```javascript
let age = 20;
let hasPermission = true;
let canEnter = age >= 18 && hasPermission;
console.log(canEnter);
```

Output:

```text
true
```

Both conditions are true, so the AND expression returns `true`.

## Learning objectives

By the end of the lesson, you should be able to:

1. Define Boolean values and their purpose.
2. Create Boolean variables and expressions.
3. Distinguish Boolean primitives from Boolean objects.
4. Identify falsy values and explain truthiness.
5. Convert values using `Boolean()` and `!!`.
6. Use comparison operators to create Boolean expressions.
7. Use `&&`, `||`, and `!`.
8. Explain short-circuit evaluation.
9. Explain why `&&` and `||` can return non-Boolean values.
10. Distinguish `??` from `||`.
11. Apply precedence and parentheses correctly.
12. Use logical assignment operators.
13. Build compound logical expressions.
14. Recognize De Morgan's laws.
15. Correct common Boolean logic mistakes.

---

## 1. What is a Boolean in JavaScript?

A **Boolean** is a primitive data type with exactly two values: `true` and `false`. They are lowercase JavaScript keywords.

```javascript
let isLoggedIn = true;
let isAccountLocked = false;
console.log(isLoggedIn);
console.log(isAccountLocked);
```

Output:

```text
true
false
```

### 1.1 Boolean values are not strings

```javascript
let firstValue = true;
let secondValue = "true";
console.log(typeof firstValue);
console.log(typeof secondValue);
console.log(true === "true");
```

Output:

```text
boolean
string
false
```

The first is a Boolean; the second is text. Strict equality does not equate them.

### 1.2 Booleans from comparisons

```javascript
console.log(10 > 5);
console.log(10 < 5);
console.log(10 === 10);
console.log(10 !== 10);
```

Output:

```text
true
false
true
false
```

### 1.3 Boolean variable versus expression

```javascript
let isAvailable = true; // stores a Boolean
let stock = 15;
isAvailable = stock > 0;  // evaluates an expression and stores its Boolean result
console.log(isAvailable); // true
```

---

## 2. Boolean primitives versus Boolean objects

### 2.1 `Boolean()` converts to a primitive

```javascript
let result = Boolean(1);
console.log(result);
console.log(typeof result);
```

Output:

```text
true
boolean
```

### 2.2 `new Boolean()` creates an object

```javascript
let result = new Boolean(false);
console.log(typeof result);
console.log(Boolean(result));
```

Output:

```text
object
true
```

Although the wrapper contains `false`, the wrapper object is truthy. **Use Boolean primitives; avoid `new Boolean()` in ordinary code.**

---

## 3. Understanding truthy and falsy values

JavaScript converts values to Boolean contexts automatically, for example inside an `if` condition:

```javascript
let username = "Alice";
if (username) {
  console.log("Username provided");
}
```

Output:

```text
Username provided
```

### 3.1 Truthy values

A truthy value becomes `true` when converted to a Boolean.

```javascript
console.log(Boolean(10));
console.log(Boolean(-5));
console.log(Boolean("Hello"));
console.log(Boolean("false"));
console.log(Boolean([]));
console.log(Boolean({}));
```

Output:

```text
true
true
true
true
true
true
```

`"false"` is a nonempty string; `[]` and `{}` are objects. All are truthy.

### 3.2 Falsy values

| Falsy value | Meaning |
|---|---|
| `false` | Boolean false |
| `0` | Positive zero |
| `-0` | Negative zero |
| `0n` | BigInt zero |
| `""` | Empty string |
| `null` | Intentional absence of a value |
| `undefined` | Missing or unassigned value |
| `NaN` | Not-a-Number |

There is also the unusual legacy browser value `document.all`, which is falsy for compatibility reasons. Do not use it in ordinary programs.

```javascript
console.log(Boolean(false));
console.log(Boolean(0));
console.log(Boolean(-0));
console.log(Boolean(0n));
console.log(Boolean(""));
console.log(Boolean(null));
console.log(Boolean(undefined));
console.log(Boolean(NaN));
```

Output: eight lines of `false`.

### 3.3 Values that surprise beginners

```javascript
console.log(Boolean("0"));
console.log(Boolean("false"));
console.log(Boolean(" "));
console.log(Boolean([]));
console.log(Boolean({}));
console.log(Boolean(Infinity));
```

Output: six lines of `true`.

`" "` contains a space and is therefore nonempty.

---

## 4. Truthiness explorer - static reference

The interactive lesson allowed selection of values. This Markdown-friendly reference preserves the same examples and explanations.

| Expression | Result | Explanation |
|---|---|---|
| `Boolean(0)` | `false` | Numeric zero is falsy. |
| `Boolean("0")` | `true` | The string contains one character. |
| `Boolean("")` | `false` | Empty string is falsy. |
| `Boolean("false")` | `true` | The text content does not determine truthiness. |
| `Boolean(" ")` | `true` | One space means the string is not empty. |
| `Boolean([])` | `true` | Arrays are objects. |
| `Boolean({})` | `true` | Objects are truthy. |
| `Boolean(null)` | `false` | Null is falsy. |
| `Boolean(undefined)` | `false` | Undefined is falsy. |
| `Boolean(NaN)` | `false` | NaN is falsy. |
| `Boolean(0n)` | `false` | BigInt zero is falsy. |
| `Boolean(Infinity)` | `true` | Infinity is truthy. |

Try each expression in the browser console or Node.js.

---

## 5. Explicit Boolean conversion

### 5.1 `Boolean()`

```javascript
let username = "Alice";
let hasUsername = Boolean(username);
console.log(hasUsername);
```

Output: `true`.

### 5.2 Double negation `!!`

```javascript
let username = "Alice";
let hasUsername = !!username;
console.log(hasUsername);
```

Output: `true`.

First `!"Alice"` becomes `false`; then `!false` becomes `true`.

```text
"Hello"
   |
   | !
   v
 false
   |
   | !
   v
 true
```

```javascript
console.log(Boolean("Hello")); // true
console.log(!!"Hello");       // true
```

Both produce Boolean primitives. `Boolean(value)` is often more readable for beginners.

---

## 6. Comparison operators and Boolean expressions

| Operator | Meaning | Example | Result |
|---|---|---|---|
| `>` | Greater than | `10 > 5` | `true` |
| `<` | Less than | `10 < 5` | `false` |
| `>=` | Greater or equal | `10 >= 10` | `true` |
| `<=` | Less or equal | `5 <= 10` | `true` |
| `===` | Strict equality | `10 === 10` | `true` |
| `!==` | Strict inequality | `10 !== "10"` | `true` |
| `==` | Loose equality | `10 == "10"` | `true` |
| `!=` | Loose inequality | `10 != "10"` | `false` |

### 6.1 Strict equality

```javascript
console.log(10 === 10);   // true
console.log(10 === "10"); // false
```

### 6.2 Loose equality

```javascript
console.log(10 == "10"); // true
```

Loose equality uses coercion according to JavaScript rules. **Prefer `===` and `!==` unless loose equality is specifically intended.**

### 6.3 String comparisons

```javascript
console.log("apple" === "apple"); // true
console.log("apple" === "Apple"); // false
```

String equality is case-sensitive.

---

## 7. Logical operators

| Operator | Name | Purpose |
|---|---|---|
| `&&` | AND | Combines required conditions |
| `||` | OR | Combines alternatives |
| `!` | NOT | Reverses truthiness |

---

## 8. Logical AND (&&)

With Boolean operands, AND is true only if both operands are true.

```javascript
let age = 20;
let hasPermission = true;
let canEnter = age >= 18 && hasPermission;
console.log(canEnter);
```

Output: `true`.

```text
age >= 18     --> true
hasPermission --> true
true && true  --> true
```

### 8.1 AND truth table

| A | B | A && B |
|---|---|---|
| `true` | `true` | `true` |
| `true` | `false` | `false` |
| `false` | `true` | `false` |
| `false` | `false` | `false` |

### 8.2 Student eligibility

```javascript
let score = 85;
let attendance = 90;
let isEligible = score >= 50 && attendance >= 75;
console.log(isEligible);
```

Output: `true`.

Line by line: `score` stores 85; `attendance` stores 90; `score >= 50` is true; `attendance >= 75` is true; `true && true` is true; the result is printed.

---

## 9. Logical OR (||)

With Boolean operands, OR is true if at least one operand is true.

```javascript
let isAdmin = false;
let isModerator = true;
let canManageContent = isAdmin || isModerator;
console.log(canManageContent);
```

Output: `true`.

### 9.1 OR truth table

| A | B | A \|\| B |
|---|---|---|
| `true` | `true` | `true` |
| `true` | `false` | `true` |
| `false` | `true` | `true` |
| `false` | `false` | `false` |

### 9.2 Discount eligibility

```javascript
let isStudent = true;
let isSenior = false;
let qualifiesForDiscount = isStudent || isSenior;
console.log(qualifiesForDiscount);
```

Output: `true`, because at least one condition is true.

---

## 10. Logical NOT (!)

NOT converts its operand to a Boolean and reverses it.

```javascript
let isLoggedIn = false;
console.log(!isLoggedIn);
```

Output: `true`.

| A | !A |
|---|---|
| `true` | `false` |
| `false` | `true` |

```javascript
let age = 15;
let isAdult = age >= 18;
console.log(isAdult);  // false
console.log(!isAdult); // true
```

With non-Boolean operands:

```javascript
console.log(!0);       // true
console.log(!"Hello"); // false
console.log(![]);      // false
```

---

## 11. Logical operator truth tables

The interactive lesson let you change operands. These tables preserve every possible Boolean combination.

### AND

| A | B | Result |
|---|---|---|
| `true` | `true` | `true` |
| `true` | `false` | `false` |
| `false` | `true` | `false` |
| `false` | `false` | `false` |

### OR

| A | B | Result |
|---|---|---|
| `true` | `true` | `true` |
| `true` | `false` | `true` |
| `false` | `true` | `true` |
| `false` | `false` | `false` |

### NOT

| A | Result |
|---|---|
| `true` | `false` |
| `false` | `true` |

---

## 12. Logical operators can return non-Boolean values

### 12.1 AND returns operands

```javascript
console.log("Hello" && "World"); // World
console.log(0 && "Hello");       // 0
```

AND returns the first falsy operand, or the final operand if none is falsy.

```javascript
console.log(10 && 20);           // 20
console.log(0 && 20);            // 0
console.log("A" && "B" && "C"); // C
console.log("A" && "" && "C");  // "" (blank line)
```

### 12.2 OR returns operands

```javascript
console.log("Hello" || "World"); // Hello
console.log(0 || 100);           // 100
```

OR returns the first truthy operand, or the final operand if none is truthy.

```javascript
console.log(0 || 50);                     // 50
console.log("" || "Guest");              // Guest
console.log("Alice" || "Guest");         // Alice
console.log(null || undefined || "Default"); // Default
```

### 12.3 Comparison

| Expression | Result | Why |
|---|---|---|
| `10 && 20` | `20` | Both truthy; last returned |
| `0 && 20` | `0` | First falsy operand |
| `"" && "Hello"` | `""` | First falsy operand |
| `10 \|\| 20` | `10` | First truthy operand |
| `0 \|\| 20` | `20` | First truthy operand |
| `"" \|\| "Guest"` | `"Guest"` | Empty string falsy |

---

## 13. Short-circuit evaluation

JavaScript may skip the right operand when the left operand determines the logical result.

### 13.1 AND short circuit

```javascript
let isLoggedIn = false;
let result = isLoggedIn && console.log("Welcome");
console.log(result);
```

Output:

```text
false
```

`"Welcome"` is not printed because the right operand is not evaluated.

### 13.2 OR short circuit

```javascript
let username = "Alice";
let displayName = username || "Guest";
console.log(displayName);
```

Output: `Alice`.

### 13.3 Guarding an operation

```javascript
let user = { name: "Alice" };
user && console.log(user.name);
```

Output: `Alice`.

Optional chaining can be clearer when accessing a potentially missing property:

```javascript
console.log(user?.name); // Alice
```

---

## 14. Nullish coalescing operator (??)

`??` uses its fallback only when the left operand is `null` or `undefined`.

```javascript
let username = null;
let displayName = username ?? "Guest";
console.log(displayName);
```

Output: `Guest`.

### 14.1 OR versus nullish coalescing

```javascript
let quantity = 0;
console.log(quantity || 10); // 10
console.log(quantity ?? 10); // 0
```

| Value | `value \|\| "Default"` | `value ?? "Default"` |
|---|---|---|
| `0` | `"Default"` | `0` |
| `false` | `"Default"` | `false` |
| `""` | `"Default"` | `""` |
| `null` | `"Default"` | `"Default"` |
| `undefined` | `"Default"` | `"Default"` |
| `"Alice"` | `"Alice"` | `"Alice"` |

### 14.2 Practical example

```javascript
let selectedQuantity = 0;
let quantity = selectedQuantity ?? 1;
console.log(quantity); // 0
```

**Use `??` when zero, false, or empty string should be preserved.**

---

## 15. Logical assignment operators

| Operator | Assigns right side when left side is |
|---|---|
| `&&=` | Truthy |
| `||=` | Falsy |
| `??=` | Nullish (`null` or `undefined`) |

### 15.1 AND assignment

```javascript
let value = 10;
value &&= 20;
console.log(value); // 20
```

```javascript
let value = 0;
value &&= 20;
console.log(value); // 0
```

### 15.2 OR assignment

```javascript
let username = "";
username ||= "Guest";
console.log(username); // Guest
```

### 15.3 Nullish assignment

```javascript
let quantity = 0;
quantity ??= 1;
console.log(quantity); // 0
```

---

## 16. Operator precedence in logical expressions

```javascript
let result = true || false && false;
console.log(result); // true
```

AND has higher precedence than OR, so this is grouped as:

```javascript
true || (false && false)
```

The inner AND is false, and `true || false` is true.

### 16.1 Simplified precedence

| Priority | Operators |
|---|---|
| Highest | Parentheses `()` |
| Higher | Logical NOT `!` |
| Higher | Relational `<`, `>`, `<=`, `>=` |
| Higher | Equality `===`, `!==`, `==`, `!=` |
| Higher | AND `&&` |
| Lower | OR `||` |
| Lower | Nullish `??` |
| Lowest among these | Assignment |

Note: `||` and `??` cannot be mixed unparenthesized, even though they have defined precedence levels.

Invalid:

```javascript
let result = null || false ?? "Default"; // SyntaxError
```

Correct:

```javascript
let result = (null || false) ?? "Default";
console.log(result); // false
```

### 16.2 Use parentheses for clarity

```javascript
let age = 20;
let isStudent = false;
let hasMembership = true;
let result = age >= 18 && isStudent || hasMembership;
console.log(result); // true
```

Equivalent grouping:

```javascript
(age >= 18 && isStudent) || hasMembership
```

---

## 17. Combining multiple logical conditions

### 17.1 Account access

```javascript
let isLoggedIn = true;
let isVerified = true;
let isSuspended = false;
let canAccess = isLoggedIn && isVerified && !isSuspended;
console.log(canAccess);
```

Output: `true`.

Line by line: logged in is true; verified is true; suspended is false; NOT suspended is true; all three required conditions are true.

### 17.2 Eligibility with alternatives

```javascript
let age = 22;
let hasTicket = false;
let hasInvitation = true;
let canAttend = age >= 18 && (hasTicket || hasInvitation);
console.log(canAttend);
```

Output: `true`.

The parentheses group the ticket/invitation alternatives.

---

## 18. De Morgan's laws

De Morgan's laws transform negations of compound Boolean conditions.

### 18.1 First law

```text
!(A && B)  <=>  !A || !B
```

```javascript
let hasEmail = true;
let hasPhone = false;
console.log(!(hasEmail && hasPhone)); // true
console.log(!hasEmail || !hasPhone);  // true
```

### 18.2 Second law

```text
!(A || B)  <=>  !A && !B
```

```javascript
let isAdmin = false;
let isModerator = false;
console.log(!(isAdmin || isModerator)); // true
console.log(!isAdmin && !isModerator);  // true
```

These laws describe equivalent Boolean truth values. With non-Boolean operands, remember that AND and OR may return operand values rather than Boolean primitives.

---

## 19. Practical Boolean logic examples

### Example 1 - Password length validation

```javascript
let password = "MyPassword123";
let isValidLength = password.length >= 8 && password.length <= 20;
console.log(isValidLength); // true
```

This checks length only, not overall password security.

### Example 2 - Score range

```javascript
let score = 75;
let isValidScore = score >= 0 && score <= 100;
console.log(isValidScore); // true
```

### Example 3 - Empty shopping cart

```javascript
let cart = [];
let isEmpty = cart.length === 0;
console.log(isEmpty); // true
```

`Boolean(cart)` would be true, because an array is an object.

### Example 4 - Default display name

```javascript
let username = "";
let displayName = username || "Guest";
console.log(displayName); // Guest
```

### Example 5 - Preserving zero

```javascript
let selectedQuantity = 0;
let quantity = selectedQuantity ?? 1;
console.log(quantity); // 0
```

### Example 6 - Multiple requirements

```javascript
let age = 25;
let hasIdentification = true;
let isBlocked = false;
let isEligible = age >= 18 && hasIdentification && !isBlocked;
console.log(isEligible); // true
```

---

## 20. Common mistakes beginners make

### Mistake 1 - Thinking `"false"` is falsy

```javascript
console.log(Boolean("false")); // true
```

When parsing a known text format:

```javascript
let input = "false";
let result = input === "true";
console.log(result); // false
```

### Mistake 2 - Thinking an empty array is falsy

```javascript
let items = [];
console.log(Boolean(items)); // true
console.log(items.length > 0); // false
```

### Mistake 3 - Thinking AND always returns Boolean

```javascript
console.log("Hello" && "World"); // World
```

### Mistake 4 - Thinking OR always returns Boolean

```javascript
console.log(0 || 100); // 100
```

### Mistake 5 - Using OR when zero is valid

```javascript
let quantity = 0;
console.log(quantity || 1); // 1
console.log(quantity ?? 1); // 0
```

### Mistake 6 - Confusing assignment with comparison

Incorrect:

```javascript
let age = 20;
if (age = 18) {
  console.log("Condition passed");
}
```

This assigns 18, which is truthy. Correct:

```javascript
let age = 20;
if (age === 18) {
  console.log("Age is exactly 18");
}
```

### Mistake 7 - Forgetting precedence

```javascript
let result = true || false && false; // true
```

Use parentheses to make the intended grouping clear.

---

## 21. Knowledge assessment

Answer before checking the answer key in [Section 23](#23-assessment-answers-and-explanations).

**Question 1.** What is the output?

```javascript
console.log(Boolean("false"));
```

A. `false`  
B. `true`  
C. `undefined`  
D. `NaN`

**Question 2.** What is printed?

```javascript
console.log(Boolean([]));
```

A. `false`  
B. `true`  
C. `undefined`  
D. Error

**Question 3.** What is the result?

```javascript
console.log(true && false);
```

A. `true`  
B. `false`  
C. `0`  
D. `undefined`

**Question 4.** What is printed?

```javascript
console.log(false || true);
```

A. `false`  
B. `true`  
C. `null`  
D. `undefined`

**Question 5.** What is the output?

```javascript
console.log(!0);
```

A. `0`  
B. `false`  
C. `true`  
D. `undefined`

**Question 6.** What is returned?

```javascript
console.log("Hello" && "World");
```

A. `true`  
B. `false`  
C. `Hello`  
D. `World`

**Question 7.** What is printed?

```javascript
console.log(0 || 100);
```

A. `0`  
B. `100`  
C. `true`  
D. `false`

**Question 8.** What is the result?

```javascript
console.log(0 ?? 10);
```

A. `0`  
B. `10`  
C. `false`  
D. `undefined`

**Question 9.** What is printed?

```javascript
console.log(true || false && false);
```

A. `true`  
B. `false`  
C. `undefined`  
D. Error

**Question 10.** What is the output?

```javascript
console.log(!(true && false));
```

A. `true`  
B. `false`  
C. `null`  
D. Error

---

## 22. Practical coding exercises

Complete these independently before reading the model solutions.

### Exercise 1 - Boolean variables

```javascript
let isLoggedIn = true;
let isVerified = false;
let isSuspended = false;
```

Print each value and its type using `typeof`.

### Exercise 2 - Truthy and falsy

Predict and explain each result:

```javascript
console.log(Boolean(0));
console.log(Boolean("0"));
console.log(Boolean(""));
console.log(Boolean("false"));
console.log(Boolean([]));
console.log(Boolean({}));
console.log(Boolean(null));
```

### Exercise 3 - Logical AND

```javascript
let age = 21;
let hasPermission = true;
```

Write an expression that is true only when age is at least 18 **and** permission is granted.

### Exercise 4 - Logical OR

```javascript
let isStudent = false;
let isSenior = true;
```

Write an expression that is true when either condition is true.

### Exercise 5 - Logical NOT

```javascript
let isBlocked = false;
```

Use NOT to create `isAllowed` and print the result.

### Exercise 6 - Short-circuit evaluation

Predict and explain:

```javascript
console.log(0 && "Hello");
console.log("Hello" && "World");
console.log("" || "Guest");
console.log("Alice" || "Guest");
console.log(null ?? "Default");
console.log(0 ?? 100);
```

### Exercise 7 - Nullish coalescing

```javascript
let quantity = 0;
let fallbackQuantity = 5;
```

Compare `quantity || fallbackQuantity` with `quantity ?? fallbackQuantity`. Which preserves a valid zero?

### Exercise 8 - Compound conditions

```javascript
let age = 25;
let hasTicket = false;
let hasInvitation = true;
```

Return true when age is at least 18 **and** the person has a ticket **or** an invitation.

### Exercise 9 - De Morgan's laws

```javascript
let a = true;
let b = false;
```

Show that `!(a && b)` and `!a || !b` have the same Boolean result; then show that `!(a || b)` and `!a && !b` have the same Boolean result.

### Exercise 10 - Mini project: Account access validator

```javascript
let isLoggedIn = true;
let isEmailVerified = true;
let isSuspended = false;
let age = 22;
```

Requirements:

1. User must be logged in.
2. Email must be verified.
3. Account must not be suspended.
4. Age must be at least 18.
5. Store the Boolean in `canAccess`.
6. Print the Boolean.
7. Print `"Access granted"` or `"Access denied"` using a conditional statement.

Expected output:

```text
true
Access granted
```

---

## 23. Assessment answers and explanations

| Question | Answer | Explanation |
|---|---|---|
| 1 | **B - `true`** | `"false"` is a nonempty string and therefore truthy. |
| 2 | **B - `true`** | An array is an object, even when empty. |
| 3 | **B - `false`** | Boolean AND requires both operands to be true. |
| 4 | **B - `true`** | Boolean OR requires at least one operand to be true. |
| 5 | **C - `true`** | Zero is falsy; NOT reverses its truthiness. |
| 6 | **D - `World`** | Both strings are truthy; AND returns the last operand. |
| 7 | **B - `100`** | Zero is falsy; OR returns the first truthy operand. |
| 8 | **A - `0`** | Zero is not nullish, so `??` preserves it. |
| 9 | **A - `true`** | AND groups before OR: `true || (false && false)`. |
| 10 | **A - `true`** | `true && false` is false, then NOT makes it true. |

### Expanded reasoning

**1.** `Boolean("false")` checks whether the string is empty, not whether it spells the word false. Because it has characters, it converts to `true`.

**2.** Arrays are reference values. Even an empty array is an object, and objects are truthy.

**3.** `true && false` returns `false` because the second operand is false.

**4.** `false || true` returns `true` because the second operand is true.

**5.** `!0` converts zero to false and negates it, yielding true.

**6.** `"Hello" && "World"` evaluates the first string as truthy and returns the second string.

**7.** `0 || 100` skips the falsy zero and returns the truthy 100.

**8.** `0 ?? 10` returns 0 because the fallback is only used for null or undefined.

**9.** `&&` has higher precedence than `||`; the inner AND is false, so the expression is `true || false`, which is true.

**10.** `!(true && false)` becomes `!false`, then `true`.

---

## 24. Exercise model solutions

### Solution 1 - Boolean variables

```javascript
let isLoggedIn = true;
let isVerified = false;
let isSuspended = false;

console.log(isLoggedIn, typeof isLoggedIn);
console.log(isVerified, typeof isVerified);
console.log(isSuspended, typeof isSuspended);
```

Output:

```text
true boolean
false boolean
false boolean
```

**Explanation:** Each variable stores a Boolean primitive. `typeof` returns `"boolean"` for both true and false.

### Solution 2 - Truthy and falsy

```javascript
console.log(Boolean(0));
console.log(Boolean("0"));
console.log(Boolean(""));
console.log(Boolean("false"));
console.log(Boolean([]));
console.log(Boolean({}));
console.log(Boolean(null));
```

Output:

```text
false
true
false
true
true
true
false
```

**Explanation, line by line:** Numeric zero is falsy. The string `"0"` contains a character, so it is truthy. An empty string is falsy. The string `"false"` is nonempty and truthy. Empty arrays and objects are truthy because they are objects. `null` is falsy.

### Solution 3 - Logical AND

```javascript
let age = 21;
let hasPermission = true;
let canEnter = age >= 18 && hasPermission;
console.log(canEnter);
```

Output:

```text
true
```

**Explanation:** `age >= 18` is true; `hasPermission` is true. Both requirements hold, so AND returns true. Changing either condition to false makes `canEnter` false.

### Solution 4 - Logical OR

```javascript
let isStudent = false;
let isSenior = true;
let qualifiesForDiscount = isStudent || isSenior;
console.log(qualifiesForDiscount);
```

Output:

```text
true
```

**Explanation:** The first operand is false, so OR checks the second. The second is true, so the overall result is true.

### Solution 5 - Logical NOT

```javascript
let isBlocked = false;
let isAllowed = !isBlocked;
console.log(isAllowed);
```

Output:

```text
true
```

**Explanation:** NOT reverses the Boolean value of `isBlocked`, producing true.

### Solution 6 - Short-circuit evaluation

```javascript
console.log(0 && "Hello");
console.log("Hello" && "World");
console.log("" || "Guest");
console.log("Alice" || "Guest");
console.log(null ?? "Default");
console.log(0 ?? 100);
```

Output:

```text
0
World
Guest
Alice
Default
0
```

**Explanation:**

1. `0 && "Hello"` returns the first falsy operand, `0`; the right operand is skipped.
2. `"Hello" && "World"` returns `"World"` because both operands are truthy.
3. `"" || "Guest"` returns `"Guest"` because the empty string is falsy.
4. `"Alice" || "Guest"` returns `"Alice"` immediately because it is truthy.
5. `null ?? "Default"` returns the fallback because null is nullish.
6. `0 ?? 100` preserves zero because it is not nullish.

### Solution 7 - Nullish coalescing

```javascript
let quantity = 0;
let fallbackQuantity = 5;

let usingOr = quantity || fallbackQuantity;
let usingNullish = quantity ?? fallbackQuantity;

console.log(usingOr);
console.log(usingNullish);
```

Output:

```text
5
0
```

**Explanation:** OR treats zero as falsy and chooses 5. Nullish coalescing only falls back for null or undefined, so it preserves the valid zero. Use `??` when zero is a meaningful input.

### Solution 8 - Compound conditions

```javascript
let age = 25;
let hasTicket = false;
let hasInvitation = true;

let canAttend = age >= 18 && (hasTicket || hasInvitation);
console.log(canAttend);
```

Output:

```text
true
```

**Explanation:** `age >= 18` is true. Inside the parentheses, `false || true` is true. The final AND is `true && true`, so the result is true. Parentheses express the requirement that the person must be an adult and have at least one of the two credentials.

### Solution 9 - De Morgan's laws

```javascript
let a = true;
let b = false;

let firstLeft = !(a && b);
let firstRight = !a || !b;
let secondLeft = !(a || b);
let secondRight = !a && !b;

console.log(firstLeft);
console.log(firstRight);
console.log(secondLeft);
console.log(secondRight);
console.log(firstLeft === firstRight);
console.log(secondLeft === secondRight);
```

Output:

```text
true
true
false
false
true
true
```

**Explanation:**

- First law: `!(true && false)` becomes `!false`, which is true. `!true || !false` becomes `false || true`, also true.
- Second law: `!(true || false)` becomes `!true`, which is false. `!true && !false` becomes `false && true`, also false.
- Both strict equality checks are true because the paired expressions have equal Boolean results.

### Solution 10 - Mini project: Account access validator

```javascript
let isLoggedIn = true;
let isEmailVerified = true;
let isSuspended = false;
let age = 22;

let canAccess =
  isLoggedIn &&
  isEmailVerified &&
  !isSuspended &&
  age >= 18;

console.log(canAccess);

if (canAccess) {
  console.log("Access granted");
} else {
  console.log("Access denied");
}
```

Output:

```text
true
Access granted
```

**Detailed walkthrough:**

1. `isLoggedIn` is true, so the login requirement passes.
2. `isEmailVerified` is true, so the verification requirement passes.
3. `isSuspended` is false, and `!isSuspended` evaluates to true.
4. `age >= 18` evaluates to `22 >= 18`, which is true.
5. All four operands of AND are true, so `canAccess` is true.
6. The first `console.log()` prints `true`.
7. The `if` condition evaluates to true, so `"Access granted"` is printed; the `else` branch is skipped.

**Try changing inputs:**

```javascript
isSuspended = true;
canAccess = isLoggedIn && isEmailVerified && !isSuspended && age >= 18;
console.log(canAccess); // false
```

**Improvement for readability:** Give each requirement a descriptive Boolean name:

```javascript
const meetsAgeRequirement = age >= 18;
const hasActiveAccount = !isSuspended;
const canAccessFeature =
  isLoggedIn && isEmailVerified && hasActiveAccount && meetsAgeRequirement;
console.log(canAccessFeature);
```

This makes the decision rule easier to read and test. These client-side checks are instructional only; real authorization must also be enforced by a trusted backend.

---

## 25. Lesson summary

| Concept | Key takeaway |
|---|---|
| Boolean | Primitive `true` or `false` |
| Truthy | Converts to true in a Boolean context |
| Falsy | Converts to false in a Boolean context |
| `Boolean()` | Explicit conversion |
| `!!` | Double negation converts to Boolean |
| `===` | Strict equality |
| `!==` | Strict inequality |
| `&&` | First falsy operand, otherwise last operand |
| `||` | First truthy operand, otherwise last operand |
| `!` | Negates truthiness, returns Boolean |
| Short circuit | Skips unnecessary operand evaluation |
| `??` | Fallback only for null/undefined |
| `&&=` | Assign when current value truthy |
| `||=` | Assign when current value falsy |
| `??=` | Assign when current value nullish |
| Precedence | Determines expression grouping |
| De Morgan's laws | Transform negated compound conditions |

### Eight principles to remember

1. Booleans have two primitive values: `true` and `false`.
2. Truthy does not mean strictly equal to `true`.
3. Empty arrays and objects are truthy; inspect their contents.
4. AND and OR may return non-Boolean operands.
5. Short-circuiting can skip expressions.
6. Use `??` when zero, false, or empty string must be preserved.
7. Use parentheses to clarify complex conditions.
8. Prefer `===` and `!==` for comparisons.

## Lesson navigation

| Direction | Lesson |
|---|---|
| Previous | [Unit 1 - Lesson 5: Numbers and Math Methods](05-numbers-and-math-methods.md) |
| Current | **Unit 1 - Lesson 6: Booleans, Truthy and Falsy Values, and Logical Expressions** |
| Next | [Unit 1 - Lesson 7: Conditional Statements and Decision Making](07-conditional-statements-and-decision-making.md) |

**Next step:** Complete the assessment and practical exercises. The next lesson covers `if`, `else if`, `else`, `switch`, and decision-making patterns.
