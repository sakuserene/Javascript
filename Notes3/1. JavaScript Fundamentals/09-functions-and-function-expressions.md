# Unit 1 - Lesson 9: JavaScript Functions and Function Expressions

**Level:** Beginner to intermediate  
**Previous:** [Lesson 8 - Loops and Iteration](08-loops-and-iteration.md)  
**Next:** [Lesson 10 - Arrow Functions and Advanced Function Concepts](10-arrow-functions-and-advanced-function-concepts.md)

> A complete, beginner-friendly lesson with executable examples, expected output, line-by-line explanations, a 10-question assessment and ten fully worked exercises. All diagrams use ASCII characters for reliable GitHub rendering.

## Table of contents

- [Learning objectives](#learning-objectives)
- [1. What is a function?](#1-what-is-a-function)
- [2. Function declarations](#2-function-declarations)
- [3. Parameters and arguments](#3-parameters-and-arguments)
- [4. The return statement](#4-the-return-statement)
- [5. Execution walkthrough](#5-execution-walkthrough)
- [6. Default parameters](#6-default-parameters)
- [7. Function expressions](#7-function-expressions)
- [8. Function hoisting](#8-function-hoisting)
- [9. Local and global scope](#9-local-and-global-scope)
- [10. Functions with conditionals](#10-functions-with-conditionals)
- [11. Functions with loops](#11-functions-with-loops)
- [12. Functions calling other functions](#12-functions-calling-other-functions)
- [13. Common function mistakes](#13-common-function-mistakes)
- [14. Knowledge assessment](#14-knowledge-assessment)
- [15. Assessment answer key](#15-assessment-answer-key)
- [16. Coding exercises](#16-coding-exercises)
- [17. Complete exercise solutions](#17-complete-exercise-solutions)
- [18. Lesson summary](#18-lesson-summary)
- [Lesson navigation](#lesson-navigation)

## Learning objectives

By the end, you should be able to define and invoke functions, distinguish parameters from arguments, use `return` instead of confusing it with `console.log`, understand default parameters and function expressions, explain hoisting and scope, and build reusable functions using conditions, loops and other functions.

## 1. What is a function?

A **function** is a reusable block of code that performs a task. Imagine a machine that receives inputs, processes them, and optionally returns an output:

```text
Input --> Function --> Output
```

Without a function, repeated calculations lead to repeated code:

```javascript
let price1 = 500;
let quantity1 = 3;
console.log(price1 * quantity1);

let price2 = 200;
let quantity2 = 4;
console.log(price2 * quantity2);
```

Output:

```text
1500
800
```

A reusable function:

```javascript
function calculateTotal(price, quantity) {
  return price * quantity;
}

console.log(calculateTotal(500, 3));
console.log(calculateTotal(200, 4));
```

Output:

```text
1500
800
```

**Pieces:** `function` introduces a declaration; `calculateTotal` names it; `price` and `quantity` are input parameters; `*` multiplies; `return` supplies the result to the caller. The second call reuses exactly the same calculation with different arguments.

## 2. Function declarations

Syntax:

```javascript
function functionName(parameters) {
  // Statements to execute when called
}
```

| Piece | Meaning |
|---|---|
| `function` | Declares a function |
| `functionName` | Identifier used to refer to it |
| `(` `)` | Enclose parameters or arguments |
| `{` `}` | Enclose the body |
| `;` | Ends statements where appropriate |

```javascript
function greet() {
  console.log("Hello, welcome!");
}

greet();
```

Output:

```text
Hello, welcome!
```

**Step 1:** `function greet()` defines the function but does not run its body. **Step 2:** `{ ... }` holds the instructions. **Step 3:** `greet()` calls the function: the `()` matters. **Step 4:** `console.log(...)` prints the message. **Step 5:** Calling `greet()` again prints the message again.

```javascript
function greet() {
  console.log("Hello!");
}

greet();
greet();
greet();
```

Output:

```text
Hello!
Hello!
Hello!
```

`greet;` merely evaluates the function reference; it does not call it.

## 3. Parameters and arguments

```javascript
function greet(name) {
  console.log("Hello, " + name);
}

greet("Alice");
```

Output:

```text
Hello, Alice
```

`name` in the declaration is a **parameter**. `"Alice"` in the call is an **argument**. The `+` operator joins strings here, so `"Hello, " + "Alice"` becomes `"Hello, Alice"`.

```text
Declaration: greet(name)       name = parameter
Call:        greet("Alice")    "Alice" = argument
During call: name = "Alice"
```

With two parameters:

```javascript
function calculateTotal(price, quantity) {
  console.log(price * quantity);
}

calculateTotal(500, 3);
```

Output: `1500`.

**Trace:** `price` receives `500`; `quantity` receives `3`; `price * quantity` becomes `500 * 3`; multiplication produces `1500`; `console.log` prints it. Arguments are matched to parameters by position.

## 4. The return statement

`console.log` displays something; `return` sends a value to the caller. They are not interchangeable.

```javascript
function add(a, b) {
  console.log(a + b);
}

let result = add(10, 20);
console.log(result);
```

Output:

```text
30
undefined
```

The function printed `30`, but returned no value, so its result is `undefined`.

```javascript
function add(a, b) {
  return a + b;
}

let result = add(10, 20);
console.log(result);
```

Output: `30`.

Break down `return a + b;`:

| Token | Purpose |
|---|---|
| `return` | Ends this function call and supplies a result |
| `a` | First parameter |
| `+` | Numeric addition when both values are numbers |
| `b` | Second parameter |
| `;` | Ends the statement |

A function stops as soon as a `return` is executed:

```javascript
function checkAge(age) {
  if (age < 18) {
    return "Underage";
  }
  return "Adult";
}

console.log(checkAge(15));
console.log(checkAge(22));
```

Output:

```text
Underage
Adult
```

When `age = 15`, `15 < 18` is true and the first `return` ends that call. The later `return` is not reached. When `age = 22`, the condition is false and the last `return` executes.

## 5. Execution walkthrough

```javascript
function add(a, b) {
  return a + b;
}

let answer = add(5, 7);
console.log(answer);
```

```text
1. JavaScript reaches add(5, 7).
2. Parameter a receives 5.
3. Parameter b receives 7.
4. The expression a + b becomes 5 + 7.
5. The expression evaluates to 12.
6. return sends 12 back to the call site.
7. The assignment stores 12 in answer.
8. console.log(answer) prints 12.
```

The assignment `let answer = add(5, 7);` first evaluates the expression on the right (`add(5, 7)`), then initializes `answer` with the returned result.

## 6. Default parameters

```javascript
function greet(name = "Guest") {
  return "Hello, " + name;
}

console.log(greet("Alice"));
console.log(greet());
```

Output:

```text
Hello, Alice
Hello, Guest
```

`name = "Guest"` in a parameter list means: use `"Guest"` when the caller omits this argument or supplies `undefined`. It does **not** replace `null`.

```javascript
function display(value = "Default") {
  console.log(value);
}

display(undefined); // Default
display(null);      // null
```

Another example:

```javascript
function calculatePrice(price, quantity = 1) {
  return price * quantity;
}

console.log(calculatePrice(500));    // 500
console.log(calculatePrice(500, 4)); // 2000
```

When quantity is missing, multiplication uses `1`. When quantity is provided, the supplied value replaces the default.

## 7. Function expressions

A **function expression** creates a function as a value, commonly assigned to a variable:

```javascript
const multiply = function(a, b) {
  return a * b;
};

console.log(multiply(5, 4));
```

Output: `20`.

| Piece | Meaning |
|---|---|
| `const` | Creates a binding that cannot be reassigned |
| `multiply` | Variable holding a function value |
| `=` | Assigns the function value |
| `function(a, b)` | Creates an anonymous function with two parameters |
| `return a * b` | Multiplies and returns the result |
| `};` | Closes the function expression and declaration statement |

Compare:

```javascript
// Function declaration
function add(a, b) {
  return a + b;
}

// Function expression (a different name to avoid redeclaration)
const addValues = function(a, b) {
  return a + b;
};

console.log(add(10, 20));       // 30
console.log(addValues(10, 20)); // 30
```

Both produce function values, but their initialization timing differs.

## 8. Function hoisting

Function declarations can generally be called before their textual declaration within the same scope:

```javascript
sayHello();

function sayHello() {
  console.log("Hello!");
}
```

Output: `Hello!`.

However:

```javascript
sayHello(); // ReferenceError

const sayHello = function() {
  console.log("Hello!");
};
```

A `const` binding cannot be accessed before its initialization (the temporal dead zone). This is different from a function declaration's initialization. Block-level function declarations have additional context-dependent rules; avoid relying on obscure hoisting behavior.

## 9. Local and global scope

A variable declared inside a function is not normally accessible outside that function:

```javascript
function greet() {
  let message = "Hello";
  console.log(message);
}

greet(); // Hello
// console.log(message); // ReferenceError
```

A function can also read variables from its enclosing scope:

```javascript
let taxRate = 0.16;

function calculateTax(amount) {
  return amount * taxRate;
}

console.log(calculateTax(1000)); // 160
```

Shadowing uses separate variables with the same name:

```javascript
let message = "Outside";

function display() {
  let message = "Inside";
  console.log(message);
}

display();             // Inside
console.log(message); // Outside
```

The inner declaration shadows the outer variable only within its own scope. Prefer explicit parameters over unnecessary dependencies on mutable outer state.

## 10. Functions with conditionals

**Scenario:** A shop gives a 10% discount when the purchase amount is at least 1000.

```javascript
function calculateFinalAmount(amount) {
  if (amount >= 1000) {
    return amount * 0.90;
  }
  return amount;
}

console.log(calculateFinalAmount(1500)); // 1350
console.log(calculateFinalAmount(800));  // 800
```

**Piece 1:** `amount` receives the argument. **Piece 2:** `>=` checks whether the amount is at least 1000. **Piece 3:** `0.90` means 90% of the original amount, because 100% - 10% = 90%. **Piece 4:** `*` multiplies the amount by 0.90. **Piece 5:** `return` sends the discounted result to the caller. **Piece 6:** If the condition is false, the final `return amount` sends back the original price.

## 11. Functions with loops

**Interview-style scenario:** Given a shopping cart of product objects, calculate the combined cost of all items.

```javascript
const cart = [
  { name: "Notebook", price: 150, quantity: 3 },
  { name: "Pen", price: 50, quantity: 4 },
  { name: "Bag", price: 1200, quantity: 1 }
];

function calculateCartTotal(items) {
  let total = 0;

  for (const item of items) {
    total += item.price * item.quantity;
  }

  return total;
}

console.log(calculateCartTotal(cart));
```

Output: `1850`.

### Break down the syntax

- `[]` creates an array containing three objects.
- `{ name: ..., price: ..., quantity: ... }` creates one product object.
- `items` is the parameter receiving the array when `calculateCartTotal(cart)` runs.
- `let total = 0` creates a running sum, initially zero.
- `for (const item of items)` visits each array element in order.
- `item.price` reads the current object's `price` property; `.` is the property-access operator.
- `item.quantity` reads its `quantity` property.
- `*` calculates the cost of the current product line.
- `+=` adds that cost to `total` and stores the updated sum.
- `return total` sends the final total to the caller.

The compact line:

```javascript
total += item.price * item.quantity;
```

means exactly:

```javascript
const lineTotal = item.price * item.quantity;
total = total + lineTotal;
```

**Manual trace:**

| Iteration | Current item | Calculation | Old total | New total |
|---|---|---|---:|---:|
| 1 | Notebook | 150 * 3 = 450 | 0 | 450 |
| 2 | Pen | 50 * 4 = 200 | 450 | 650 |
| 3 | Bag | 1200 * 1 = 1200 | 650 | 1850 |

The loop ends after the third object; the function returns `1850`.

## 12. Functions calling other functions

```javascript
function calculateSubtotal(price, quantity) {
  return price * quantity;
}

function calculateTax(subtotal, taxRate) {
  return subtotal * taxRate;
}

function calculateInvoice(price, quantity, taxRate) {
  const subtotal = calculateSubtotal(price, quantity);
  const tax = calculateTax(subtotal, taxRate);
  return subtotal + tax;
}

console.log(calculateInvoice(500, 3, 0.16));
```

Output: `1740`.

```text
calculateInvoice(500, 3, 0.16)
|-- calculateSubtotal(500, 3) --> 1500
|-- calculateTax(1500, 0.16) --> 240
\-- 1500 + 240 --> return 1740
```

The line `const subtotal = calculateSubtotal(price, quantity);` calls another function, waits for its return value, and assigns that value to `subtotal`. The line `const tax = calculateTax(subtotal, taxRate);` then passes the subtotal to another function. This is **function composition**.

## 13. Common function mistakes

### Mistake A: Forgetting parentheses

```javascript
function greet() { console.log("Hello"); }
greet;   // Does not call the function
greet(); // Prints Hello
```

### Mistake B: Printing instead of returning

```javascript
function add(a, b) { console.log(a + b); }
const result = add(5, 5); // Prints 10; result is undefined
```

Use `return` if the caller needs to store or reuse the result.

### Mistake C: Incorrect argument order

```javascript
function subtract(a, b) { return a - b; }
console.log(subtract(10, 5)); // 5
console.log(subtract(5, 10)); // -5
```

### Mistake D: Unreachable code after return

```javascript
function example() {
  return 10;
  console.log("Never reached");
}
```

### Mistake E: Unintended string concatenation

```javascript
function add(a, b) { return a + b; }
console.log(add("10", 20)); // "1020", not 30
```

The `+` operator concatenates when string conversion applies. Validate inputs or convert intentionally.

### Mistake F: Missing or unexpected arguments

```javascript
function multiply(a, b) { return a * b; }
console.log(multiply(5)); // NaN because b is undefined
```

`5 * undefined` produces `NaN`. Default parameters or explicit validation can prevent this.

## 14. Knowledge assessment

Try answering without looking at the key.

1. **What is the main purpose of a function?** A. Store only numbers; B. Create reusable code; C. Repeat forever; D. Declare arrays.
2. **Which syntax calls a function named `greet`?** A. `function greet`; B. `greet`; C. `greet()`; D. `call greet`.
3. **What is a parameter?** A. Value passed during a call; B. Variable declared in a function's parameter list; C. Loop counter only; D. Function output.
4. **What is an argument?** A. Function name; B. `return` keyword; C. Value supplied in a function call; D. Local scope.
5. **What does `return` do?** A. Prints automatically; B. Ends the current function call and provides a result; C. Creates a loop; D. Always returns a string.
6. **What does a function return if it completes without a `return` statement?** A. `null`; B. `0`; C. `undefined`; D. `false`.
7. **What is `multiply(3, 4)` if `multiply(a, b)` returns `a * b`?** A. 7; B. 12; C. 34; D. `undefined`.
8. **What does `greet()` return if the declaration is `function greet(name = "Guest") { return "Hello, " + name; }`?** A. `Hello, undefined`; B. `Hello, null`; C. `Hello, Guest`; D. Error.
9. **Can a function declaration generally be called before its declaration in the same scope?** A. Yes; B. No; C. Only with `const`; D. Only in a loop.
10. **What does `check(15)` return if `function check(n) { if (n > 10) return "Large"; return "Small"; }`?** A. `Small`; B. `Large`; C. `15`; D. `undefined`.

## 15. Assessment answer key

1. **B - Create reusable code.** The body is defined once and may be called repeatedly. The function does not automatically repeat unless it is called repeatedly.
2. **C - `greet()`.** The parentheses invoke the function. Merely writing `greet` accesses the function reference.
3. **B - Variable in a function's parameter list.** For `function greet(name)`, `name` is the parameter.
4. **C - Value supplied during a call.** In `greet("Alice")`, `"Alice"` is the argument passed into `name`.
5. **B - End the call and provide a result.** `return a + b` evaluates the expression and returns its value. It does not print it.
6. **C - `undefined`.** A normal function that reaches its end without returning a value produces `undefined`.
7. **B - 12.** `a = 3`, `b = 4`, so `a * b` is `3 * 4 = 12`.
8. **C - `Hello, Guest`.** Omitting the argument makes the parameter use its default `"Guest"`.
9. **A - Yes.** Function declarations are normally initialized before their surrounding statements execute. Function expressions assigned to `const` behave differently.
10. **B - `Large`.** `15 > 10` is true, so the first `return` runs and exits the function.

## 16. Coding exercises

For each question, first write your own solution. Then compare with the fully explained answers below.

1. **Greeting:** Write `greetUser(name)` that returns `"Hello, Alice!"` for `"Alice"`.
2. **Addition:** Write `addNumbers(a, b)` returning `40` for `15, 25`.
3. **Even or odd:** Write `checkEvenOdd(number)` returning `"Odd"` for `17`.
4. **Largest of three:** Write a function returning `45` for `12, 45, 28`.
5. **Discount:** Apply 15% off purchases at least `2000`; `2500` should return `2125`.
6. **Factorial:** Calculate `5!`, returning `120`; handle nonnegative integer input.
7. **Array sum:** Return `100` for `[10, 20, 30, 40]`.
8. **Vowel count:** Return `5` for `"education"`.
9. **Shopping cart:** Calculate total for two books at 400 each, five pens at 50 each, and one bag at 1500; return `2550`.
10. **Student result analyzer:** For `[78, 92, 65, 88, 45]`, return total `368`, average `73.6`, highest `92`, lowest `45`, passed `4`, failed `1`.

## 17. Complete exercise solutions

### Exercise 1 - Greeting function

**Interview question:** Create a reusable greeting function that accepts a name and returns a greeting. Do not print from inside the function.

```javascript
function greetUser(name) {
  return "Hello, " + name + "!";
}

console.log(greetUser("Alice"));
```

Expected output:

```text
Hello, Alice!
```

**Step-by-step code pieces:**

1. `function greetUser(name)` declares the function. The identifier `greetUser` is its name; `name` is a parameter.
2. `"Hello, "` is the first string, including a trailing space.
3. `+ name` concatenates the supplied name onto the greeting. With `name = "Alice"`, the intermediate result is `"Hello, Alice"`.
4. `+ "!"` appends an exclamation mark, producing `"Hello, Alice!"`.
5. `return` sends the completed string to the caller and exits the function.
6. `greetUser("Alice")` passes the argument `"Alice"` into `name`.
7. `console.log(...)` prints the returned string. The function itself does not print.

**Trace:** `name = "Alice"` --> `"Hello, " + "Alice"` --> `"Hello, Alice"` --> append `"!"` --> return `"Hello, Alice!"`.

### Exercise 2 - Addition calculator

**Interview question:** Write a function that adds two numeric arguments and returns the result.

```javascript
function addNumbers(a, b) {
  return a + b;
}

console.log(addNumbers(15, 25));
```

Expected output: `40`.

**Code pieces:**

1. `a` and `b` are parameters. The call `addNumbers(15, 25)` assigns `a = 15` and `b = 25`.
2. `+` adds the numeric operands; `15 + 25` evaluates to `40`.
3. `return` sends `40` back; `console.log` displays it.
4. This simple function assumes numeric inputs. With a string operand, `+` may concatenate instead.

**Trace:** `a=15` --> `b=25` --> evaluate `15+25` --> return `40` --> print `40`.

### Exercise 3 - Even or odd

**Interview question:** Return `"Even"` for even integers and `"Odd"` for odd integers.

```javascript
function checkEvenOdd(number) {
  if (number % 2 === 0) {
    return "Even";
  }
  return "Odd";
}

console.log(checkEvenOdd(17));
```

Expected output: `Odd`.

**Code pieces:**

1. `number` receives the argument `17`.
2. `%` is the remainder operator: `17 % 2` is `1` because dividing 17 by 2 leaves remainder 1.
3. `===` compares both type and value; `1 === 0` evaluates to `false`.
4. `if (...)` runs its body only if the condition is truthy; here it skips `return "Even"`.
5. Execution reaches `return "Odd"`, which returns the string.

**Trace:** `number=17` --> `17%2=1` --> `1===0` is false --> skip first return --> return `"Odd"`.

### Exercise 4 - Largest of three numbers

**Interview question:** Determine the largest of three numbers without using `Math.max`.

```javascript
function largestOfThree(a, b, c) {
  if (a >= b && a >= c) {
    return a;
  }
  if (b >= a && b >= c) {
    return b;
  }
  return c;
}

console.log(largestOfThree(12, 45, 28));
```

Expected output: `45`.

**Code pieces:**

1. Parameters receive `a=12`, `b=45`, `c=28`.
2. `>=` means greater than or equal to. `a >= b` becomes `12 >= 45`, which is false.
3. `&&` is logical AND: both sides must be truthy for the combined condition to be true. Since the first side is false, JavaScript short-circuits and skips the second comparison.
4. The second `if` checks `b >= a && b >= c`: `45 >= 12` is true, and `45 >= 28` is true.
5. Because both comparisons are true, `return b` returns `45` and ends the function.
6. `return c` is a fallback for cases where neither `a` nor `b` is largest.

**Trace:** first condition false --> second condition true --> return `45`. Using `>=` also handles ties consistently.

### Exercise 5 - Discount calculator

**Interview question:** Apply a 15% discount when the amount is at least 2000, otherwise return the original amount.

```javascript
function applyDiscount(amount) {
  if (amount >= 2000) {
    const discount = amount * 0.15;
    return amount - discount;
  }
  return amount;
}

console.log(applyDiscount(2500));
```

Expected output: `2125`.

**Code pieces:**

1. `amount=2500` comes from the call.
2. `amount >= 2000` checks the threshold; `2500 >= 2000` is true.
3. `const discount = amount * 0.15` creates a variable holding 15% of the amount. `0.15` represents 15 divided by 100.
4. `2500 * 0.15` evaluates to `375`, so `discount=375`.
5. `amount - discount` becomes `2500 - 375 = 2125`.
6. `return` sends `2125` back. The fallback `return amount` does not execute.

**Trace:** `2500` --> eligible --> discount `375` --> final `2125`.

### Exercise 6 - Factorial function

**Interview question:** Compute the factorial of a nonnegative integer using a loop.

```javascript
function factorial(number) {
  if (!Number.isInteger(number) || number < 0) {
    throw new RangeError("Expected a nonnegative integer");
  }

  let result = 1;

  for (let i = 2; i <= number; i++) {
    result *= i;
  }

  return result;
}

console.log(factorial(5));
```

Expected output: `120`.

**Code pieces:**

1. `Number.isInteger(number)` checks whether the input is an integer. For `5`, it is true.
2. `!` negates the Boolean: `!true` is false.
3. `||` means logical OR; `number < 0` checks for a negative number. The condition is false for `5`, so `throw` is skipped.
4. `throw new RangeError(...)` would stop normal execution and signal invalid input. This is more informative than silently returning a wrong value.
5. `let result = 1` initializes the running product to 1, the multiplicative identity.
6. `for (let i = 2; i <= number; i++)` starts `i` at 2, checks that `i` is not above `number`, and increments `i` after each iteration.
7. `result *= i` is shorthand for `result = result * i`.
8. `return result` returns the completed product.

**Manual trace:**

| `i` | Old `result` | `result * i` | New `result` |
|---:|---:|---:|---:|
| 2 | 1 | 1 * 2 | 2 |
| 3 | 2 | 2 * 3 | 6 |
| 4 | 6 | 6 * 4 | 24 |
| 5 | 24 | 24 * 5 | 120 |

When `i` increments to 6, `6 <= 5` is false and the loop stops. For `factorial(0)`, the loop never runs and returns 1, which is correct. JavaScript numbers lose integer precision for sufficiently large factorials; this version is intended for small inputs.

### Exercise 7 - Array sum

**Interview question:** Sum all numbers in an array using a function and loop.

```javascript
function sumArray(numbers) {
  let total = 0;

  for (const number of numbers) {
    total += number;
  }

  return total;
}

console.log(sumArray([10, 20, 30, 40]));
```

Expected output: `100`.

**Code pieces:**

1. `numbers` receives the array `[10, 20, 30, 40]`.
2. `let total = 0` creates an accumulator starting at zero.
3. `for (const number of numbers)` takes each array value in order. `const number` creates a new binding for each iteration; `of` requests values rather than property keys.
4. `total += number` expands to `total = total + number`.
5. `return total` sends the sum to the caller.

**Manual trace:**

| Iteration | `number` | Before | After |
|---:|---:|---:|---:|
| 1 | 10 | 0 | 10 |
| 2 | 20 | 10 | 30 |
| 3 | 30 | 30 | 60 |
| 4 | 40 | 60 | 100 |

### Exercise 8 - Count vowels

**Interview question:** Count the English vowels in a word without using a regular expression.

```javascript
function countVowels(word) {
  const vowels = "aeiou";
  let count = 0;

  for (const character of word.toLowerCase()) {
    if (vowels.includes(character)) {
      count++;
    }
  }

  return count;
}

console.log(countVowels("education"));
```

Expected output: `5`.

**Code pieces:**

1. `word` receives `"education"`.
2. `const vowels = "aeiou"` stores the five English vowel letters for lookup.
3. `let count = 0` starts the number of matches at zero.
4. `word.toLowerCase()` returns a lowercase version, so uppercase letters can also be matched.
5. `for (const character of ...)` visits each character in sequence.
6. `vowels.includes(character)` checks whether the current character appears in the string `"aeiou"`. It returns a Boolean.
7. `if (...)` executes `count++` only when that Boolean is true.
8. `count++` is shorthand for increasing `count` by one (the expression itself yields the previous value, but that value is unused here).
9. `return count` returns the total matches.

**Trace:**

| Character | Vowel? | Count after |
|---|---|---:|
| e | Yes | 1 |
| d | No | 1 |
| u | Yes | 2 |
| c | No | 2 |
| a | Yes | 3 |
| t | No | 3 |
| i | Yes | 4 |
| o | Yes | 5 |
| n | No | 5 |

### Exercise 9 - Shopping cart total

**Interview question:** Given an array of product objects, calculate the total price across quantities.

```javascript
const cart = [
  { name: "Book", price: 400, quantity: 2 },
  { name: "Pen", price: 50, quantity: 5 },
  { name: "Bag", price: 1500, quantity: 1 }
];

function calculateCartTotal(items) {
  let total = 0;

  for (const item of items) {
    const lineTotal = item.price * item.quantity;
    total += lineTotal;
  }

  return total;
}

console.log(calculateCartTotal(cart));
```

Expected output: `2550`.

**Code pieces:**

1. `const cart = [...]` declares an array. `[]` groups elements; each `{...}` is a product object.
2. `name`, `price`, and `quantity` are object property names separated from values by `:`.
3. `calculateCartTotal(items)` receives the array in its `items` parameter.
4. `let total = 0` starts a running sum.
5. `for (const item of items)` loops through the product objects one by one.
6. `item.price` uses `.` to read the price property; `item.quantity` reads the quantity.
7. `const lineTotal = item.price * item.quantity` calculates the cost of all units of the current product.
8. `total += lineTotal` expands to `total = total + lineTotal`, adding the current line cost.
9. `return total` provides the final amount.

**Manual trace:**

| Product | Price | Quantity | Line total | Running total |
|---|---:|---:|---:|---:|
| Book | 400 | 2 | 800 | 800 |
| Pen | 50 | 5 | 250 | 1050 |
| Bag | 1500 | 1 | 1500 | 2550 |

**Interview follow-up:** How would you handle negative quantities or nonnumeric prices? In production code, validate the inputs before computing the total. For money, consider integer minor units to avoid floating-point rounding errors.

### Exercise 10 - Student result analyzer

**Interview question:** Write a reusable function that summarizes student scores. A passing score is at least 50. Assume a nonempty array of valid numeric scores.

```javascript
const scores = [78, 92, 65, 88, 45];

function analyzeScores(scores) {
  let total = 0;
  let highest = scores[0];
  let lowest = scores[0];
  let passed = 0;
  let failed = 0;

  for (const score of scores) {
    total += score;

    if (score > highest) {
      highest = score;
    }

    if (score < lowest) {
      lowest = score;
    }

    if (score >= 50) {
      passed++;
    } else {
      failed++;
    }
  }

  const average = total / scores.length;

  return { total, average, highest, lowest, passed, failed };
}

console.log(analyzeScores(scores));
```

Expected result (object property display may vary by environment):

```text
{
  total: 368,
  average: 73.6,
  highest: 92,
  lowest: 45,
  passed: 4,
  failed: 1
}
```

**Break down every meaningful piece:**

1. `scores = [78, 92, 65, 88, 45]` is an array containing five numeric elements.
2. `function analyzeScores(scores)` defines the function; its parameter `scores` receives the array passed to it.
3. `let total = 0` initializes an accumulator for the sum.
4. `let highest = scores[0]` starts the maximum with the first array element, `78`. `[0]` accesses the element at index zero.
5. `let lowest = scores[0]` similarly starts the minimum at `78`.
6. `let passed = 0` and `let failed = 0` initialize counters.
7. `for (const score of scores)` visits each score in order, assigning it to `score` for that iteration.
8. `total += score` means `total = total + score` and updates the running sum.
9. `if (score > highest)` checks whether the current score exceeds the previous maximum. If so, `highest = score` replaces the maximum.
10. `if (score < lowest)` checks whether the current score is smaller than the previous minimum. If so, `lowest = score` replaces the minimum.
11. `if (score >= 50)` checks the passing threshold. The `>=` operator means greater than or equal to.
12. `passed++` adds one to the pass counter. `else` runs only when the condition is false; `failed++` adds one to the fail counter.
13. `scores.length` returns the number of elements, which is `5`.
14. `total / scores.length` divides `368` by `5`, producing `73.6`.
15. `return { total, average, highest, lowest, passed, failed }` creates an object using property-name shorthand: `{ total }` means `{ total: total }`.
16. `console.log(analyzeScores(scores))` calls the function and prints the returned object.

**Manual trace:**

| Score | Total after | Highest after | Lowest after | Passed | Failed |
|---:|---:|---:|---:|---:|---:|
| Start | 0 | 78 | 78 | 0 | 0 |
| 78 | 78 | 78 | 78 | 1 | 0 |
| 92 | 170 | 92 | 78 | 2 | 0 |
| 65 | 235 | 92 | 65 | 3 | 0 |
| 88 | 323 | 92 | 65 | 4 | 0 |
| 45 | 368 | 92 | 45 | 4 | 1 |

**Final calculation:** `average = 368 / 5 = 73.6`.

**Interview follow-up:** What happens for `[]`? The current solution would use `undefined` for highest and lowest and divide zero by zero. A robust production version should explicitly handle empty input, such as returning `null` or a documented empty summary, and should validate that scores are numbers within the permitted range.

## 18. Lesson summary

| Concept | Meaning |
|---|---|
| Function | Reusable block of instructions |
| Declaration | Named function declared with `function` |
| Call | Invocation using parentheses |
| Parameter | Variable listed in function definition |
| Argument | Value supplied in a call |
| `return` | Ends call and sends result |
| Default parameter | Fallback for missing or `undefined` argument |
| Function expression | Function created as part of an expression |
| Hoisting | Declaration initialization behavior |
| Local scope | Variable visibility inside function/block |
| Global scope | Variables in global scope |
| Composition | Functions calling other functions |

**Remember:** `console.log(value)` prints a value; `return value` makes that value available to the caller. A function can be defined once and called many times with different arguments. Break complex programs into small functions, use descriptive names, and validate assumptions about input data.

## Lesson navigation

- **Previous:** [Unit 1 - Lesson 8: Loops and Iteration](08-loops-and-iteration.md)
- **Current:** Unit 1 - Lesson 9: Functions and Function Expressions
- **Next:** [Unit 1 - Lesson 10: Arrow Functions and Advanced Function Concepts](10-arrow-functions-and-advanced-function-concepts.md)

**Suggested repository path:** `javascript-course/unit-01-fundamentals/09-functions-and-function-expressions.md`
