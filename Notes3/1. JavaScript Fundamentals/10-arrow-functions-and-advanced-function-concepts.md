# Unit 1 - Lesson 10: Arrow Functions and Advanced Function Concepts

**Level:** Beginner to Intermediate, progressing to Advanced  
**Previous:** [Lesson 9 - Functions and Function Expressions](09-functions-and-function-expressions.md)  
**Next:** [Lesson 11 - JavaScript Arrays and Array Methods](11-javascript-arrays-and-array-methods.md)

## Table of Contents

1. [Learning Objectives](#1-learning-objectives)
2. [Arrow Function Basics](#2-arrow-function-basics)
3. [Implicit and Explicit Return](#3-implicit-and-explicit-return)
4. [Parameter Variations](#4-parameter-variations)
5. [Returning Objects](#5-returning-objects)
6. [Functions as Values](#6-functions-as-values)
7. [Callbacks](#7-callbacks)
8. [Higher-Order Functions](#8-higher-order-functions)
9. [Array Iteration Callbacks](#9-array-iteration-callbacks)
10. [Closures and Lexical Scope](#10-closures-and-lexical-scope)
11. [Rest Parameters](#11-rest-parameters)
12. [Spread Syntax](#12-spread-syntax)
13. [Recursion](#13-recursion)
14. [Arrow Functions and this](#14-arrow-functions-and-this)
15. [Shopping Cart Interview Challenge](#15-shopping-cart-interview-challenge)
16. [Common Mistakes](#16-common-mistakes)
17. [Ten Interview Questions with Answers](#17-ten-interview-questions-with-answers)
18. [Ten Coding Exercises](#18-ten-coding-exercises)
19. [Complete Exercise Solutions](#19-complete-exercise-solutions)
20. [Lesson Summary and Navigation](#20-lesson-summary-and-navigation)

## 1. Learning Objectives

After completing this lesson, you should be able to write arrow functions, distinguish implicit from explicit returns, pass functions as values, implement callbacks and higher-order functions, use `map`, `filter`, and `reduce`, explain closures, distinguish rest and spread, write safe recursive functions, and explain the behavior of `this` in arrow functions. Each example below explains the syntax, then traces execution.

## 2. Arrow Function Basics

### Interview-style question

**Q:** Rewrite a traditional addition function as an arrow function. Explain every symbol and show the output.

Traditional function expression:

```javascript
const add = function(a, b) {
  return a + b;
};
```

Arrow function:

```javascript
const add = (a, b) => {
  return a + b;
};

console.log(add(10, 20)); // 30
```

### Code pieces

| Code | Meaning |
| --- | --- |
| `const` | Declares a binding that cannot be reassigned. |
| `add` | Name of the variable holding the function. |
| `=` | Assigns the function value to the variable. |
| `(a, b)` | Declares two parameters. |
| `a` | First parameter. |
| `,` | Separates parameters. |
| `b` | Second parameter. |
| `=>` | Separates the parameter list from the arrow function body. |
| `{` and `}` | Start and end a block body. |
| `return` | Ends this function call and returns a value. |
| `a + b` | Adds numeric operands (or concatenates when string conversion applies). |
| `;` | Ends a statement. |
| `add(10, 20)` | Calls the function with arguments 10 and 20. |

### Execution trace

1. JavaScript evaluates the arrow function and stores it in `add`.
2. `add(10, 20)` calls that function.
3. `a` receives `10`; `b` receives `20`.
4. `a + b` becomes `10 + 20`.
5. The expression evaluates to `30`.
6. `return` sends `30` to the caller.
7. `console.log` prints `30`.

## 3. Implicit and Explicit Return

**Q:** Why do these two functions return the same result?

```javascript
const multiplyA = (a, b) => {
  return a * b;
};

const multiplyB = (a, b) => a * b;

console.log(multiplyA(5, 4)); // 20
console.log(multiplyB(5, 4)); // 20
```

**Explicit return:** The `{ ... }` block contains the `return` keyword. **Implicit return:** When an arrow function uses an expression body without `{ ... }`, the value of that expression is automatically returned.

| Piece | Explanation |
| --- | --- |
| `a * b` | Multiply `a` by `b`. |
| `=> a * b` | Evaluate and return that expression. |
| `=> { return a * b; }` | Execute a block and explicitly return the product. |

**Common mistake:**

```javascript
const wrong = (a, b) => {
  a * b;
};
console.log(wrong(5, 4)); // undefined
```

The multiplication occurs, but its result is discarded. There is no `return`. The function finishes and returns `undefined`.

## 4. Parameter Variations

```javascript
const hello = () => "Hello";
const square = number => number * number;
const rectangleArea = (length, width) => length * width;
const total = (price, quantity = 1) => price * quantity;

console.log(hello());            // Hello
console.log(square(6));         // 36
console.log(rectangleArea(5, 8)); // 40
console.log(total(500));        // 500
console.log(total(500, 3));     // 1500
```

- `()` is mandatory when there are zero parameters.
- A single simple parameter may omit its surrounding parentheses.
- Two or more parameters require parentheses.
- `quantity = 1` is a default parameter. It uses `1` if the argument is omitted or explicitly `undefined`, but not if it is `null`.
- `price * quantity` is multiplication, not exponentiation.

**Trace for `total(500, 3)`:** `price = 500`, `quantity = 3`, `500 * 3 = 1500`, return `1500`.

## 5. Returning Objects

**Q:** Why must a concise arrow function wrap an object literal in parentheses?

```javascript
const createUser = (name, age) => ({ name, age });
console.log(createUser("Alice", 25));
// { name: 'Alice', age: 25 }
```

| Piece | Explanation |
| --- | --- |
| `(name, age)` | Two parameters. |
| `=>` | Introduces the arrow function body. |
| `(` immediately after `=>` | Groups an expression so `{` is parsed as an object literal. |
| `{ name, age }` | Object literal using property shorthand. |
| `name` | Equivalent to `name: name` in this object. |
| `age` | Equivalent to `age: age` in this object. |

Without grouping parentheses, `{ ... }` is interpreted as a function block, not a directly returned object expression. The longer equivalent is:

```javascript
const createUserLong = (name, age) => {
  return { name: name, age: age };
};
```

## 6. Functions as Values

```javascript
const greet = () => "Hello!";
const sayHello = greet;
console.log(sayHello()); // Hello!
```

**Crucial distinction:** `greet` refers to the function value. `greet()` invokes it and evaluates to its return value. In `const sayHello = greet`, both names refer to the same function. If you wrote `const sayHello = greet()`, `sayHello` would contain the string `"Hello!"`, not a function.

## 7. Callbacks

**Q:** What is a callback and when is it called?

```javascript
function greet(name) {
  return "Hello, " + name;
}

function processUser(name, callback) {
  return callback(name);
}

console.log(processUser("Alice", greet)); // Hello, Alice
```

### Explain each important piece

- `processUser(name, callback)` declares two parameters. The second is intended to hold a function.
- `processUser("Alice", greet)` passes a string and a **function reference**.
- `callback(name)` invokes the supplied function with `name` as its argument.
- `return callback(name)` sends the callback's result to the caller.

### Execution trace

1. `processUser("Alice", greet)` starts.
2. Local `name` becomes `"Alice"`.
3. Local `callback` refers to the `greet` function.
4. `callback(name)` is equivalent here to `greet("Alice")`.
5. `greet` evaluates `"Hello, " + "Alice"`.
6. `greet` returns `"Hello, Alice"`.
7. `processUser` returns the same string.
8. `console.log` displays it.

## 8. Higher-Order Functions

A higher-order function accepts a function, returns a function, or both.

```javascript
function calculate(a, b, operation) {
  return operation(a, b);
}

const add = (a, b) => a + b;
const multiply = (a, b) => a * b;

console.log(calculate(10, 5, add));      // 15
console.log(calculate(10, 5, multiply)); // 50
console.log(calculate(10, 5, (a, b) => a - b)); // 5
```

**Trace for multiplication:** `a = 10`, `b = 5`, `operation = multiply`, `operation(a, b)` calls `multiply(10, 5)`, evaluates `10 * 5`, and returns `50`.

The inline `(a, b) => a - b` is an anonymous arrow function passed directly as an argument. Its `a - b` expression is implicitly returned.

## 9. Array Iteration Callbacks

### 9.1 forEach

```javascript
const numbers = [10, 20, 30];
numbers.forEach(number => console.log(number));
// 10
// 20
// 30
```

`numbers` is the array. The dot (`.`) accesses the `forEach` method. The arrow function is a callback. Each invocation receives a current element in `number`. `forEach` returns `undefined`; use it primarily for side effects rather than constructing a new array.

### 9.2 map

```javascript
const numbers = [2, 4, 6];
const doubled = numbers.map(number => number * 2);
console.log(doubled); // [4, 8, 12]
```

| Iteration | `number` | `number * 2` | New array so far |
| --- | ---: | ---: | --- |
| 1 | 2 | 4 | `[4]` |
| 2 | 4 | 8 | `[4, 8]` |
| 3 | 6 | 12 | `[4, 8, 12]` |

`map` collects each callback's returned value into a new array. The original array is not modified by this callback.

### 9.3 filter

```javascript
const numbers = [5, 12, 8, 20, 3];
const large = numbers.filter(number => number > 10);
console.log(large); // [12, 20]
```

`number > 10` compares the current element with 10 and returns a boolean. `filter` keeps elements whose callback returns a truthy value.

| Element | Test | Keep? |
| ---: | --- | --- |
| 5 | `5 > 10` -> false | No |
| 12 | `12 > 10` -> true | Yes |
| 8 | `8 > 10` -> false | No |
| 20 | `20 > 10` -> true | Yes |
| 3 | `3 > 10` -> false | No |

### 9.4 reduce

```javascript
const numbers = [10, 20, 30];
const total = numbers.reduce((sum, number) => sum + number, 0);
console.log(total); // 60
```

**Do not skip the final `0`:** It is the initial accumulator value. The callback receives `sum` (the running accumulator) and `number` (the current array element). `sum + number` returns the new accumulator for the next iteration.

| Iteration | `sum` before | `number` | Returned `sum` |
| --- | ---: | ---: | ---: |
| Start | 0 | - | 0 |
| 1 | 0 | 10 | 10 |
| 2 | 10 | 20 | 30 |
| 3 | 30 | 30 | 60 |

## 10. Closures and Lexical Scope

```javascript
function createCounter() {
  let count = 0;
  return function() {
    count++;
    return count;
  };
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```

### Explain the syntax

- `let count = 0` creates a local variable in the outer function.
- `return function() { ... }` returns a **function value**; it does not immediately call that function.
- The returned function can still access `count` from its surrounding lexical environment.
- `count++` is postfix increment. As a standalone statement here, it increases `count` by one.
- `count++` here has the same final effect as `count = count + 1` or `count += 1`.
- `return count` returns the new counter value.

### Execution trace

1. `createCounter()` executes **once**, creating `count = 0`.
2. It returns an inner function and assigns that function to `counter`.
3. First `counter()` call: `count` changes 0 -> 1; returns 1.
4. Second call: 1 -> 2; returns 2.
5. Third call: 2 -> 3; returns 3.

**Interview follow-up:** Two calls to `createCounter()` create separate closure environments:

```javascript
const a = createCounter();
const b = createCounter();
console.log(a()); // 1
console.log(a()); // 2
console.log(b()); // 1
```

## 11. Rest Parameters

```javascript
function sumNumbers(...numbers) {
  let total = 0;
  for (const number of numbers) {
    total += number;
  }
  return total;
}

console.log(sumNumbers(10, 20));         // 30
console.log(sumNumbers(10, 20, 30, 40)); // 100
```

### Every operator and statement

| Code | Meaning |
| --- | --- |
| `...numbers` | Rest parameter: collects remaining arguments into an array named `numbers`. |
| `let total = 0` | Creates an accumulator initially set to zero. |
| `for (const number of numbers)` | Iterates through values of the `numbers` array. |
| `total += number` | Shorthand for `total = total + number`. |
| `return total` | Returns the final accumulator. |

**Trace for `sumNumbers(10, 20, 30)`:** `numbers = [10, 20, 30]`; start `total = 0`; then `0 + 10 = 10`, `10 + 20 = 30`, `30 + 30 = 60`; return 60. A rest parameter must appear last in the parameter list.

## 12. Spread Syntax

```javascript
const numbers = [10, 20, 30];
console.log(Math.max(...numbers)); // 30
```

Here `...numbers` is **spread** syntax, not a rest parameter. It expands the array elements into separate arguments, conceptually `Math.max(10, 20, 30)`.

```javascript
const original = [1, 2, 3];
const copy = [...original];
console.log(copy); // [1, 2, 3]
```

`[...original]` constructs a new array containing the same elements. It is a **shallow** copy: nested objects are not deeply cloned.

| Rest | Spread |
| --- | --- |
| `function sum(...numbers)` | `sum(...numbersArray)` |
| Collects arguments into an array | Expands an iterable into argument positions |

## 13. Recursion

**Q:** Implement factorial using recursion and explain why it stops.

```javascript
function factorial(n) {
  if (n === 0 || n === 1) {
    return 1;
  }
  return n * factorial(n - 1);
}

console.log(factorial(5)); // 120
```

Assume `n` is a nonnegative integer.

### Explain each expression

- `n === 0` uses strict equality to check whether `n` is zero.
- `||` is logical OR. The condition is true if either comparison is true.
- `n === 1` checks whether `n` is one.
- `return 1` is the **base case**: it stops the current chain of recursive calls.
- `n - 1` makes the input smaller.
- `factorial(n - 1)` calls the same function again.
- `n * factorial(n - 1)` multiplies the current `n` by the recursive result.

### Call stack trace

```text
factorial(5)
|-- 5 * factorial(4)
|   |-- 4 * factorial(3)
|   |   |-- 3 * factorial(2)
|   |   |   |-- 2 * factorial(1)
|   |   |   \-- factorial(1) returns 1
|   |   \-- factorial(2) returns 2
|   \-- factorial(3) returns 6
\-- factorial(4) returns 24
factorial(5) returns 120
```

### Iterative alternative

```javascript
function factorialLoop(n) {
  let result = 1;
  for (let i = 2; i <= n; i++) {
    result *= i;
  }
  return result;
}
```

`result *= i` means `result = result * i`. The iterative approach uses constant auxiliary space; the recursive approach uses stack space proportional to `n`. Both are O(n) time for nonnegative integer `n` under the usual unit-cost arithmetic model.

## 14. Arrow Functions and this

**Q:** Why should you be cautious using an arrow function as an object method?

```javascript
const person = {
  name: "Alice",
  greet: function() {
    return "Hello, " + this.name;
  }
};
console.log(person.greet()); // Hello, Alice
```

A traditional function called as `person.greet()` generally receives `person` as its `this` value.

```javascript
const anotherPerson = {
  name: "Bob",
  greet: () => this.name
};
```

The arrow function does **not** bind `this` to `anotherPerson`. Instead it captures `this` from its lexical environment. The actual value depends on that environment.

Arrow functions can be useful **inside** traditional methods:

```javascript
const user = {
  name: "Alice",
  greet: function() {
    const getName = () => this.name;
    return "Hello, " + getName();
  }
};
console.log(user.greet()); // Hello, Alice
```

The inner arrow captures the surrounding method's `this`.

## 15. Shopping Cart Interview Challenge

**Q:** Given a shopping cart, calculate subtotal, discount, and final amount. Explain the callback line by line.

```javascript
const cart = [
  { name: "Notebook", price: 200, quantity: 3 },
  { name: "Pen", price: 50, quantity: 4 },
  { name: "Bag", price: 1200, quantity: 1 }
];

const calculateSubtotal = items => {
  return items.reduce((total, item) => {
    return total + item.price * item.quantity;
  }, 0);
};

const calculateDiscount = (subtotal, rate) => subtotal * rate;

const createSummary = (items, discountRate = 0) => {
  const subtotal = calculateSubtotal(items);
  const discount = calculateDiscount(subtotal, discountRate);
  const finalTotal = subtotal - discount;
  return { subtotal, discount, finalTotal };
};

console.log(createSummary(cart, 0.10));
// { subtotal: 2000, discount: 200, finalTotal: 1800 }
```

### The important line, piece by piece

```javascript
return total + item.price * item.quantity;
```

| Piece | Explanation |
| --- | --- |
| `return` | Sends the computed accumulator back to `reduce`. |
| `total` | Running subtotal from previous iterations. |
| `+` | Adds the current product's cost to the running subtotal. |
| `item` | Current product object. |
| `.` | Accesses a property on an object. |
| `item.price` | Current product's unit price. |
| `*` | Multiplication operator. |
| `item.quantity` | Number of units of this product. |
| `item.price * item.quantity` | Cost of this product line. |
| `total + (...)` | Updated running subtotal. |

**Operator precedence:** Multiplication occurs before addition, so `total + item.price * item.quantity` means `total + (item.price * item.quantity)`.

### Trace every iteration

| Iteration | Product | Previous `total` | Line cost | New `total` |
| --- | --- | ---: | ---: | ---: |
| 1 | Notebook | 0 | 200 * 3 = 600 | 600 |
| 2 | Pen | 600 | 50 * 4 = 200 | 800 |
| 3 | Bag | 800 | 1200 * 1 = 1200 | 2000 |

Then `discount = 2000 * 0.10 = 200` and `finalTotal = 2000 - 200 = 1800`. The returned object uses shorthand properties: `{ subtotal }` is equivalent to `{ subtotal: subtotal }`.

## 16. Common Mistakes

1. **Using `{}` with no `return`:** `(x) => { x * 2; }` returns `undefined`.
2. **Passing a result instead of a callback:** `processUser("Alice", greet())` calls `greet` immediately; `processUser("Alice", greet)` passes the function.
3. **Confusing rest and spread:** Both use `...`, but their grammatical positions determine their roles.
4. **Forgetting a recursion base case:** Recursive calls may never terminate normally.
5. **Assuming arrow method `this` is the object:** Arrow functions inherit lexical `this`.
6. **Forgetting `reduce`'s initial value:** `reduce(callback, 0)` is often safer for sums and works with empty arrays.
7. **Using `map` only for side effects:** Prefer `forEach` for side effects and `map` when building a transformed array.
8. **Assuming spread makes a deep copy:** It makes a shallow copy.

## 17. Ten Interview Questions with Answers

### Q1. What is the difference between an arrow function and a traditional function?

**Answer:** Arrow functions have lexical `this`, do not have their own `arguments` object, and cannot be used as constructors. Traditional functions can have call-dependent `this` and, when constructible, may be called with `new`. Arrow functions support concise implicit-return expression bodies.

**Example:** `const square = n => n * n;` returns the square. A traditional method can use `this` to access its receiving object.

### Q2. What is an implicit return?

**Answer:** A concise arrow function automatically returns its expression result. Example: `n => n * 2`. A block-bodied arrow function needs `return` to provide a value.

**Trace:** `n = 4` -> `4 * 2` -> `8` returned.

### Q3. What is a callback?

**Answer:** A function supplied to another function to be invoked by that function. Example: `numbers.map(n => n * 2)`. The callback is invoked for elements processed by `map`.

### Q4. What is a higher-order function?

**Answer:** A function that accepts a function, returns a function, or both. `map`, `filter`, and `reduce` are common examples.

### Q5. What is a closure?

**Answer:** A function together with access to its lexical environment. A returned counter function can continue reading and updating a `count` variable from the outer call.

### Q6. Rest versus spread: what is the difference?

**Answer:** Rest **collects** remaining arguments: `function f(...values) {}`. Spread **expands** iterable values: `f(...array)`.

### Q7. Why does an arrow method not automatically use its object as `this`?

**Answer:** Arrow functions do not establish their own `this` binding; they capture `this` from the lexical environment where they were created.

### Q8. How do `map` and `forEach` differ?

**Answer:** `map` returns a new array of callback results; `forEach` invokes callbacks for side effects and returns `undefined`.

### Q9. What is a recursion base case?

**Answer:** A condition that stops further recursive calls. In factorial, when `n` is 0 or 1, return 1 without another recursive call.

### Q10. Why does `(a, b) => { a + b; }` return `undefined`?

**Answer:** Curly braces create a block body, so the addition expression's value is discarded. Write `return a + b;` inside the block or use `(a, b) => a + b`.

## 18. Ten Coding Exercises

Solve these before reading Section 19. Each exercise is framed as a practical interview task.

1. **Arrow conversion:** Convert `function calculateSquare(number) { return number * number; }` to an arrow function. Input 7, output 49.
2. **Product price:** Create `calculatePrice(price, quantity, discountRate = 0)` returning total after discount. Input `(500, 4, 0.10)`, output 1800.
3. **Passing students:** Filter student objects to scores >= 50. Alice 85, Bob 42, Carol 73, David 35. Expected Alice and Carol.
4. **Price increase:** Use `map` to increase `[100, 200, 300]` by 10%. Expected `[110, 220, 330]`.
5. **Expense sum:** Use `reduce` to sum `[1200, 450, 800, 250]`. Expected 2700.
6. **Reusable calculator:** Write `calculate(a, b, operation)` and demonstrate add, subtract, multiply, and divide.
7. **Closure counter:** Write `createCounter()` so repeated calls to its returned function produce 1, 2, 3.
8. **Average with rest:** Write `average(...numbers)`. Input 10, 20, 30, 40; output 25. Decide how to handle zero arguments.
9. **Recursive sum:** Write `sumToN(n)` to sum integers 1 through n. Input 5; output 15.
10. **Sales report:** For Laptop (50000 x 2), Mouse (1500 x 5), Keyboard (3500 x 3), Monitor (20000 x 2), calculate total revenue 158000, units 12, average per unit 13166.67, highest-revenue product Laptop, and products with line revenue above 10000: Laptop, Keyboard, Monitor.

## 19. Complete Exercise Solutions

### Solution 1: Arrow conversion

```javascript
const calculateSquare = number => number * number;
console.log(calculateSquare(7)); // 49
```

**Breakdown:** `const` declares the binding; `number` is the one parameter; `=>` introduces the expression body; `number * number` multiplies the input by itself and is implicitly returned. **Trace:** input 7 -> `7 * 7` -> 49. **Complexity:** O(1) time, O(1) auxiliary space.

### Solution 2: Discounted product price

```javascript
const calculatePrice = (price, quantity, discountRate = 0) => {
  const subtotal = price * quantity;
  const discount = subtotal * discountRate;
  return subtotal - discount;
};
console.log(calculatePrice(500, 4, 0.10)); // 1800
```

**Piece-by-piece:** `discountRate = 0` supplies a default. `price * quantity` computes gross cost. `subtotal * discountRate` computes the discount amount. `subtotal - discount` gives the amount payable. **Trace:** 500 * 4 = 2000; 2000 * 0.10 = 200; 2000 - 200 = 1800. **Complexity:** O(1) time, O(1) space.

### Solution 3: Passing students

```javascript
const students = [
  { name: "Alice", score: 85 },
  { name: "Bob", score: 42 },
  { name: "Carol", score: 73 },
  { name: "David", score: 35 }
];
const passed = students.filter(student => student.score >= 50);
console.log(passed.map(student => student.name)); // [ 'Alice', 'Carol' ]
```

**Breakdown:** `filter` visits each student. `student.score` reads the score property. `>=` means greater than or equal to. The callback returns true for 85 and 73, false for 42 and 35. `map` then extracts each surviving object's `name` property. **Trace:** Alice true -> keep; Bob false -> skip; Carol true -> keep; David false -> skip. **Complexity:** O(n) time and O(n) output space in the worst case.

### Solution 4: Price increase

```javascript
const prices = [100, 200, 300];
const increased = prices.map(price => price * 1.10);
console.log(increased); // [110, 220, 330]
```

**Breakdown:** `map` constructs a new array. `price` is the current value. `1.10` represents 110% of the original price. **Trace:** 100 * 1.10 = 110; 200 * 1.10 = 220; 300 * 1.10 = 330. **Complexity:** O(n) time and O(n) output space. For currency calculations in production, consider integer minor units or decimal-safe handling.

### Solution 5: Total expenses

```javascript
const expenses = [1200, 450, 800, 250];
const total = expenses.reduce((sum, expense) => sum + expense, 0);
console.log(total); // 2700
```

**Breakdown:** `sum` is the accumulator; `expense` is the current element; `sum + expense` becomes the next accumulator; `0` initializes it. **Trace:** 0 + 1200 = 1200; +450 = 1650; +800 = 2450; +250 = 2700. **Complexity:** O(n) time and O(1) auxiliary space.

### Solution 6: Reusable calculator

```javascript
const calculate = (a, b, operation) => operation(a, b);
const add = (a, b) => a + b;
const subtract = (a, b) => a - b;
const multiply = (a, b) => a * b;
const divide = (a, b) => {
  if (b === 0) throw new Error("Cannot divide by zero");
  return a / b;
};

console.log(calculate(20, 5, add));      // 25
console.log(calculate(20, 5, subtract)); // 15
console.log(calculate(20, 5, multiply)); // 100
console.log(calculate(20, 5, divide));   // 4
```

**Breakdown:** `operation` is a parameter holding a function reference. `operation(a, b)` calls that function. `throw new Error(...)` raises an error if the divisor is zero. `===` is strict equality; `/` divides. **Trace for division:** a=20, b=5, operation=divide -> `divide(20, 5)` -> 5 === 0 is false -> return 20/5 = 4. **Complexity:** O(1) for these operations.

### Solution 7: Closure counter

```javascript
function createCounter() {
  let count = 0;
  return () => {
    count += 1;
    return count;
  };
}
const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```

**Breakdown:** `count` is created when `createCounter()` executes. `return () => { ... }` returns an arrow function, not its result. The arrow retains access to `count`. `count += 1` means `count = count + 1`. **Trace:** outer call sets 0; first inner call sets 1; second sets 2; third sets 3. **Complexity:** O(1) time per counter call and O(1) retained state.

### Solution 8: Average with rest

```javascript
function average(...numbers) {
  if (numbers.length === 0) return null;
  const total = numbers.reduce((sum, number) => sum + number, 0);
  return total / numbers.length;
}
console.log(average(10, 20, 30, 40)); // 25
console.log(average()); // null
```

**Breakdown:** `...numbers` collects arguments into an array. `.length` is its element count. `=== 0` checks whether it is empty. We choose `null` to represent no average rather than dividing by zero. `reduce` adds the values. `/ numbers.length` divides the sum by the count. **Trace:** [10,20,30,40], total 100, length 4, 100/4 = 25. **Complexity:** O(n) time and O(n) space for the rest-parameter array.

### Solution 9: Recursive sum

```javascript
function sumToN(n) {
  if (!Number.isInteger(n) || n < 0) {
    throw new Error("n must be a nonnegative integer");
  }
  if (n === 0) return 0;
  return n + sumToN(n - 1);
}
console.log(sumToN(5)); // 15
```

**Breakdown:** `Number.isInteger(n)` checks integer type; `!` negates its boolean result; `||` means OR; `n < 0` rejects negative values; `n === 0` is the base case; `n - 1` makes the next recursive input smaller. **Trace:** `sumToN(5) = 5 + sumToN(4)`; then 4+3+2+1+0; return 15. **Complexity:** O(n) time and O(n) call-stack space. Very large `n` can exceed the call stack.

### Solution 10: Sales report

```javascript
const sales = [
  { product: "Laptop", price: 50000, quantity: 2 },
  { product: "Mouse", price: 1500, quantity: 5 },
  { product: "Keyboard", price: 3500, quantity: 3 },
  { product: "Monitor", price: 20000, quantity: 2 }
];

const lineRevenue = sale => sale.price * sale.quantity;
const totalRevenue = sales.reduce((sum, sale) => sum + lineRevenue(sale), 0);
const totalUnits = sales.reduce((sum, sale) => sum + sale.quantity, 0);
const averagePerUnit = totalUnits === 0 ? null : totalRevenue / totalUnits;
const highestRevenueProduct = sales.reduce((best, sale) => {
  if (best === null || lineRevenue(sale) > lineRevenue(best)) return sale;
  return best;
}, null);
const above10000 = sales
  .filter(sale => lineRevenue(sale) > 10000)
  .map(sale => sale.product);

console.log("Total revenue:", totalRevenue); // 158000
console.log("Total units:", totalUnits); // 12
console.log("Average per unit:", averagePerUnit.toFixed(2)); // 13166.67
console.log("Highest-revenue product:", highestRevenueProduct?.product ?? null); // Laptop
console.log("Products above 10000:", above10000); // [ 'Laptop', 'Keyboard', 'Monitor' ]
```

#### Explain the smallest pieces

| Code | Meaning |
| --- | --- |
| `sale => sale.price * sale.quantity` | Arrow function returning one sale line's revenue. |
| `sum + lineRevenue(sale)` | Add this line's revenue to the running sum. |
| `sum + sale.quantity` | Add this line's unit count to the running unit total. |
| `totalUnits === 0` | Check whether division would use a zero denominator. |
| `? null : totalRevenue / totalUnits` | Ternary: return `null` if there are zero units; otherwise divide. |
| `best === null` | True before a best sale has been selected. |
| `||` | Logical OR; if the left side is true, the right side is not evaluated. |
| `lineRevenue(sale) > lineRevenue(best)` | Compare current line revenue to the best so far. |
| `return sale` | Replace the current best with this sale. |
| `return best` | Keep the previously selected best sale. |
| `.filter(...)` | Retain sales whose line revenue exceeds 10000. |
| `.map(sale => sale.product)` | Convert retained sale objects to product names. |
| `?.product` | Optional chaining: if the left value is nullish, evaluate to `undefined` rather than throw. |
| `?? null` | Nullish coalescing: use `null` if the left expression is null or undefined. |
| `.toFixed(2)` | Format a number to two decimal places; returns a string. |

#### Manual revenue trace

| Product | Price | Quantity | Line revenue | Cumulative revenue | Cumulative units |
| --- | ---: | ---: | ---: | ---: | ---: |
| Laptop | 50000 | 2 | 100000 | 100000 | 2 |
| Mouse | 1500 | 5 | 7500 | 107500 | 7 |
| Keyboard | 3500 | 3 | 10500 | 118000 | 10 |
| Monitor | 20000 | 2 | 40000 | 158000 | 12 |

**Average:** 158000 / 12 = 13166.666... -> `toFixed(2)` gives `"13166.67"`. **Highest revenue:** Laptop 100000 beats Mouse 7500, Keyboard 10500, and Monitor 40000. **Filter:** 100000 > 10000 true; 7500 > 10000 false; 10500 > 10000 true; 40000 > 10000 true. **Complexity:** O(n) time across several passes, O(n) space for the filtered names in the worst case.

## 20. Lesson Summary and Navigation

| Concept | Remember |
| --- | --- |
| Arrow function | `=>` defines an arrow function. |
| Implicit return | An expression body returns automatically. |
| Explicit return | A block body needs `return` to return a value. |
| Callback | A function passed to another function. |
| Higher-order function | Accepts or returns functions. |
| Closure | A function retaining access to its lexical environment. |
| Rest | `...` collects remaining arguments into an array. |
| Spread | `...` expands values in supported contexts. |
| Recursion | A function calls itself; use a base case. |
| `this` | Arrow functions capture lexical `this`. |

**Review checklist:** Can you explain `=>`, `return`, `...`, `+=`, `++`, `===`, `||`, `? :`, `.map`, `.filter`, `.reduce`, and the role of a callback? Can you trace the accumulator and explain every change in its value?

**Previous:** [Lesson 9 - Functions and Function Expressions](09-functions-and-function-expressions.md)  
**Current:** Lesson 10 - Arrow Functions and Advanced Function Concepts  
**Next:** [Lesson 11 - JavaScript Arrays and Array Methods](11-javascript-arrays-and-array-methods.md)
