# Unit 1 - Lesson 7: JavaScript Conditional Statements and Decision Making

**Level:** Beginner to intermediate  
**Previous:** [Lesson 6 - Booleans, Truthy and Falsy Values, and Logical Expressions](06-booleans-truthy-falsy-and-logical-expressions.md)  
**Next:** [Lesson 8 - JavaScript Loops and Iteration](08-loops-and-iteration.md)

## Table of contents

1. [Lesson overview and objectives](#lesson-overview-and-objectives)
2. [Understanding control flow](#1-understanding-control-flow)
3. [The if statement](#2-the-if-statement)
4. [The if...else statement](#3-the-ifelse-statement)
5. [Interactive example explained](#4-interactive-example-explained)
6. [The else if statement](#5-the-else-if-statement)
7. [Nested conditional statements](#6-nested-conditional-statements)
8. [Combining conditions](#7-combining-conditions-with-logical-operators)
9. [The ternary operator](#8-the-ternary-operator)
10. [Ternary example explained](#9-ternary-example-explained)
11. [The switch statement](#10-the-switch-statement)
12. [Understanding break](#11-understanding-break-in-switch)
13. [Grouping switch cases](#12-grouping-switch-cases)
14. [Understanding default](#13-understanding-default-in-switch)
15. [Switch example explained](#14-switch-example-explained)
16. [Comparing decision structures](#15-comparing-ifelse-switch-and-ternary)
17. [Guard clauses and early returns](#16-guard-clauses-and-early-returns)
18. [Variable scope](#17-variable-scope-inside-conditional-blocks)
19. [Common mistakes](#18-common-mistakes-beginners-make)
20. [Practical examples](#19-practical-examples)
21. [Knowledge assessment and answers](#20-knowledge-assessment-with-answers)
22. [Coding exercises](#21-practical-coding-exercises)
23. [Model solutions](#22-complete-model-solutions)
24. [Lesson summary](#23-lesson-summary)
25. [Lesson navigation](#lesson-navigation)

---

## Lesson overview and objectives

Conditional statements control which parts of a JavaScript program execute. For example:

```javascript
let score = 85;
if (score >= 50) {
  console.log("You passed");
} else {
  console.log("You failed");
}
```

**Output:** `You passed`.

The expression `score >= 50` is true, so the first block runs and the second is skipped.

**Learning objectives:** Explain control flow; use `if`, `else`, `else if`, and nesting; combine conditions; write ternary expressions; use `switch`, `case`, `break`, `default`, and fall-through; choose suitable decision structures; understand guard clauses, scope, and common errors; build practical programs.

---

## 1. Understanding control flow

**Control flow** is the order in which program statements execute. By default, JavaScript executes statements from top to bottom.

```javascript
console.log("Step 1");
console.log("Step 2");
console.log("Step 3");
```

**Output:**

```text
Step 1
Step 2
Step 3
```

Conditional execution changes that sequence:

```javascript
let score = 75;
if (score >= 50) {
  console.log("Pass");
}
```

**Output:** `Pass`.

```text
Sequential: Start --> Step 1 --> Step 2 --> Step 3 --> End

Conditional:            +-- true  --> Action A --+
Start --> Condition? ---+                       +--> End
                        +-- false --> Action B --+
```

---

## 2. The if statement

The `if` statement executes a block when its condition is **truthy**.

**Syntax:**

```javascript
if (condition) {
  // Runs when condition is truthy
}
```

| Component | Purpose |
|---|---|
| `if` | Keyword introducing the condition |
| `(condition)` | Expression evaluated for truthiness |
| `{ }` | Block of statements |
| Statements inside block | Execute only when condition is truthy |

**Example:**

```javascript
let age = 20;
if (age >= 18) {
  console.log("You are an adult");
}
```

**Output:** `You are an adult`.

**Line by line:** `let age = 20` stores the age. `age >= 18` compares 20 with 18 and evaluates to `true`. JavaScript executes the `console.log` inside the block.

**False condition:**

```javascript
let age = 15;
if (age >= 18) {
  console.log("You are an adult");
}
console.log("Program finished");
```

**Output:** `Program finished` only. The statement after the block executes regardless.

Conditions can use truthy values:

```javascript
let username = "Alice";
if (username) {
  console.log("Username provided");
}
```

**Output:** `Username provided`. A nonempty string is truthy. An explicit check such as `username.length > 0` may communicate the intention more clearly.

---

## 3. The if...else statement

`if...else` chooses between two alternatives.

```javascript
if (condition) {
  // Truthy branch
} else {
  // Falsy branch
}
```

**Pass/fail example:**

```javascript
let score = 45;
if (score >= 50) {
  console.log("Pass");
} else {
  console.log("Fail");
}
```

**Output:** `Fail`.

**Execution:** Evaluate `45 >= 50` (false), skip the `if` block, execute the `else` block.

**Even/odd example:**

```javascript
let number = 12;
if (number % 2 === 0) {
  console.log("Even number");
} else {
  console.log("Odd number");
}
```

**Output:** `Even number` because `12 % 2` is `0`. This assumes an integer input.

---

## 4. Interactive example explained

Try changing `score` to `49`, `50`, and `75` and predict the branch:

```javascript
let score = 75;
if (score >= 50) {
  console.log("Pass");
} else {
  console.log("Fail");
}
```

| Score | Condition | Output |
|---:|---|---|
| 49 | `49 >= 50` is false | `Fail` |
| 50 | `50 >= 50` is true | `Pass` |
| 75 | `75 >= 50` is true | `Pass` |

**Practice:** Run the code with all three values. This table preserves the lesson's interactive demonstration in a GitHub-readable format.

---

## 5. The else if statement

Use `else if` when there are more than two possibilities. Conditions are evaluated top to bottom, and the first truthy branch executes.

```javascript
if (condition1) {
  // First branch
} else if (condition2) {
  // Second branch
} else {
  // Fallback
}
```

**Grade classification:**

```javascript
let score = 82;
if (score >= 90) {
  console.log("Grade A");
} else if (score >= 80) {
  console.log("Grade B");
} else if (score >= 70) {
  console.log("Grade C");
} else if (score >= 60) {
  console.log("Grade D");
} else {
  console.log("Grade F");
}
```

**Output:** `Grade B`.

**Execution:** `82 >= 90` is false; `82 >= 80` is true; `Grade B` prints; remaining branches are skipped.

**Order matters:**

```javascript
let score = 95;
if (score >= 50) {
  console.log("Pass");
} else if (score >= 90) {
  console.log("Excellent");
}
```

**Output:** `Pass` because the first condition already matches.

**Correct order:**

```javascript
let score = 95;
if (score >= 90) {
  console.log("Excellent");
} else if (score >= 50) {
  console.log("Pass");
} else {
  console.log("Fail");
}
```

**Output:** `Excellent`.

**Temperature example (illustrative thresholds):**

```javascript
let temperature = 28;
if (temperature >= 35) {
  console.log("Very hot");
} else if (temperature >= 25) {
  console.log("Warm");
} else if (temperature >= 15) {
  console.log("Cool");
} else {
  console.log("Cold");
}
```

**Output:** `Warm`.

---

## 6. Nested conditional statements

A nested conditional places an `if` inside another conditional.

```javascript
let isLoggedIn = true;
let isVerified = true;

if (isLoggedIn) {
  if (isVerified) {
    console.log("Access granted");
  } else {
    console.log("Verify your account");
  }
} else {
  console.log("Please log in");
}
```

**Output:** `Access granted`.

```text
START
  |
  v
Logged in?
  +-- No  --> Please log in
  +-- Yes --> Verified?
                +-- No  --> Verify your account
                +-- Yes --> Access granted
```

**Simpler alternative:**

```javascript
let isLoggedIn = true;
let isVerified = true;
if (isLoggedIn && isVerified) {
  console.log("Access granted");
} else {
  console.log("Access denied");
}
```

**Output:** `Access granted`. The simpler version loses the distinct failure messages. Choose based on requirements.

---

## 7. Combining conditions with logical operators

| Operator | Name | Meaning |
|---|---|---|
| `&&` | AND | Both operands must be truthy for a true Boolean result |
| `||` | OR | At least one operand must be truthy for a true Boolean result |
| `!` | NOT | Reverses truthiness |

**AND:**

```javascript
let age = 22;
let hasTicket = true;
if (age >= 18 && hasTicket) {
  console.log("Entry allowed");
} else {
  console.log("Entry denied");
}
```

**Output:** `Entry allowed`.

**OR:**

```javascript
let isStudent = false;
let isSenior = true;
if (isStudent || isSenior) {
  console.log("Discount available");
} else {
  console.log("Standard price");
}
```

**Output:** `Discount available`.

**NOT:**

```javascript
let isBlocked = false;
if (!isBlocked) {
  console.log("Account active");
} else {
  console.log("Account blocked");
}
```

**Output:** `Account active`.

**Compound grouping:**

```javascript
let age = 25;
let hasTicket = false;
let hasInvitation = true;
if (age >= 18 && (hasTicket || hasInvitation)) {
  console.log("Entry allowed");
} else {
  console.log("Entry denied");
}
```

**Output:** `Entry allowed`.

```text
Age >= 18 AND (Has ticket OR Has invitation)
```

---

## 8. The ternary operator

The ternary operator is an **expression** that selects one of two results.

**Syntax:**

```javascript
condition ? expressionIfTrue : expressionIfFalse;
```

**Age example:**

```javascript
let age = 20;
let status = age >= 18 ? "Adult" : "Minor";
console.log(status);
```

**Output:** `Adult`.

**Equivalent if...else:**

```javascript
let age = 20;
let status;
if (age >= 18) {
  status = "Adult";
} else {
  status = "Minor";
}
console.log(status);
```

**Output:** `Adult`.

**Even/odd:**

```javascript
let number = 7;
let result = number % 2 === 0 ? "Even" : "Odd";
console.log(result);
```

**Output:** `Odd`.

**Larger number:**

```javascript
let a = 15;
let b = 25;
let larger = a > b ? a : b;
console.log(larger);
```

**Output:** `25`.

**Nested ternary:**

```javascript
let score = 85;
let grade =
  score >= 90 ? "A" :
  score >= 80 ? "B" :
  score >= 70 ? "C" :
  "F";
console.log(grade);
```

**Output:** `B`. Deeply nested ternaries are often harder to read than `if...else if`.

---

## 9. Ternary example explained

```javascript
let age = 20;
let status = age >= 18 ? "Adult" : "Minor";
```

| Age | `age >= 18` | Selected value |
|---:|---|---|
| 17 | false | `Minor` |
| 18 | true | `Adult` |
| 20 | true | `Adult` |

The unselected expression is not evaluated. Try the three values to reproduce the interactive lesson example.

---

## 10. The switch statement

`switch` compares one expression against multiple fixed case labels.

```javascript
switch (expression) {
  case value1:
    // Statements
    break;
  case value2:
    // Statements
    break;
  default:
    // Fallback statements
}
```

| Component | Purpose |
|---|---|
| `switch` | Starts the selection |
| `expression` | Value to evaluate |
| `case` | Potential match |
| `break` | Exits the switch |
| `default` | Handles no matching case |

**Day of the week:**

```javascript
let day = 3;
switch (day) {
  case 1: console.log("Monday"); break;
  case 2: console.log("Tuesday"); break;
  case 3: console.log("Wednesday"); break;
  case 4: console.log("Thursday"); break;
  case 5: console.log("Friday"); break;
  case 6: console.log("Saturday"); break;
  case 7: console.log("Sunday"); break;
  default: console.log("Invalid day");
}
```

**Output:** `Wednesday`. JavaScript evaluates `day` as `3`, matches `case 3`, prints, and exits on `break`.

**Strict matching:**

```javascript
let day = "3";
switch (day) {
  case 3: console.log("Wednesday"); break;
  default: console.log("No match");
}
```

**Output:** `No match`. The string `"3"` does not match numeric `3`.

---

## 11. Understanding break in switch

**With break:**

```javascript
let number = 2;
switch (number) {
  case 1: console.log("One"); break;
  case 2: console.log("Two"); break;
  case 3: console.log("Three"); break;
}
```

**Output:** `Two`.

**Without break:**

```javascript
let number = 2;
switch (number) {
  case 1: console.log("One");
  case 2: console.log("Two");
  case 3: console.log("Three");
}
```

**Output:**

```text
Two
Three
```

This is **fall-through**: after a matching case, execution continues into subsequent case bodies until `break`, `return`, another exit, or the end of the switch.

---

## 12. Grouping switch cases

Intentional fall-through can group multiple labels:

```javascript
let day = "Saturday";
switch (day) {
  case "Saturday":
  case "Sunday":
    console.log("Weekend");
    break;
  case "Monday":
  case "Tuesday":
  case "Wednesday":
  case "Thursday":
  case "Friday":
    console.log("Weekday");
    break;
  default:
    console.log("Invalid day");
}
```

**Output:** `Weekend`.

---

## 13. Understanding default in switch

The `default` clause handles unmatched values:

```javascript
let color = "Purple";
switch (color) {
  case "Red": console.log("Red selected"); break;
  case "Blue": console.log("Blue selected"); break;
  case "Green": console.log("Green selected"); break;
  default: console.log("Unknown color");
}
```

**Output:** `Unknown color`.

---

## 14. Switch example explained

For the day-of-week switch, experiment with these inputs:

| `day` | Matching case | Output |
|---:|---|---|
| 1 | `case 1` | `Monday` |
| 3 | `case 3` | `Wednesday` |
| 5 | `case 5` | `Friday` |
| 7 | `case 7` | `Sunday` |
| 8 | `default` | `Invalid day` |

This table is a static equivalent of the lesson's interactive switch demonstration.

---

## 15. Comparing if...else, switch, and ternary

| Feature | `if...else` | `switch` | Ternary |
|---|---|---|---|
| Multiple conditions | Yes | Fixed case matches | Possible, but nesting gets complex |
| Ranges | Excellent | Less convenient | Simple ranges |
| Exact matching | Yes | Excellent | Yes |
| Directly produces a value | No | No | Yes |
| Complex decisions | Often best | Good for fixed alternatives | Usually avoid |

**Choose `if...else` for ranges:**

```javascript
let score = 85;
if (score >= 80) console.log("High score");
else console.log("Lower score");
```

**Output:** `High score`.

**Choose `switch` for fixed states:**

```javascript
let status = "approved";
switch (status) {
  case "pending": console.log("Waiting"); break;
  case "approved": console.log("Accepted"); break;
  default: console.log("Unknown status");
}
```

**Output:** `Accepted`.

**Choose ternary for simple selection:**

```javascript
let isActive = true;
let label = isActive ? "Active" : "Inactive";
console.log(label);
```

**Output:** `Active`.

---

## 16. Guard clauses and early returns

A **guard clause** handles invalid or exceptional conditions early and returns from a function.

**Nested approach:**

```javascript
function checkAccess(age, isVerified) {
  if (age >= 18) {
    if (isVerified) {
      return "Access granted";
    } else {
      return "Account not verified";
    }
  } else {
    return "Age requirement not met";
  }
}
console.log(checkAccess(22, true));
```

**Output:** `Access granted`.

**Guard clause approach:**

```javascript
function checkAccess(age, isVerified) {
  if (age < 18) {
    return "Age requirement not met";
  }
  if (!isVerified) {
    return "Account not verified";
  }
  return "Access granted";
}
console.log(checkAccess(22, true));
```

**Output:** `Access granted`.

Guard clauses reduce nesting, clarify failure conditions, and keep the success path readable. `return` exits the current function, not the entire program.

---

## 17. Variable scope inside conditional blocks

`let` and `const` are **block-scoped**.

```javascript
let score = 85;
if (score >= 50) {
  let message = "Passed";
  console.log(message);
}
```

**Output:** `Passed`.

Accessing `message` outside its block fails:

```javascript
let score = 85;
if (score >= 50) {
  let message = "Passed";
}
console.log(message);
```

**Result:** `ReferenceError: message is not defined`.

**Correct when the value is needed afterward:**

```javascript
let score = 85;
let message;
if (score >= 50) {
  message = "Passed";
} else {
  message = "Failed";
}
console.log(message);
```

**Output:** `Passed`.

---

## 18. Common mistakes beginners make

### Mistake 1: Assignment instead of comparison

```javascript
let age = 20;
if (age = 18) {
  console.log("Age is 18");
}
```

This assigns `18` to `age`, and the condition becomes truthy. Use `age === 18` for comparison.

### Mistake 2: Omitting braces

```javascript
let age = 15;
if (age >= 18)
  console.log("Adult");
  console.log("Check completed");
```

**Output:** `Check completed`. Only the first statement belongs to the `if`. Prefer braces:

```javascript
if (age >= 18) {
  console.log("Adult");
  console.log("Check completed");
}
```

### Mistake 3: Incorrect branch order

```javascript
let score = 95;
if (score >= 50) console.log("Pass");
else if (score >= 90) console.log("Excellent");
```

**Output:** `Pass`. Put `score >= 90` first if it should take priority.

### Mistake 4: Forgetting break

```javascript
let value = 1;
switch (value) {
  case 1: console.log("One");
  case 2: console.log("Two");
}
```

**Output:** `One` and `Two` because of fall-through.

### Mistake 5: Incompatible switch types

```javascript
let day = "2";
switch (day) {
  case 2: console.log("Tuesday"); break;
  default: console.log("No match");
}
```

**Output:** `No match`.

### Mistake 6: Overusing nested ternaries

```javascript
let score = 85;
let result = score >= 90 ? "A" : score >= 80 ? "B" : score >= 70 ? "C" : score >= 60 ? "D" : "F";
```

**Result:** `B`. Prefer `if...else if` when multiple branches need clear explanation.

### Mistake 7: Incorrect logical grouping

```javascript
let age = 16;
let hasTicket = false;
let hasInvitation = true;
if (age >= 18 && hasTicket || hasInvitation) {
  console.log("Allowed");
}
```

**Output:** `Allowed` because the expression groups as `(age >= 18 && hasTicket) || hasInvitation`.

If the age requirement always applies, write `age >= 18 && (hasTicket || hasInvitation)`.

---

## 19. Practical examples

### Example 1: Simple calculator

```javascript
let a = 20;
let b = 5;
let operator = "*";
let result;
switch (operator) {
  case "+": result = a + b; break;
  case "-": result = a - b; break;
  case "*": result = a * b; break;
  case "/": result = b !== 0 ? a / b : "Cannot divide by zero"; break;
  default: result = "Invalid operator";
}
console.log(result);
```

**Output:** `100` because `operator` is `"*"` and `20 * 5 = 100`.

### Example 2: Shipping fee calculation

Illustrative rules: orders at least 1000 ship free; orders at least 500 cost 50; smaller orders cost 100.

```javascript
let orderTotal = 750;
let shippingFee;
if (orderTotal >= 1000) {
  shippingFee = 0;
} else if (orderTotal >= 500) {
  shippingFee = 50;
} else {
  shippingFee = 100;
}
console.log(shippingFee);
```

**Output:** `50`.

### Example 3: Input presence validation

```javascript
let username = "Alice";
let password = "secret123";
if (username === "") {
  console.log("Username required");
} else if (password === "") {
  console.log("Password required");
} else {
  console.log("Input provided");
}
```

**Output:** `Input provided`. This checks for nonempty fields; it is **not** real authentication.

### Example 4: Leap year calculation

A Gregorian calendar year is a leap year if divisible by 400, or divisible by 4 but not by 100.

```javascript
let year = 2024;
let isLeapYear =
  year % 400 === 0 ||
  (year % 4 === 0 && year % 100 !== 0);
if (isLeapYear) {
  console.log("Leap year");
} else {
  console.log("Not a leap year");
}
```

**Output:** `Leap year`.

```text
Divisible by 400
       OR
(Divisible by 4 AND not divisible by 100)
```

---

## 20. Knowledge assessment with answers

Try answering each question before reading its solution.

### Question 1

```javascript
let age = 15;
if (age >= 18) {
  console.log("Adult");
} else {
  console.log("Minor");
}
```

Options: A. Adult; B. Minor; C. Both; D. Nothing.

**Answer: B. Minor.** `15 >= 18` is false, so the `else` branch runs.

### Question 2

```javascript
let score = 95;
if (score >= 50) {
  console.log("Pass");
} else if (score >= 90) {
  console.log("Excellent");
}
```

Options: A. Excellent; B. Pass; C. Both; D. Nothing.

**Answer: B. Pass.** The first condition is true, so later `else if` branches are skipped.

### Question 3

```javascript
let age = 20;
console.log(age >= 18 ? "Adult" : "Minor");
```

Options: A. Minor; B. Adult; C. true; D. undefined.

**Answer: B. Adult.** The ternary selects the truthy branch.

### Question 4

```javascript
let day = "2";
switch (day) {
  case 2: console.log("Tuesday"); break;
  default: console.log("No match");
}
```

Options: A. Tuesday; B. No match; C. 2; D. Error.

**Answer: B. No match.** The string `"2"` does not match the number `2`.

### Question 5

```javascript
let n = 1;
switch (n) {
  case 1: console.log("One");
  case 2: console.log("Two");
}
```

Options: A. One only; B. Two only; C. One then Two; D. Nothing.

**Answer: C. One then Two.** Without `break`, execution falls through.

### Question 6

```javascript
let age = 16;
let invited = true;
console.log(age >= 18 && invited);
```

Options: A. true; B. false; C. undefined; D. Error.

**Answer: B. false.** The age condition is false, so the AND result is false.

### Question 7

```javascript
let score = 75;
let grade = score >= 90 ? "A" : score >= 70 ? "C" : "F";
console.log(grade);
```

Options: A. A; B. C; C. F; D. undefined.

**Answer: B. C.** `75 >= 90` is false, but `75 >= 70` is true.

### Question 8

```javascript
let x = 0;
if (x) {
  console.log("A");
} else {
  console.log("B");
}
```

Options: A. A; B. B; C. Both; D. Neither.

**Answer: B. B.** Zero is falsy.

### Question 9

```javascript
let value = 5;
if (value > 0) {
  let message = "Positive";
}
console.log(message);
```

Options: A. Positive; B. undefined; C. ReferenceError; D. false.

**Answer: C. ReferenceError.** `message` was declared using `let` inside the `if` block and is not accessible outside it.

### Question 10

```javascript
let year = 2000;
let leap = year % 400 === 0 || (year % 4 === 0 && year % 100 !== 0);
console.log(leap);
```

Options: A. true; B. false; C. 2000; D. NaN.

**Answer: A. true.** 2000 is divisible by 400.

---

## 21. Practical coding exercises

Attempt all ten before reading the solutions.

### Exercise 1: Positive, negative, or zero

Given `let number = -15;`, write `if...else if...else` to print `Positive`, `Negative`, or `Zero`. **Expected:** `Negative`.

### Exercise 2: Even or odd

Given `let number = 27;`, use the remainder operator and `if...else` to print `Even` or `Odd`. **Expected:** `Odd`.

### Exercise 3: Grade classification

Given `let score = 88;`, use these illustrative rules: 90-100 A, 80-89 B, 70-79 C, 60-69 D, below 60 F. **Expected:** `B`.

### Exercise 4: Largest of three numbers

Given `let a = 15; let b = 42; let c = 28;`, find the largest using conditional statements. **Expected:** `42`.

### Exercise 5: Ternary operator

Given `let age = 17;`, assign `Adult` or `Minor` using a ternary. **Expected:** `Minor`.

### Exercise 6: Day of the week

Given `let day = 5;`, use `switch` with 1 = Monday through 7 = Sunday. **Expected:** `Friday`.

### Exercise 7: Calculator

Given `let a = 30; let b = 6; let operator = "/";`, use `switch` to support `+`, `-`, `*`, `/`, division by zero, and invalid operators. **Expected:** `5`.

### Exercise 8: Leap year checker

Given `let year = 2024;`, determine whether it is a Gregorian leap year. **Expected:** `Leap year`.

### Exercise 9: Access eligibility

Given `let age = 22; let hasTicket = false; let hasInvitation = true;`, permit entry only if age >= 18 and (ticket or invitation). **Expected:** `Entry allowed`.

### Exercise 10: Shopping discount calculator

Given `let totalAmount = 1500; let isMember = true;`, apply these illustrative rules: total >= 1000 and member: 20%; otherwise total >= 1000: 10%; otherwise member: 5%; otherwise 0%. Print original, discount, and final amount to two decimals.

**Expected output:**

```text
Original amount: 1500.00
Discount: 300.00
Final amount: 1200.00
```

---

## 22. Complete model solutions

### Solution 1: Positive, negative, or zero

```javascript
let number = -15;
if (number > 0) {
  console.log("Positive");
} else if (number < 0) {
  console.log("Negative");
} else {
  console.log("Zero");
}
```

**Output:** `Negative`.

**Explanation:** `-15 > 0` is false; `-15 < 0` is true, so the second branch runs. The fallback is used only for zero (assuming valid numeric input).

### Solution 2: Even or odd

```javascript
let number = 27;
if (number % 2 === 0) {
  console.log("Even");
} else {
  console.log("Odd");
}
```

**Output:** `Odd`.

**Explanation:** `27 % 2` is `1`, not `0`, so the `else` branch runs.

### Solution 3: Grade classification

```javascript
let score = 88;
if (score < 0 || score > 100) {
  console.log("Invalid score");
} else if (score >= 90) {
  console.log("A");
} else if (score >= 80) {
  console.log("B");
} else if (score >= 70) {
  console.log("C");
} else if (score >= 60) {
  console.log("D");
} else {
  console.log("F");
}
```

**Output:** `B`.

**Explanation:** `88` is within the allowed range, below 90, and at least 80. The `B` branch runs. The validation branch also rejects scores outside 0-100; a production version should additionally check `Number.isFinite(score)`.

### Solution 4: Largest of three numbers

```javascript
let a = 15;
let b = 42;
let c = 28;
let largest;
if (a >= b && a >= c) {
  largest = a;
} else if (b >= a && b >= c) {
  largest = b;
} else {
  largest = c;
}
console.log(largest);
```

**Output:** `42`.

**Explanation:** `a` is not greater than or equal to both other values. `b` is greater than or equal to both, so `largest = b`. The `>=` comparisons also handle ties.

### Solution 5: Ternary operator

```javascript
let age = 17;
let status = age >= 18 ? "Adult" : "Minor";
console.log(status);
```

**Output:** `Minor`.

**Explanation:** `17 >= 18` is false, so the expression selects `"Minor"`.

### Solution 6: Day of the week

```javascript
let day = 5;
switch (day) {
  case 1: console.log("Monday"); break;
  case 2: console.log("Tuesday"); break;
  case 3: console.log("Wednesday"); break;
  case 4: console.log("Thursday"); break;
  case 5: console.log("Friday"); break;
  case 6: console.log("Saturday"); break;
  case 7: console.log("Sunday"); break;
  default: console.log("Invalid day");
}
```

**Output:** `Friday`.

**Explanation:** `day` is numeric `5`, so `case 5` matches. `break` prevents fall-through.

### Solution 7: Calculator

```javascript
let a = 30;
let b = 6;
let operator = "/";
let result;
switch (operator) {
  case "+": result = a + b; break;
  case "-": result = a - b; break;
  case "*": result = a * b; break;
  case "/":
    result = b === 0 ? "Cannot divide by zero" : a / b;
    break;
  default:
    result = "Invalid operator";
}
console.log(result);
```

**Output:** `5`.

**Explanation:** The operator matches `/`. Since `b` is not zero, JavaScript calculates `30 / 6 = 5`. If `b` were zero, the message would be selected instead. An unknown operator triggers `default`.

### Solution 8: Leap year checker

```javascript
let year = 2024;
let isLeapYear =
  year % 400 === 0 ||
  (year % 4 === 0 && year % 100 !== 0);
if (isLeapYear) {
  console.log("Leap year");
} else {
  console.log("Not a leap year");
}
```

**Output:** `Leap year`.

**Explanation:** 2024 is divisible by 4 but not by 100, so the second grouped condition is true. Century years such as 1900 are excluded unless divisible by 400, as 2000 is.

### Solution 9: Access eligibility

```javascript
let age = 22;
let hasTicket = false;
let hasInvitation = true;
if (age >= 18 && (hasTicket || hasInvitation)) {
  console.log("Entry allowed");
} else {
  console.log("Entry denied");
}
```

**Output:** `Entry allowed`.

**Explanation:** `age >= 18` is true. `(false || true)` is true. Both sides of the AND are true, so entry is allowed. The parentheses ensure that the age requirement always applies.

### Solution 10: Shopping discount calculator

```javascript
let totalAmount = 1500;
let isMember = true;
let discountRate;

if (totalAmount >= 1000 && isMember) {
  discountRate = 0.20;
} else if (totalAmount >= 1000) {
  discountRate = 0.10;
} else if (isMember) {
  discountRate = 0.05;
} else {
  discountRate = 0;
}

let discount = totalAmount * discountRate;
let finalAmount = totalAmount - discount;

console.log(`Original amount: ${totalAmount.toFixed(2)}`);
console.log(`Discount: ${discount.toFixed(2)}`);
console.log(`Final amount: ${finalAmount.toFixed(2)}`);
```

**Output:**

```text
Original amount: 1500.00
Discount: 300.00
Final amount: 1200.00
```

**Explanation:** `1500 >= 1000` and `isMember` are both true, so the first branch sets `discountRate` to `0.20`. The discount is `1500 * 0.20 = 300`; the final amount is `1500 - 300 = 1200`. `toFixed(2)` formats the values as strings with two decimal places. These are illustrative rules, and floating-point calculations may need special handling for real financial software.

### Exercise review checklist

Before proceeding, confirm that you can:

- [ ] Predict which branch executes.
- [ ] Explain why condition order matters.
- [ ] Use `===` instead of accidental assignment.
- [ ] Group AND/OR expressions with parentheses.
- [ ] Explain fall-through and `break`.
- [ ] Choose between `if...else`, `switch`, and ternary.
- [ ] Explain block scope and early returns.
- [ ] Reproduce all ten solutions without copying.

---

## 23. Lesson summary

| Concept | Key takeaway |
|---|---|
| Control flow | Determines execution order |
| `if` | Runs a block when a condition is truthy |
| `else` | Runs when preceding `if` is falsy |
| `else if` | Tests further conditions in sequence |
| Nested conditions | Places conditions inside other conditions |
| `&&` | Combines required conditions |
| `||` | Combines alternatives |
| `!` | Negates truthiness |
| Ternary | Selects one of two expressions |
| `switch` | Matches an expression against cases |
| `case` | Defines a potential match |
| `break` | Exits a switch or loop |
| `default` | Handles unmatched cases |
| Fall-through | Continues into later case bodies |
| Guard clause | Handles invalid or exceptional conditions early |
| Block scope | Restricts `let` and `const` declarations to their blocks |

**Key principles:** Conditions control execution; branch order matters; parentheses clarify logical grouping; ternaries are best for simple value selection; switches are useful for fixed alternatives; `break` prevents unwanted fall-through; block-scoped variables are not visible outside their block; validate unexpected inputs.

---

## Lesson navigation

**Previous:** [Unit 1 - Lesson 6: Booleans, Truthy and Falsy Values, and Logical Expressions](06-booleans-truthy-falsy-and-logical-expressions.md)  
**Current:** Unit 1 - Lesson 7: JavaScript Conditional Statements and Decision Making  
**Next:** [Unit 1 - Lesson 8: JavaScript Loops and Iteration](08-loops-and-iteration.md)

**Suggested repository path:** `javascript-course/unit-01-fundamentals/07-conditional-statements-and-decision-making.md`
