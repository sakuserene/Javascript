# Topic 05: Complete JavaScript Functions Mastery

> Phase 2 | Levels 1â€“8 | Modern JavaScript | 4-space indentation

**Navigation:** [Previous: Nested Loops](../phase-01-conditions-and-loops/04-complete-nested-for-loops.md) | [Contents](#table-of-contents) | [Next: Arrow Functions](06-complete-arrow-functions.md) *(planned)*

## Table of contents

- [1. Prerequisites and learning outcomes](#1-prerequisites-and-learning-outcomes)
- [2. Complete capability inventory](#2-complete-capability-inventory)
- [3. Progressive learning levels](#3-progressive-learning-levels)
- [4. Function syntax and execution model](#4-function-syntax-and-execution-model)
- [5. Worked capability examples](#5-worked-capability-examples)
- [6. Advanced function behavior](#6-advanced-function-behavior)
- [7. Debugging and testing](#7-debugging-and-testing)
- [8. Independent assessments](#8-independent-assessments)
- [9. Coverage and next topic](#9-coverage-and-next-topic)

## 1. Prerequisites and learning outcomes

Prerequisites: variables, primitive values, comparisons, conditions, loops, blocks, `console.log`, and template literals. Functions introduce **reusable operations** and **controlled data flow**. By completion, you should recognize when a function is appropriate, design a signature, implement it, trace execution, validate inputs, and test boundaries.

**Teaching rule:** Each example begins with a business requirement, then recognition, algorithm, pseudocode, implementation, output, token-level deconstruction, trace, and distinct questions. Examples are educational unless explicitly marked production-oriented.

## 2. Complete capability inventory

| ID | Capability | Classification | Coverage |
|---|---|---|---|
| F01 | Function declarations, invocation, parentheses, blocks | CORE | Sections 4â€“5 |
| F02 | Function expressions, named and anonymous | CORE | Sections 4â€“6 |
| F03 | Parameters, arguments, order, arity | CORE | Sections 4â€“5 |
| F04 | `return`, implicit `undefined`, unreachable code | CORE | Sections 4â€“5 |
| F05 | Default parameters, evaluation timing | CORE | Sections 5â€“6 |
| F06 | Rest parameters, spread arguments | CORE | Sections 5â€“6 |
| F07 | Scope, shadowing, lexical environment | CORE | Sections 5â€“6 |
| F08 | Hoisting, TDZ, declarations versus expressions | CORE | Section 6 |
| F09 | Closures and persistent private state | CORE | Sections 5â€“6 |
| F10 | Recursion, base cases, call stack | CORE | Sections 5â€“6 |
| F11 | Pure functions, side effects, mutation | CORE | Sections 5â€“7 |
| F12 | Error handling, validation, `throw`, `try/catch` | CORE | Sections 5â€“7 |
| F13 | Functions as values and higher-order functions | CORE | Sections 5â€“6 |
| F14 | Callbacks and callback contracts | RELATED | Section 6; Topic 09 |
| F15 | Arrow syntax, lexical `this`, expression bodies | RELATED | Section 6; Topic 06 |
| F16 | `this`, method calls, `call`, `apply`, `bind` | CORE | Section 6 |
| F17 | `arguments`, rest differences | CORE | Section 6 |
| F18 | Constructor functions, `new`, `new.target`, prototype | RELATED | Section 6; Objects topic |
| F19 | Function object `name`, `length`, `prototype`, `toString()` | CORE | Section 6 |
| F20 | Async functions and promise return | ADVANCED | Topic 19 |
| F21 | Generator functions, `yield`, `yield*` | ADVANCED | Iteration protocols module |
| F22 | Async generator functions, `for await...of` | ADVANCED | Async module |
| F23 | IIFE and module encapsulation | RELATED | Section 6; Modules |
| F24 | Strict mode and function restrictions | CORE | Section 6 |
| F25 | Destructured parameters | RELATED | Arrays and Objects topics |
| F26 | Tail calls, recursion limits, optimization | ADVANCED | Algorithms module |
| F27 | `Function` constructor and security implications | RELATED | Section 6 |
| F28 | Function identity, equality, references, garbage collection | CORE | Section 6 |
| F29 | Testing, assertions, tracing, complexity | CORE | Sections 7â€“8 |
| F30 | Browser/runtime differences and legacy features | RELATED | Section 6 |

**Status legend:** Listed = inventory created; Explained = conceptual treatment; Practiced = worked examples; Assessed = independent question; Mastered = requires your demonstrated work. No capability is automatically marked mastered.

## 3. Progressive learning levels

| Level | Distinct capability | Evidence of mastery |
|---|---|---|
| 1 | Recognize and call a function | Explain call versus definition |
| 2 | Parameters and return values | Predict inputs and outputs |
| 3 | Combine conditions and loops | Implement reusable business rules |
| 4 | Defaults, rest, validation | Handle variable and invalid input |
| 5 | Scope, closures, recursion | Trace lifetime and call stack |
| 6 | Function design and composition | Decompose unfamiliar requirements |
| 7 | Debugging, testing, performance | Diagnose and verify implementations |
| 8 | Independent transfer | Build and defend a solution without hints |

## 4. Function syntax and execution model

```javascript
function calculateTotal(price, quantity) {
    const total = price * quantity;
    return total;
}

const result = calculateTotal(250, 3);
console.log(result);
```

**Expected console output**

```text
750
```

**Definition versus invocation:** Defining the function makes it available; calling `calculateTotal(250, 3)` executes its body. The argument `250` binds to parameter `price`; `3` binds to `quantity`. The multiplication produces `750`; `return` ends the call and supplies the result to the caller. The `const result` binding receives `750`, and `console.log` displays it.

| Token / expression | Exact role |
|---|---|
| `function` | Function declaration keyword |
| `calculateTotal` | Identifier used to reference the function |
| `(price, quantity)` | Ordered parameter list; local bindings created per call |
| `{ ... }` | Function body |
| `const` | Block-scoped declaration; reassignment prohibited |
| `price * quantity` | Multiplication expression; reads both parameters |
| `return total` | Exits the function and supplies the current value |
| `calculateTotal(250, 3)` | Call expression with two arguments |
| `console.log(result)` | Logging side effect; not a return from `calculateTotal` |

**Trace:** call â†’ bind `price=250` â†’ bind `quantity=3` â†’ compute `total=750` â†’ return `750` â†’ assign `result=750` â†’ log `750`.

**Common confusion:** `console.log(total)` displays a value but does not make it the function's returned value. A function with no explicit `return` normally returns `undefined`.

[â†‘ Contents](#table-of-contents) | [Next: Worked examples](#5-worked-capability-examples)

## 5. Worked capability examples

### Example 1: Named declaration and invocation (L1)

**Business question:** A warehouse clerk must print a fixed dispatch heading whenever a new report starts.

**Inputs, rules, and output:** No inputs; print the heading once per call.

**Capability recognition:** A named function packages a repeatable action.

**Algorithm logic:** Define a named function; call it twice.

**Pseudocode:**

```text
READ required inputs
APPLY stated validation or rule
EXECUTE operation
RETURN or DISPLAY result
```

**Complete runnable code:**

```javascript
function printHeading() {
    console.log("DAILY DISPATCH REPORT");
}

printHeading();
printHeading();
```

**Expected console output:**

```text
DAILY DISPATCH REPORT
DAILY DISPATCH REPORT
```

**Token and expression deconstruction:**

| Code component | Logical responsibility |
|---|---|

| `function` | Introduces a function declaration. |

| `printHeading` | Stores a callable identifier. |

| `()` | Declares zero parameters; later invokes with zero arguments. |

| `console.log` | Produces visible output, a side effect. |

| `printHeading()` | Executes the body again on each call. |

**Line-by-line execution logic:**

- **Line 1:** `function printHeading() {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 2:** `console.log("DAILY DISPATCH REPORT");` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 3:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 5:** `printHeading();` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 6:** `printHeading();` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

**Trace and verification:** Expected output is shown above. Trace the parameter values, branch decisions, and return or logging behavior. Then rerun with boundary inputs and compare against the business rule.

**Six distinct capability questions (solutions intentionally withheld):**

1. **Recognition:** Which part of this business requirement makes a function preferable to duplicating code?
2. **Syntax:** Identify the function's parameters, its arguments at each call, and its return behavior.
3. **Tracing:** Predict the exact output and the order of executed statements without running it.
4. **Modification:** Change one business rule and describe which line must change and why.
5. **Debugging:** What happens if you remove `return`, alter a condition, or pass an unexpected type?
6. **Transfer:** Invent a different business scenario using the same capability and design its signature.

[â†‘ Contents](#table-of-contents)

### Example 2: Arguments, parameters, and return (L2)

**Business question:** An online shop must compute the value of a purchase from unit price and quantity.

**Inputs, rules, and output:** Price and quantity are nonnegative numbers; return the total, do not print inside the function.

**Capability recognition:** A parameterized function transforms supplied values into a reusable result.

**Algorithm logic:** Multiply the inputs and return the product.

**Pseudocode:**

```text
READ required inputs
APPLY stated validation or rule
EXECUTE operation
RETURN or DISPLAY result
```

**Complete runnable code:**

```javascript
function orderValue(unitPrice, quantity) {
    return unitPrice * quantity;
}

const first = orderValue(120, 4);
const second = orderValue(75, 2);
console.log(first);
console.log(second);
```

**Expected console output:**

```text
480
150
```

**Token and expression deconstruction:**

| Code component | Logical responsibility |
|---|---|

| `unitPrice` | Local parameter bound to the first argument. |

| `quantity` | Local parameter bound to the second argument. |

| `*` | Multiplies two numeric operands. |

| `return` | Sends the calculated value back to the caller. |

| `first` / `second` | Independent results of separate calls. |

**Line-by-line execution logic:**

- **Line 1:** `function orderValue(unitPrice, quantity) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 2:** `return unitPrice * quantity;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 3:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 5:** `const first = orderValue(120, 4);` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 6:** `const second = orderValue(75, 2);` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 7:** `console.log(first);` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 8:** `console.log(second);` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

**Trace and verification:** Expected output is shown above. Trace the parameter values, branch decisions, and return or logging behavior. Then rerun with boundary inputs and compare against the business rule.

**Six distinct capability questions (solutions intentionally withheld):**

1. **Recognition:** Which part of this business requirement makes a function preferable to duplicating code?
2. **Syntax:** Identify the function's parameters, its arguments at each call, and its return behavior.
3. **Tracing:** Predict the exact output and the order of executed statements without running it.
4. **Modification:** Change one business rule and describe which line must change and why.
5. **Debugging:** What happens if you remove `return`, alter a condition, or pass an unexpected type?
6. **Transfer:** Invent a different business scenario using the same capability and design its signature.

[â†‘ Contents](#table-of-contents)

### Example 3: Conditional business rule (L3)

**Business question:** A payroll system must apply a 10% allowance only when an employeeâ€™s basic pay is at least 30,000.

**Inputs, rules, and output:** Input is nonnegative pay; return final pay including allowance.

**Capability recognition:** A condition inside a function selects the appropriate calculation.

**Algorithm logic:** Check threshold; choose allowance; return total.

**Pseudocode:**

```text
READ required inputs
APPLY stated validation or rule
EXECUTE operation
RETURN or DISPLAY result
```

**Complete runnable code:**

```javascript
function payWithAllowance(basicPay) {
    if (basicPay >= 30000) {
        return basicPay * 1.10;
    }

    return basicPay;
}

console.log(payWithAllowance(32000));
console.log(payWithAllowance(25000));
```

**Expected console output:**

```text
35200
25000
```

**Token and expression deconstruction:**

| Code component | Logical responsibility |
|---|---|

| `basicPay >= 30000` | Boolean comparison that controls the branch. |

| `if` | Runs its body only when condition is truthy. |

| `return basicPay * 1.10` | Calculates and exits early for qualifying pay. |

| `return basicPay` | Fallback when the condition is false. |

**Line-by-line execution logic:**

- **Line 1:** `function payWithAllowance(basicPay) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 2:** `if (basicPay >= 30000) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 3:** `return basicPay * 1.10;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 4:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 6:** `return basicPay;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 7:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 9:** `console.log(payWithAllowance(32000));` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 10:** `console.log(payWithAllowance(25000));` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

**Trace and verification:** Expected output is shown above. Trace the parameter values, branch decisions, and return or logging behavior. Then rerun with boundary inputs and compare against the business rule.

**Six distinct capability questions (solutions intentionally withheld):**

1. **Recognition:** Which part of this business requirement makes a function preferable to duplicating code?
2. **Syntax:** Identify the function's parameters, its arguments at each call, and its return behavior.
3. **Tracing:** Predict the exact output and the order of executed statements without running it.
4. **Modification:** Change one business rule and describe which line must change and why.
5. **Debugging:** What happens if you remove `return`, alter a condition, or pass an unexpected type?
6. **Transfer:** Invent a different business scenario using the same capability and design its signature.

[â†‘ Contents](#table-of-contents)

### Example 4: Loop and accumulator inside a function (L3)

**Business question:** A school needs the total number of seats across several identical rows.

**Inputs, rules, and output:** Input row count and seats per row, both nonnegative integers.

**Capability recognition:** A loop repeats accumulation; a function makes the operation reusable.

**Algorithm logic:** Initialize total to zero; add row capacity once per row; return total.

**Pseudocode:**

```text
READ required inputs
APPLY stated validation or rule
EXECUTE operation
RETURN or DISPLAY result
```

**Complete runnable code:**

```javascript
function countSeats(rows, seatsPerRow) {
    let total = 0;

    for (let row = 1; row <= rows; row++) {
        total += seatsPerRow;
    }

    return total;
}

console.log(countSeats(3, 8));
```

**Expected console output:**

```text
24
```

**Token and expression deconstruction:**

| Code component | Logical responsibility |
|---|---|

| `let total = 0` | Creates mutable accumulator initialized before the loop. |

| `row <= rows` | Repeats for every row. |

| `total += seatsPerRow` | Adds each row capacity to the running total. |

| `return total` | Supplies the final accumulated result. |

**Line-by-line execution logic:**

- **Line 1:** `function countSeats(rows, seatsPerRow) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 2:** `let total = 0;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 4:** `for (let row = 1; row <= rows; row++) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 5:** `total += seatsPerRow;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 6:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 8:** `return total;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 9:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 11:** `console.log(countSeats(3, 8));` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

**Trace and verification:** Expected output is shown above. Trace the parameter values, branch decisions, and return or logging behavior. Then rerun with boundary inputs and compare against the business rule.

**Six distinct capability questions (solutions intentionally withheld):**

1. **Recognition:** Which part of this business requirement makes a function preferable to duplicating code?
2. **Syntax:** Identify the function's parameters, its arguments at each call, and its return behavior.
3. **Tracing:** Predict the exact output and the order of executed statements without running it.
4. **Modification:** Change one business rule and describe which line must change and why.
5. **Debugging:** What happens if you remove `return`, alter a condition, or pass an unexpected type?
6. **Transfer:** Invent a different business scenario using the same capability and design its signature.

[â†‘ Contents](#table-of-contents)

### Example 5: Default parameters (L4)

**Business question:** A hotel offers standard check-in at 14:00 unless a different hour is supplied.

**Inputs, rules, and output:** Accept a room identifier and optional hour; use 14 when the hour argument is missing or undefined.

**Capability recognition:** Default parameters express an optional input.

**Algorithm logic:** Bind the default if needed; build a message; return it.

**Pseudocode:**

```text
READ required inputs
APPLY stated validation or rule
EXECUTE operation
RETURN or DISPLAY result
```

**Complete runnable code:**

```javascript
function checkInMessage(room, hour = 14) {
    return `Room ${room}: check-in at ${hour}:00`;
}

console.log(checkInMessage(12));
console.log(checkInMessage(15, 16));
```

**Expected console output:**

```text
Room 12: check-in at 14:00
Room 15: check-in at 16:00
```

**Token and expression deconstruction:**

| Code component | Logical responsibility |
|---|---|

| `hour = 14` | Default initializer evaluated when argument is missing or undefined. |

| `room` | Required business input, although JavaScript does not enforce it automatically. |

| `` `Room ${room}...` `` | Template literal interpolates both parameters. |

| `checkInMessage(12)` | Uses default hour. |

| `checkInMessage(15, 16)` | Overrides default with 16. |

**Line-by-line execution logic:**

- **Line 1:** `function checkInMessage(room, hour = 14) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 2:** `return `Room ${room}: check-in at ${hour}:00`;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 3:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 5:** `console.log(checkInMessage(12));` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 6:** `console.log(checkInMessage(15, 16));` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

**Trace and verification:** Expected output is shown above. Trace the parameter values, branch decisions, and return or logging behavior. Then rerun with boundary inputs and compare against the business rule.

**Six distinct capability questions (solutions intentionally withheld):**

1. **Recognition:** Which part of this business requirement makes a function preferable to duplicating code?
2. **Syntax:** Identify the function's parameters, its arguments at each call, and its return behavior.
3. **Tracing:** Predict the exact output and the order of executed statements without running it.
4. **Modification:** Change one business rule and describe which line must change and why.
5. **Debugging:** What happens if you remove `return`, alter a condition, or pass an unexpected type?
6. **Transfer:** Invent a different business scenario using the same capability and design its signature.

[â†‘ Contents](#table-of-contents)

### Example 6: Rest parameters (L4)

**Business question:** A supermarket must total a variable number of item prices.

**Inputs, rules, and output:** Accept any number of numeric prices; return their sum.

**Capability recognition:** Rest parameters gather trailing arguments into a real array.

**Algorithm logic:** Start at zero; visit every price; accumulate; return.

**Pseudocode:**

```text
READ required inputs
APPLY stated validation or rule
EXECUTE operation
RETURN or DISPLAY result
```

**Complete runnable code:**

```javascript
function sumPrices(...prices) {
    let total = 0;

    for (const price of prices) {
        total += price;
    }

    return total;
}

console.log(sumPrices(20, 30, 50));
console.log(sumPrices());
```

**Expected console output:**

```text
100
0
```

**Token and expression deconstruction:**

| Code component | Logical responsibility |
|---|---|

| `...prices` | Rest parameter collects all supplied arguments into an array. |

| `for...of` | Visits each array element in sequence. |

| `const price` | New binding for each visited element. |

| `total += price` | Accumulates numeric values. |

| `sumPrices()` | Empty rest array means no iterations; result remains zero. |

**Line-by-line execution logic:**

- **Line 1:** `function sumPrices(...prices) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 2:** `let total = 0;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 4:** `for (const price of prices) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 5:** `total += price;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 6:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 8:** `return total;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 9:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 11:** `console.log(sumPrices(20, 30, 50));` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 12:** `console.log(sumPrices());` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

**Trace and verification:** Expected output is shown above. Trace the parameter values, branch decisions, and return or logging behavior. Then rerun with boundary inputs and compare against the business rule.

**Six distinct capability questions (solutions intentionally withheld):**

1. **Recognition:** Which part of this business requirement makes a function preferable to duplicating code?
2. **Syntax:** Identify the function's parameters, its arguments at each call, and its return behavior.
3. **Tracing:** Predict the exact output and the order of executed statements without running it.
4. **Modification:** Change one business rule and describe which line must change and why.
5. **Debugging:** What happens if you remove `return`, alter a condition, or pass an unexpected type?
6. **Transfer:** Invent a different business scenario using the same capability and design its signature.

[â†‘ Contents](#table-of-contents)

### Example 7: Lexical scope and shadowing (L5)

**Business question:** An accounts report uses a company-wide tax rate but a specific calculation needs a local override.

**Inputs, rules, and output:** Global rate 0.16; local calculation rate 0.08.

**Capability recognition:** A local binding can shadow an outer binding without changing it.

**Algorithm logic:** Declare outer value; declare local value in function; return local calculation.

**Pseudocode:**

```text
READ required inputs
APPLY stated validation or rule
EXECUTE operation
RETURN or DISPLAY result
```

**Complete runnable code:**

```javascript
const taxRate = 0.16;

function localTax(amount) {
    const taxRate = 0.08;
    return amount * taxRate;
}

console.log(localTax(1000));
console.log(taxRate);
```

**Expected console output:**

```text
80
0.16
```

**Token and expression deconstruction:**

| Code component | Logical responsibility |
|---|---|

| `const taxRate = 0.16` | Outer binding available outside function. |

| `const taxRate = 0.08` | New local binding shadows the outer name. |

| `amount * taxRate` | Resolves nearest lexical binding (0.08). |

| `console.log(taxRate)` | Reads outer 0.16 after function returns. |

**Line-by-line execution logic:**

- **Line 1:** `const taxRate = 0.16;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 3:** `function localTax(amount) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 4:** `const taxRate = 0.08;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 5:** `return amount * taxRate;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 6:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 8:** `console.log(localTax(1000));` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 9:** `console.log(taxRate);` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

**Trace and verification:** Expected output is shown above. Trace the parameter values, branch decisions, and return or logging behavior. Then rerun with boundary inputs and compare against the business rule.

**Six distinct capability questions (solutions intentionally withheld):**

1. **Recognition:** Which part of this business requirement makes a function preferable to duplicating code?
2. **Syntax:** Identify the function's parameters, its arguments at each call, and its return behavior.
3. **Tracing:** Predict the exact output and the order of executed statements without running it.
4. **Modification:** Change one business rule and describe which line must change and why.
5. **Debugging:** What happens if you remove `return`, alter a condition, or pass an unexpected type?
6. **Transfer:** Invent a different business scenario using the same capability and design its signature.

[â†‘ Contents](#table-of-contents)

### Example 8: Closures and private state (L5)

**Business question:** A ticket counter must issue increasing ticket numbers without allowing callers to modify the internal count directly.

**Inputs, rules, and output:** Start from 0; each call returns the next integer.

**Capability recognition:** A returned inner function retains access to its enclosing variable.

**Algorithm logic:** Create counter; return a function that increments and returns it.

**Pseudocode:**

```text
READ required inputs
APPLY stated validation or rule
EXECUTE operation
RETURN or DISPLAY result
```

**Complete runnable code:**

```javascript
function createTicketCounter() {
    let current = 0;

    return function nextTicket() {
        current++;
        return current;
    };
}

const next = createTicketCounter();
console.log(next());
console.log(next());
```

**Expected console output:**

```text
1
2
```

**Token and expression deconstruction:**

| Code component | Logical responsibility |
|---|---|

| `current` | Variable captured in closure. |

| `return function nextTicket()` | Returns a function value, not a number. |

| `current++` | Mutates captured state each time inner function is called. |

| `const next = ...` | Stores returned function reference. |

| `next()` | Calls same closure twice; state persists. |

**Line-by-line execution logic:**

- **Line 1:** `function createTicketCounter() {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 2:** `let current = 0;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 4:** `return function nextTicket() {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 5:** `current++;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 6:** `return current;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 7:** `};` â€” Open or close a block; control returns to the containing statement.

- **Line 8:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 10:** `const next = createTicketCounter();` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 11:** `console.log(next());` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 12:** `console.log(next());` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

**Trace and verification:** Expected output is shown above. Trace the parameter values, branch decisions, and return or logging behavior. Then rerun with boundary inputs and compare against the business rule.

**Six distinct capability questions (solutions intentionally withheld):**

1. **Recognition:** Which part of this business requirement makes a function preferable to duplicating code?
2. **Syntax:** Identify the function's parameters, its arguments at each call, and its return behavior.
3. **Tracing:** Predict the exact output and the order of executed statements without running it.
4. **Modification:** Change one business rule and describe which line must change and why.
5. **Debugging:** What happens if you remove `return`, alter a condition, or pass an unexpected type?
6. **Transfer:** Invent a different business scenario using the same capability and design its signature.

[â†‘ Contents](#table-of-contents)

### Example 9: Recursion and base cases (L5)

**Business question:** A finance report needs the factorial of a small nonnegative integer for a combinatorial calculation.

**Inputs, rules, and output:** Accept a nonnegative integer; 0! is 1.

**Capability recognition:** Recursion solves a problem by calling the same function on a smaller input.

**Algorithm logic:** Stop at 0; otherwise multiply n by factorial(n-1).

**Pseudocode:**

```text
READ required inputs
APPLY stated validation or rule
EXECUTE operation
RETURN or DISPLAY result
```

**Complete runnable code:**

```javascript
function factorial(n) {
    if (n === 0) {
        return 1;
    }

    return n * factorial(n - 1);
}

console.log(factorial(4));
```

**Expected console output:**

```text
24
```

**Token and expression deconstruction:**

| Code component | Logical responsibility |
|---|---|

| `n === 0` | Base case prevents unbounded recursion for valid inputs. |

| `return 1` | Returns factorial of zero. |

| `factorial(n - 1)` | Recursive call with smaller argument. |

| `n * ...` | Multiplies result while calls unwind. |

| `factorial(4)` | Produces 4 Ã— 3 Ã— 2 Ã— 1 = 24. |

**Line-by-line execution logic:**

- **Line 1:** `function factorial(n) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 2:** `if (n === 0) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 3:** `return 1;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 4:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 6:** `return n * factorial(n - 1);` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 7:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 9:** `console.log(factorial(4));` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

**Trace and verification:** Expected output is shown above. Trace the parameter values, branch decisions, and return or logging behavior. Then rerun with boundary inputs and compare against the business rule.

**Six distinct capability questions (solutions intentionally withheld):**

1. **Recognition:** Which part of this business requirement makes a function preferable to duplicating code?
2. **Syntax:** Identify the function's parameters, its arguments at each call, and its return behavior.
3. **Tracing:** Predict the exact output and the order of executed statements without running it.
4. **Modification:** Change one business rule and describe which line must change and why.
5. **Debugging:** What happens if you remove `return`, alter a condition, or pass an unexpected type?
6. **Transfer:** Invent a different business scenario using the same capability and design its signature.

[â†‘ Contents](#table-of-contents)

### Example 10: Function expression and function values (L6)

**Business question:** A payment report selects a reusable fee calculation and assigns it to a variable.

**Inputs, rules, and output:** Flat fee of 25 per transaction.

**Capability recognition:** A function expression creates a function value assigned to a binding.

**Algorithm logic:** Assign expression; call binding; print result.

**Pseudocode:**

```text
READ required inputs
APPLY stated validation or rule
EXECUTE operation
RETURN or DISPLAY result
```

**Complete runnable code:**

```javascript
const calculateFee = function (transactions) {
    return transactions * 25;
};

console.log(calculateFee(3));
```

**Expected console output:**

```text
75
```

**Token and expression deconstruction:**

| Code component | Logical responsibility |
|---|---|

| `const calculateFee` | Binding for a function value. |

| `function (transactions)` | Anonymous function expression with one parameter. |

| `};` | Ends assignment statement after function body. |

| `calculateFee(3)` | Invokes stored function with argument 3. |

**Line-by-line execution logic:**

- **Line 1:** `const calculateFee = function (transactions) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 2:** `return transactions * 25;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 3:** `};` â€” Open or close a block; control returns to the containing statement.

- **Line 5:** `console.log(calculateFee(3));` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

**Trace and verification:** Expected output is shown above. Trace the parameter values, branch decisions, and return or logging behavior. Then rerun with boundary inputs and compare against the business rule.

**Six distinct capability questions (solutions intentionally withheld):**

1. **Recognition:** Which part of this business requirement makes a function preferable to duplicating code?
2. **Syntax:** Identify the function's parameters, its arguments at each call, and its return behavior.
3. **Tracing:** Predict the exact output and the order of executed statements without running it.
4. **Modification:** Change one business rule and describe which line must change and why.
5. **Debugging:** What happens if you remove `return`, alter a condition, or pass an unexpected type?
6. **Transfer:** Invent a different business scenario using the same capability and design its signature.

[â†‘ Contents](#table-of-contents)

### Example 11: Functions as callbacks (L6)

**Business question:** A notification system must run a supplied action after validating an order ID.

**Inputs, rules, and output:** Positive integer order ID; action receives that ID.

**Capability recognition:** A function can be passed as an argument and called later.

**Algorithm logic:** Validate ID; call action; return no business value.

**Pseudocode:**

```text
READ required inputs
APPLY stated validation or rule
EXECUTE operation
RETURN or DISPLAY result
```

**Complete runnable code:**

```javascript
function processOrder(orderId, action) {
    if (!Number.isInteger(orderId) || orderId <= 0) {
        throw new TypeError("Invalid order ID");
    }

    action(orderId);
}

function notify(id) {
    console.log(`Order ${id} processed`);
}

processOrder(42, notify);
```

**Expected console output:**

```text
Order 42 processed
```

**Token and expression deconstruction:**

| Code component | Logical responsibility |
|---|---|

| `action` | Parameter receiving a callable value. |

| `Number.isInteger(orderId)` | Checks integer type and value. |

| `throw new TypeError(...)` | Aborts invalid execution with an exception. |

| `action(orderId)` | Calls supplied callback with current ID. |

| `notify` | Passed as a function reference, not invoked immediately. |

**Line-by-line execution logic:**

- **Line 1:** `function processOrder(orderId, action) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 2:** `if (!Number.isInteger(orderId) || orderId <= 0) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 3:** `throw new TypeError("Invalid order ID");` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 4:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 6:** `action(orderId);` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 7:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 9:** `function notify(id) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 10:** `console.log(`Order ${id} processed`);` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 11:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 13:** `processOrder(42, notify);` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

**Trace and verification:** Expected output is shown above. Trace the parameter values, branch decisions, and return or logging behavior. Then rerun with boundary inputs and compare against the business rule.

**Six distinct capability questions (solutions intentionally withheld):**

1. **Recognition:** Which part of this business requirement makes a function preferable to duplicating code?
2. **Syntax:** Identify the function's parameters, its arguments at each call, and its return behavior.
3. **Tracing:** Predict the exact output and the order of executed statements without running it.
4. **Modification:** Change one business rule and describe which line must change and why.
5. **Debugging:** What happens if you remove `return`, alter a condition, or pass an unexpected type?
6. **Transfer:** Invent a different business scenario using the same capability and design its signature.

[â†‘ Contents](#table-of-contents)

### Example 12: Input validation and exceptions (L7)

**Business question:** A bank calculator must reject negative deposits and non-finite numeric amounts.

**Inputs, rules, and output:** Input must be a finite number at least zero.

**Capability recognition:** Validation guards business invariants before calculation.

**Algorithm logic:** Reject invalid input; otherwise return updated balance.

**Pseudocode:**

```text
READ required inputs
APPLY stated validation or rule
EXECUTE operation
RETURN or DISPLAY result
```

**Complete runnable code:**

```javascript
function deposit(balance, amount) {
    if (!Number.isFinite(balance) || !Number.isFinite(amount)) {
        throw new TypeError("Numbers required");
    }

    if (balance < 0 || amount < 0) {
        throw new RangeError("Negative values not allowed");
    }

    return balance + amount;
}

console.log(deposit(1000, 250));
```

**Expected console output:**

```text
1250
```

**Token and expression deconstruction:**

| Code component | Logical responsibility |
|---|---|

| `Number.isFinite` | Rejects NaN, Infinity, strings, and other non-number values. |

| `||` | Logical OR; either invalid input triggers the error. |

| `TypeError` | Signals an unacceptable type or non-finite numeric input. |

| `RangeError` | Signals a number outside the permitted range. |

| `return balance + amount` | Runs only after validation succeeds. |

**Line-by-line execution logic:**

- **Line 1:** `function deposit(balance, amount) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 2:** `if (!Number.isFinite(balance) || !Number.isFinite(amount)) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 3:** `throw new TypeError("Numbers required");` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 4:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 6:** `if (balance < 0 || amount < 0) {` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 7:** `throw new RangeError("Negative values not allowed");` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 8:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 10:** `return balance + amount;` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

- **Line 11:** `}` â€” Open or close a block; control returns to the containing statement.

- **Line 13:** `console.log(deposit(1000, 250));` â€” Evaluate or execute this statement in the order dictated by the enclosing control flow.

**Trace and verification:** Expected output is shown above. Trace the parameter values, branch decisions, and return or logging behavior. Then rerun with boundary inputs and compare against the business rule.

**Six distinct capability questions (solutions intentionally withheld):**

1. **Recognition:** Which part of this business requirement makes a function preferable to duplicating code?
2. **Syntax:** Identify the function's parameters, its arguments at each call, and its return behavior.
3. **Tracing:** Predict the exact output and the order of executed statements without running it.
4. **Modification:** Change one business rule and describe which line must change and why.
5. **Debugging:** What happens if you remove `return`, alter a condition, or pass an unexpected type?
6. **Transfer:** Invent a different business scenario using the same capability and design its signature.

[â†‘ Contents](#table-of-contents)

## 6. Advanced function behavior

### 6.1 Hoisting and temporal dead zone

Function declarations are initialized during environment setup, so a declaration can generally be called before its textual position within its scope. A function expression stored in `const` cannot be read before its declaration initializes: doing so causes a `ReferenceError` due to the temporal dead zone.

```javascript
console.log(declared(2));

function declared(x) {
    return x + 1;
}
```

**Output:** `3`. Contrast:

```javascript
// Calling expression(2) here would throw ReferenceError.
const expression = function (x) {
    return x + 1;
};
```

### 6.2 Return and completion

`return;` produces `undefined`; falling off the end of an ordinary function also returns `undefined`. `return` exits the current function, including from inside a loop. A `finally` block can override an earlier return or throw; avoid returning from `finally` because it obscures control flow.

### 6.3 Parameters: defaults, rest, and destructuring

A default applies when the argument is omitted or explicitly `undefined`, not for `null`, `0`, or `false`. Defaults evaluate left-to-right at call time and may refer to earlier parameters. A rest parameter must be last and only one is allowed. Destructuring parameters unpack object or array structure; full destructuring coverage belongs to the Arrays and Objects topics.

```javascript
function priceAfterTax(price, rate = 0.16) {
    return price * (1 + rate);
}

console.log(priceAfterTax(100));
console.log(priceAfterTax(100, 0));
```

**Output:**

```text
115.99999999999999
100
```

Floating-point results are not exact decimal money representations. For real financial software, use integer minor units or a suitable decimal strategy, with explicit rounding rules.

### 6.4 `arguments` and `length`

Ordinary functions have an array-like `arguments` object. Unlike rest parameters, it is not a real array; prefer rest when collecting variable arguments. Arrow functions do not create their own `arguments`. `fn.length` counts formal parameters before the first parameter with a default, excluding rest parameters; it does not report actual arguments passed.

```javascript
function example(a, b = 2, ...rest) {
    return arguments.length;
}

console.log(example.length);
console.log(example(1, 2, 3));
```

**Output:**

```text
1
3
```

### 6.5 `this` and invocation forms

For ordinary functions, `this` depends on *how the function is called*, not where it is written. `obj.method()` generally binds `this` to `obj`; extracting the method and calling it as a plain function loses that receiver. Strict-mode plain calls use `undefined` for `this`. Arrow functions capture `this` lexically.

```javascript
"use strict";

const account = {
    balance: 200,
    readBalance: function () {
        return this.balance;
    }
};

console.log(account.readBalance());
console.log(account.readBalance.call({ balance: 50 }));
console.log(account.readBalance.apply({ balance: 75 }, []));
const bound = account.readBalance.bind({ balance: 90 });
console.log(bound());
```

**Output:**

```text
200
50
75
90
```

`call(thisArg, ...args)` invokes immediately; `apply(thisArg, argsArray)` invokes immediately; `bind(thisArg, ...presetArgs)` creates a new bound function. A constructor call with `new` has special receiver behavior.

### 6.6 Function identity, names, properties, and references

Every function creation yields a function object. Two separately created functions are not equal by identity, even if their bodies match. Functions have `name`, `length`, and methods inherited from `Function.prototype`, including `call`, `apply`, `bind`, and `toString`. Ordinary constructible functions generally have an own `prototype` property; arrow functions do not. `toString()` can expose source text, but must not be used as a security boundary.

### 6.7 Constructor functions and `new.target`

`new Foo()` creates an object, connects its prototype, calls the constructor with the new receiver, and ordinarily returns that object. `new.target` reveals whether a constructible function was called with `new`. Prefer classes for modern object construction when appropriate; detailed prototypes belong to the Objects topic.

### 6.8 Strict mode, legacy syntax, and runtime considerations

`"use strict"` changes several rules, including plain-call `this` and silent assignment failures. In strict mode, duplicate simple parameter names are disallowed. Legacy non-strict function behaviors vary by context and should not be used for new code. Block-level function declarations have special historical compatibility behavior outside strict mode; prefer unambiguous declarations or expressions. The nonstandard `caller`/`callee` patterns are restricted or deprecated and should be avoided.

### 6.9 IIFE, closures, and modules

An immediately invoked function expression (IIFE) executes as soon as it is created and can isolate variables. ES modules are generally preferable for file-level encapsulation today.

```javascript
(function () {
    const secret = "internal";
    console.log("Initialized");
})();
```

**Output:** `Initialized`. `secret` is not accessible outside that function scope.

### 6.10 Arrow, async, generators, and specialized syntax

- **Arrow functions:** `x => x * 2`, lexical `this`, no own `arguments`, not constructors. Full mastery: Topic 06.
- **Async functions:** `async function f() { ... }` always return a promise (possibly fulfilled with an ordinary returned value). Full mastery: Phase 4.
- **Generator functions:** `function* f() { yield 1; }` return generator objects; execution pauses at `yield`. Full mastery: Iteration protocols.
- **Async generators:** `async function* f() { ... }` combine asynchronous iteration and yielding. Full mastery: Phase 4.
- **`Function` constructor:** dynamically compiles strings, is hard to secure, and does not close over the local lexical scope; avoid when ordinary functions suffice.
- **Recursion limits:** deep recursion can cause `RangeError` due to call-stack exhaustion. Proper tail-call optimization is not portable across mainstream engines; do not rely on it.

### 6.11 Performance and memory

A call adds overhead, and recursive calls consume stack space. Closures can keep referenced data alive while reachable. Choose readable abstractions first; measure before optimizing. Function identity matters when registering and removing event listeners: a new function with identical source is not the same callback object.

[â†‘ Contents](#table-of-contents) | [Next: Debugging and testing](#7-debugging-and-testing)

## 7. Debugging and testing

### 7.1 Error categories

| Error | Symptom | Diagnosis |
|---|---|---|
| SyntaxError | Code fails to parse | Check parentheses, braces, parameter syntax |
| ReferenceError | Missing binding or TDZ | Check declaration order and scope |
| TypeError | Non-callable value invoked | Verify value is a function |
| RangeError | Recursion exceeds stack | Check base case and progress |
| Logical error | Runs but wrong answer | Trace arguments, branches, return |
| Mutation error | Unexpected shared state | Inspect objects passed by reference |

### 7.2 Testing with assertions

```javascript
function square(x) {
    return x * x;
}

console.assert(square(3) === 9, "positive test failed");
console.assert(square(0) === 0, "zero test failed");
console.assert(square(-2) === 4, "negative test failed");
console.log("Assertions evaluated");
```

**Expected output when assertions pass:** `Assertions evaluated`.

`console.assert` logs on failure in typical browser/Node consoles; it is not a substitute for a dedicated automated test runner. A robust test suite should include normal, zero, negative, boundary, invalid-type, missing-argument, and large-input cases where applicable.

### 7.3 Debugging workflow

1. Reproduce with the smallest failing input.
2. State expected versus actual output.
3. Inspect parameter bindings and types.
4. Trace branch and loop decisions.
5. Locate the first incorrect state change.
6. Fix the root cause.
7. Add a regression test.

### 7.4 Debugging challenge (do not reveal solution yet)

```javascript
function discount(price, percent) {
    const reduction = price * percent;
    return price - reduction;
}

console.log(discount(1000, 10));
```

**Business rule:** `percent` is a percentage from 0 to 100. Predict the output, explain the logic error, fix it, and test 0%, 10%, and 100%.

## 8. Independent assessments

### Level 1: Recognition

A clinic repeatedly prints the same visit heading. Explain whether a function helps, and why.

### Level 2: Return values

Design `calculateArea(width, height)` returning a numeric result. Trace inputs `(4, 6)` and `(0, 5)`.

### Level 3: Conditions and loops

A warehouse processes a numeric sequence of shipment weights. Design a function to count weights exceeding a threshold. You may use arrays after completing the Arrays topic; for now, use fixed numeric arguments or rest parameters.

### Level 4: Validation

Implement `calculateInstallment(total, months)` with clear rules for non-finite numbers, zero months, and negative totals.

### Level 5: Closures and recursion

Build an independent sequential receipt-number generator; separately trace recursive factorial of 5 and explain the base case.

### Level 6: Design and composition

A hotel calculates booking charges from nights, nightly rate, and an optional discount. Design small reusable functions and specify return contracts.

### Level 7: Debugging and testing

Find and correct three different bugs in an implementation of your choice; provide regression tests and discuss time complexity.

### Level 8: Capstone â€” Independent payroll calculator

**Business requirement:** Calculate gross pay, allowance eligibility, deductions, and net pay for a worker. Validate all numeric inputs. Separate pure calculations from display. Provide test cases for boundaries and invalid inputs. **Do not assume statutory tax rates:** use explicitly supplied hypothetical rates, label them as examples, and never represent them as legal payroll rules.

**Submission requirements:** problem decomposition, pseudocode, function signatures, implementation, output samples, line-by-line reasoning, tests, and tradeoffs.

**Mastery threshold:** at least 85% overall and no major weakness in recognizing capabilities, tracing, writing correct functions, debugging, or testing. Solutions to independent assessments are intentionally withheld until requested.

## 9. Coverage and next topic

This guide covers the function capability inventory at different depths: foundational functions, return behavior, scope, validation, closures, recursion, callbacks, invocation forms, and testing are treated directly; arrows, arrays, object construction, iteration protocols, and asynchronous functions are introduced with named follow-up topics. **Inventory coverage does not equal personal mastery.**

**Navigation:** [Previous: Nested Loops](../phase-01-conditions-and-loops/04-complete-nested-for-loops.md) | [â†‘ Contents](#table-of-contents) | [Next: Arrow Functions](06-complete-arrow-functions.md) *(planned)*
