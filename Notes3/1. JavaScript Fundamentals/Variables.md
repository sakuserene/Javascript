# Unit 1 â€” Lesson 1: JavaScript Fundamentals

**Course:** JavaScript â€” Beginner to Advanced  
**Lesson:** Unit 1, Lesson 1  
**Topic:** Variables, Values, Data Types, Scope, and References  
**Level:** Basic knowledge â†’ Strong fundamentals  
**Teaching approach:** Deep learning, line-by-line explanations, code tracing, exercises, and assessments

> This lesson uses general-purpose examples. Work through the questions before opening the model solutions.

## Table of Contents

- [Learning objectives](#learning-objectives)
- [1. What is a variable?](#1-what-is-a-variable)
  - [1.1 Declaring a variable](#11-declaring-a-variable)
  - [1.2 Initializing a variable](#12-initializing-a-variable)
  - [1.3 Reassigning a variable](#13-reassigning-a-variable)
- [2. Understanding `let`, `const`, and `var`](#2-understanding-let-const-and-var)
  - [2.1 `let`](#21-the-let-keyword)
  - [2.2 `const`](#22-the-const-keyword)
  - [2.3 `var`](#23-the-var-keyword)
  - [2.4 Comparison](#24-comparison-table)
- [3. Variable naming rules](#3-variable-naming-rules)
- [4. JavaScript data types](#4-javascript-data-types)
  - [4.1 String](#41-string)
  - [4.2 Number](#42-number)
  - [4.3 Boolean](#43-boolean)
  - [4.4 Undefined](#44-undefined)
  - [4.5 Null](#45-null)
  - [4.6 BigInt](#46-bigint)
  - [4.7 Symbol](#47-symbol)
  - [4.8 Object](#48-object)
- [5. Understanding `typeof`](#5-understanding-typeof)
- [6. Understanding variable scope](#6-understanding-variable-scope)
- [7. Hoisting and the temporal dead zone](#7-hoisting-and-the-temporal-dead-zone)
- [8. Primitive values versus object references](#8-primitive-values-versus-object-references)
- [9. Mutation versus reassignment](#9-mutation-versus-reassignment)
- [10. Knowledge assessment](#10-knowledge-assessment)
- [11. Practical coding exercises](#11-practical-coding-exercises)
- [12. Lesson summary](#12-lesson-summary)
- [13. Knowledge assessment answers and explanations](#13-knowledge-assessment-answers-and-explanations)
- [14. Practical exercise model solutions](#14-practical-exercise-model-solutions)
- [Lesson navigation](#lesson-navigation)

---

## Learning objectives

By the end of this lesson, you should understand:

1. What variables and values are.
2. The differences between `let`, `const`, and `var`.
3. Variable declaration, initialization, and reassignment.
4. Variable naming rules and conventions.
5. JavaScript's primitive and object data types.
6. The difference between `undefined` and `null`.
7. How `typeof` works.
8. Block scope, function scope, and global scope.
9. Hoisting and the temporal dead zone.
10. Primitive copying versus shared object references.
11. The difference between reassignment and mutation.
12. How to predict JavaScript code execution.

---

## 1. What is a variable?

A **variable** is a named binding that allows a program to access a value.

```javascript
let age = 25;
```

| Component | Name | Explanation |
|---|---|---|
| `let` | Keyword | Declares a variable |
| `age` | Identifier | Name used to access the variable |
| `=` | Assignment operator | Assigns the value on the right |
| `25` | Value | A number |
| `;` | Semicolon | Terminates the statement |

The assignment operator `=` is different from the equality operators `==` and `===`, which we will study in Lesson 2.

### 1.1 Declaring a variable

```javascript
let username;
```

This creates a variable called `username` without explicitly assigning it a value. Its initial value is `undefined`.

```javascript
let username;
console.log(username);
```

**Output:**

```text
undefined
```

### 1.2 Initializing a variable

Initialization means giving a variable its first value.

```javascript
let username = "Alice";
console.log(username);
```

**Output:**

```text
Alice
```

Here, `username` is declared and initialized in the same statement.

### 1.3 Reassigning a variable

Reassignment means changing the value associated with an existing variable.

```javascript
let score = 10;
score = 20;
console.log(score);
```

**Output:**

```text
20
```

**Line-by-line execution:**

1. `let score = 10;` creates the `score` binding and initializes it with `10`.
2. `score = 20;` updates the existing binding to hold `20`.
3. `console.log(score);` reads the current value and prints `20`.

**Visual representation:**

```text
Initially:          After reassignment:
score â”€â”€> 10        score â”€â”€> 20
```

The existing variable is reassigned. A second variable is not created.

---

## 2. Understanding `let`, `const`, and `var`

JavaScript provides three keywords for declaring variables.

### 2.1 The `let` keyword

Use `let` when a variable may need to be reassigned.

```javascript
let price = 100;
price = 150;
console.log(price);
```

**Output:**

```text
150
```

You can also declare a `let` variable without immediately initializing it.

```javascript
let message;
message = "Hello World";
console.log(message);
```

**Output:**

```text
Hello World
```

However, you cannot redeclare the same `let` variable in the same scope.

```javascript
let age = 20;
let age = 30; // SyntaxError
```

**Key distinction:** Reassignment is allowed; redeclaration in the same scope is not.

### 2.2 The `const` keyword

Use `const` when the variable binding should not be reassigned.

```javascript
const birthYear = 2000;
console.log(birthYear);
```

**Output:**

```text
2000
```

Attempting reassignment:

```javascript
const birthYear = 2000;
birthYear = 2001; // TypeError
```

Unlike `let`, a `const` declaration must be initialized immediately.

```javascript
const username; // SyntaxError
```

**Important: `const` does not make objects immutable.**

```javascript
const person = {
  name: "Alice",
  age: 25
};

person.age = 26;
console.log(person);
```

**Output (object contents):**

```javascript
{ name: "Alice", age: 26 }
```

This works because `person.age = 26` modifies a property of the existing object. It does not reassign the variable `person`.

But this is prohibited:

```javascript
const person = { name: "Alice" };
person = { name: "Bob" }; // TypeError
```

The second assignment attempts to replace the value associated with `person`. These two operations are called **mutation** and **reassignment**.

### 2.3 The `var` keyword

`var` is the older way to declare variables.

```javascript
var language = "JavaScript";
language = "Python";
console.log(language);
```

**Output:**

```text
Python
```

Unlike `let` and `const`, `var` permits redeclaration in the same scope.

```javascript
var count = 10;
var count = 20;
console.log(count);
```

**Output:**

```text
20
```

Although `var` remains valid, modern JavaScript generally uses `const` and `let` because their scope rules are easier to reason about.

### 2.4 Comparison table

| Feature | `let` | `const` | `var` |
|---|---|---|---|
| Can reassign | Yes | No | Yes |
| Can redeclare in same scope | No | No | Yes |
| Must initialize immediately | No | Yes | No |
| Block scoped | Yes | Yes | No |
| Function scoped when declared in a function | Yes | Yes | Yes |
| Hoisted | Yes | Yes | Yes |
| Temporal dead zone | Yes | Yes | No |

**Recommended practice:** Start with `const`. Change to `let` when you need reassignment. Avoid `var` in new code unless its specific behavior is required.

---

## 3. Variable naming rules

JavaScript variable names must follow certain rules.

**Valid variable names:**

```javascript
let username = "Alice";
let userAge = 25;
let _count = 10;
let $price = 100;
let firstName2 = "Bob";
```

**Invalid variable names (examples; do not execute):**

```javascript
let 2name = "Alice";     // Cannot start with a digit
let user-name = "Bob";   // Hyphen is an operator
let first name = "Sam";  // Spaces are not allowed
let const = 10;          // Reserved keyword
```

JavaScript is case-sensitive.

```javascript
let name = "Alice";
let Name = "Bob";
console.log(name);
console.log(Name);
```

**Output:**

```text
Alice
Bob
```

`name` and `Name` are different identifiers.

**Naming conventions:** Use descriptive camelCase names for ordinary variables.

```javascript
let firstName = "Alice";
let totalPrice = 500;
let isLoggedIn = true;
let numberOfStudents = 30;
```

Avoid vague names when a clearer one is possible:

```javascript
let x = 500;
let a = true;
let temp1 = 30;
```

Short names are acceptable in small, obvious contexts, but descriptive names usually make code easier to maintain.

---

## 4. JavaScript data types

A data type describes the kind of value being used. JavaScript has seven primitive types and an object type.

```text
JavaScript Values
|-- Primitive Types
|   |-- String
|   |-- Number
|   |-- Boolean
|   |-- Undefined
|   |-- Null
|   |-- BigInt
|   \-- Symbol
\-- Object Type
    |-- Plain objects
    |-- Arrays
    |-- Functions (callable objects)
    |-- Dates
    |-- Maps
    \-- Sets
```

**Output:**

```text
Hello Alice
```

Strings can be created using double quotes, single quotes, or backticks. Backticks support template literals, including interpolation using `${...}`.

### 4.2 Number

The `number` type represents ordinary numeric values.

```javascript
let age = 25;
let price = 99.99;
let temperature = -10;
console.log(typeof age);
console.log(typeof price);
```

**Output:**

```text
number
number
```

Both integers and decimal numbers normally use the `number` type. JavaScript also has special numeric values such as `NaN`, `Infinity`, and `-Infinity`.

```javascript
console.log(typeof NaN);
```

**Output:**

```text
number
```

Although `NaN` means "Not a Number," its JavaScript type is `number`.

### 4.3 Boolean

Booleans represent logical values.

```javascript
let isActive = true;
let isComplete = false;
console.log(isActive);
console.log(typeof isComplete);
```

**Output:**

```text
true
boolean
```

Booleans are essential for conditions and decision-making.

### 4.4 Undefined

`undefined` is the value automatically assigned to many declared but uninitialized variables.

```javascript
let username;
console.log(username);
console.log(typeof username);
```

**Output:**

```text
undefined
undefined
```

It can also occur when reading a missing object property.

```javascript
const user = { name: "Alice" };
console.log(user.age);
```

**Output:**

```text
undefined
```

### 4.5 Null

`null` represents an intentional absence of a value.

```javascript
let selectedItem = null;
console.log(selectedItem);
```

**Output:**

```text
null
```

**`undefined` versus `null`:**

| `undefined` | `null` |
|---|---|
| Often assigned automatically | Usually assigned intentionally |
| Represents an undefined value | Represents an intentional absence |
| `typeof` returns `"undefined"` | `typeof` returns `"object"` |

```javascript
let firstValue;
let secondValue = null;
console.log(firstValue);
console.log(secondValue);
```

**Output:**

```text
undefined
null
```

**Important:** `typeof null` returning `"object"` is a historical JavaScript behavior. `null` is still a primitive.

### 4.6 BigInt

`BigInt` represents integer values that may exceed the safe integer range of the ordinary `number` type.

```javascript
const bigNumber = 9007199254740993n;
console.log(typeof bigNumber);
```

**Output:**

```text
bigint
```

The `n` suffix identifies a BigInt literal.

### 4.7 Symbol

Symbols are unique primitive values.

```javascript
const id1 = Symbol("id");
const id2 = Symbol("id");
console.log(id1 === id2);
```

**Output:**

```text
false
```

Although both symbols have the description `"id"`, they are distinct values.

### 4.8 Object

An object stores properties as key-value pairs.

```javascript
const student = {
  name: "Alice",
  age: 22,
  isEnrolled: true
};

console.log(student.name);
console.log(student.age);
```

**Output:**

```text
Alice
22
```

Arrays are also objects:

```javascript
const numbers = [10, 20, 30];
console.log(typeof numbers);
```

**Output:**

```text
object
```

Functions are callable objects, but `typeof` returns `"function"` for them.

---

## 5. Understanding `typeof`

The `typeof` operator returns a string identifying the type of its operand.

```javascript
console.log(typeof "Hello");
console.log(typeof 100);
console.log(typeof true);
console.log(typeof undefined);
console.log(typeof null);
console.log(typeof {});
console.log(typeof []);
console.log(typeof function() {});
```

| Expression | Output |
|---|---|
| `typeof "Hello"` | `"string"` |
| `typeof 100` | `"number"` |
| `typeof true` | `"boolean"` |
| `typeof undefined` | `"undefined"` |
| `typeof null` | `"object"` |
| `typeof {}` | `"object"` |
| `typeof []` | `"object"` |
| `typeof function(){}` | `"function"` |

**How to check whether a value is an array:** Since `typeof []` returns `"object"`, use `Array.isArray()`.

```javascript
const numbers = [10, 20, 30];
console.log(Array.isArray(numbers));
```

**Output:**

```text
true
```

Compare:

```javascript
const person = { name: "Alice" };
console.log(Array.isArray(person));
```

**Output:**

```text
false
```

---

## 6. Understanding variable scope

Scope determines where a variable can be accessed. We will examine three common forms of scope.

### 6.1 Global scope

A variable declared at the top level of a script is generally accessible throughout that script, subject to JavaScript's module and environment rules.

```javascript
let message = "Hello";

function greet() {
  console.log(message);
}

greet();
```

**Output:**

```text
Hello
```

The function can access `message` because it is declared in an outer scope.

### 6.2 Function scope

Variables declared inside a function are not accessible outside that function.

```javascript
function greet() {
  let message = "Hello";
  console.log(message);
}

greet();
console.log(message);
```

The call to `greet()` prints `Hello`. The final `console.log(message)` throws a `ReferenceError` because the function's local `message` is not available outside the function.

### 6.3 Block scope

A block is a section of code enclosed in `{}`.

```javascript
{
  let age = 25;
  const name = "Alice";
  console.log(age);
  console.log(name);
}
```

**Output:**

```text
25
Alice
```

But:

```javascript
{
  let age = 25;
}
console.log(age); // ReferenceError
```

**Comparing `let` and `var` inside blocks:**

```javascript
{
  let a = 10;
  var b = 20;
}

console.log(b);
console.log(a);
```

The first log prints `20`, assuming the code is executed at the top level. The second throws a `ReferenceError`. `var` is not block-scoped, while `let` is.

---

## 7. Hoisting and the temporal dead zone

Hoisting describes how JavaScript processes declarations before executing the statements in their scope. Different declarations behave differently.

### 7.1 Hoisting with `var`

```javascript
console.log(score);
var score = 100;
```

**Output:**

```text
undefined
```

The `var` binding exists before its declaration statement executes and is initialized to `undefined`.

A useful simplified mental model is:

```javascript
var score;
console.log(score);
score = 100;
```

JavaScript does not literally rewrite your source code this way, but the model helps explain the behavior.

### 7.2 Hoisting with `let`

```javascript
console.log(score);
let score = 100;
```

This throws a `ReferenceError`. The `score` binding exists in its scope but cannot be accessed before initialization. This period is called the **temporal dead zone (TDZ)**.

### 7.3 Hoisting with `const`

```javascript
console.log(price);
const price = 100;
```

This also throws a `ReferenceError` because the binding has not yet been initialized.

| Declaration | Access before declaration executes |
|---|---|
| `var` | Returns `undefined` |
| `let` | `ReferenceError` |
| `const` | `ReferenceError` |

---

## 8. Primitive values versus object references

This concept is fundamental when learning arrays, objects, functions, and callbacks.

### 8.1 Copying primitive values

```javascript
let a = 10;
let b = a;
b = 50;
console.log(a);
console.log(b);
```

**Output:**

```text
10
50
```

**Step-by-step explanation:**

1. `a` is initialized with `10`.
2. `b` receives a copy of the primitive value in `a`.
3. `b` is reassigned to `50`.
4. `a` still holds `10`.

```text
a â”€â”€> 10
b â”€â”€> 50
```

Each variable contains its own primitive value.

### 8.2 Copying object references

```javascript
let person1 = { name: "Alice" };
let person2 = person1;
person2.name = "Bob";
console.log(person1.name);
console.log(person2.name);
```

**Output:**

```text
Bob
Bob
```

**Step-by-step explanation:**

1. An object is created with `name: "Alice"`.
2. `person1` refers to that object.
3. `person2 = person1` copies the reference to the same object.
4. `person2.name = "Bob"` modifies the shared object.
5. Both variables observe the updated property.

```text
person1 â”€â”€â”
          â”œâ”€â”€> { name: "Bob" }
person2 â”€â”€â”˜
```

### 8.3 Comparing object references

```javascript
const a = { value: 10 };
const b = { value: 10 };
console.log(a === b);
```

**Output:**

```text
false
```

Even though the objects have identical properties, they are separate objects.

Now compare:

```javascript
const a = { value: 10 };
const b = a;
console.log(a === b);
```

**Output:**

```text
true
```

Both variables refer to the same object. This is why object equality works differently from primitive equality.

---

## 9. Mutation versus reassignment

### Reassignment

```javascript
let numbers = [10, 20];
numbers = [30, 40];
console.log(numbers);
```

**Output:**

```text
[30, 40]
```

The variable `numbers` now refers to a different array.

### Mutation

```javascript
const numbers = [10, 20];
numbers.push(30);
console.log(numbers);
```

**Output:**

```text
[10, 20, 30]
```

The original array was modified.

### Why `const` permits mutation

```javascript
const numbers = [10, 20];
numbers[0] = 100;
console.log(numbers);
```

**Output:**

```text
[100, 20]
```

The `numbers` binding remains associated with the same array.

But:

```javascript
const numbers = [10, 20];
numbers = [100, 200]; // TypeError
```

This throws a `TypeError` because the variable cannot be reassigned.

---

## 10. Knowledge assessment

**Instructions:** Predict the result without running the code. The answers are in [Section 13](#13-knowledge-assessment-answers-and-explanations).

### Question 1: What is the output?

```javascript
let x = 10;
let y = x;
y = 30;
console.log(x);
```

- A. `10`
- B. `30`
- C. `undefined`
- D. `ReferenceError`

### Question 2: What is the output?

```javascript
const user = { name: 'Alice' };
user.name = 'Bob';
console.log(user.name);
```

- A. `Alice`
- B. `Bob`
- C. `TypeError`
- D. `undefined`

### Question 3: What does this expression return?

```javascript
typeof null
```

- A. `null`
- B. `undefined`
- C. `object`
- D. `boolean`

### Question 4: What is the output?

```javascript
let a = [1, 2];
let b = a;
b.push(3);
console.log(a);
```

- A. `[1, 2]`
- B. `[1, 2, 3]`
- C. `[3]`
- D. `TypeError`

### Question 5: What happens?

```javascript
{
  let message = 'Hello';
}
console.log(message);
```

- A. `Hello`
- B. `undefined`
- C. `ReferenceError`
- D. `null`

### Question 6: What is printed?

```javascript
console.log(value);
var value = 50;
```

- A. `50`
- B. `undefined`
- C. `ReferenceError`
- D. `null`

### Question 7: What is the result?

```javascript
const a = { x: 1 };
const b = { x: 1 };
console.log(a === b);
```

- A. `true`
- B. `false`
- C. `undefined`
- D. `SyntaxError`

### Question 8: Which statement is correct?

```javascript
const items = [10, 20];
```

- A. `items.push(30)` is prohibited.
- B. `items[0] = 50` is prohibited.
- C. `items = [30, 40]` is prohibited.
- D. All operations are prohibited.

---

## 11. Practical coding exercises

**Instructions:** Write your own code and explain the output before checking the [model solutions](#14-practical-exercise-model-solutions).

### Exercise 1 â€” Variables and reassignment

Create a variable called `balance` with an initial value of `1000`. Subtract `250` from it and print the new balance.

**Expected output:**

```text
750
```

### Exercise 2 â€” Objects and mutation

Create a `const` object called `student` with these properties:

```javascript
name: "Alice"
age: 20
course: "JavaScript"
```

Change the student's age to `21` and print the entire object.

### Exercise 3 â€” Primitive versus reference values

Predict the output of this code:

```javascript
let x = 5;
let y = x;
y = 15;

const a = { value: 5 };
const b = a;
b.value = 15;

console.log(x);
console.log(y);
console.log(a.value);
console.log(b.value);
```

Explain why changing `y` does not affect `x`, while changing `b.value` affects `a.value`.

### Exercise 4 â€” Scope

Study the following code:

```javascript
let x = 10;

function test() {
  let x = 20;

  if (true) {
    let x = 30;
    console.log(x);
  }

  console.log(x);
}

test();
console.log(x);
```

Predict the three outputs and explain which `x` each `console.log()` accesses.

### Exercise 5 â€” Debugging

Identify the errors in the following code and rewrite it correctly. The intended behavior is to print the user's updated name and age.

```javascript
const userName = "Alice";
userName = "Bob";

let age = 20;
let age = 21;

console.log(userName);
console.log(age);
```

---

## 12. Lesson summary

| Concept | Key takeaway |
|---|---|
| Variable | A named binding for accessing a value |
| `let` | Allows reassignment |
| `const` | Prevents reassignment |
| `var` | Older declaration with function-scoping behavior |
| Primitive | Values such as strings, numbers, and booleans |
| Object | A value with properties and reference identity |
| `typeof` | Reports a value's type |
| Scope | Determines where bindings are accessible |
| Hoisting | Declaration processing before statement execution |
| TDZ | Period before a lexical binding is initialized |
| Mutation | Modifying an existing object |
| Reassignment | Changing the value associated with a variable |

Complete the knowledge assessment and practical exercises before proceeding. The next lesson is **Unit 1, Lesson 2 â€” JavaScript Operators and Expressions**.

---

## 13. Knowledge assessment answers and explanations

> **Model answers:** These are reference solutions, not a record of a learner's submitted answers.

| Question | Correct option | Result |
|---|---|---|
| 1 | A | `10` |
| 2 | B | `Bob` |
| 3 | C | `object` |
| 4 | B | `[1, 2, 3]` |
| 5 | C | `ReferenceError` |
| 6 | B | `undefined` |
| 7 | B | `false` |
| 8 | C | Reassigning `items` is prohibited |

### Question 1 explanation

The primitive number `10` is copied into `y`. Reassigning `y` to `30` does not change `x`, so `console.log(x)` prints `10`.

### Question 2 explanation

`const` prevents rebinding `user`, but it does not freeze the object. Assigning `user.name = 'Bob'` changes a property, so the result is `Bob`.

### Question 3 explanation

`typeof null` returns the string `"object"`. This is a historical quirk; `null` is actually a primitive.

### Question 4 explanation

`a` and `b` refer to the same array. `b.push(3)` mutates the shared array, so printing `a` shows `[1, 2, 3]`.

### Question 5 explanation

`let message` is declared inside a block. It is not available outside the block, so accessing it there throws a `ReferenceError`.

### Question 6 explanation

`var` declarations are hoisted and their bindings are initialized to `undefined` before the assignment executes. The first log therefore prints `undefined`.

### Question 7 explanation

The two object literals create two distinct objects. Strict equality compares object identity, so `a === b` is `false`.

### Question 8 explanation

A `const` binding cannot be reassigned to a different array. However, mutating the existing array with `push()` or an index assignment is allowed.

---

## 14. Practical exercise model solutions

> **Model solutions:** These are examples of correct answers. They have not yet been compared with your own submitted solutions.

### Exercise 1 â€” Model solution

```javascript
let balance = 1000;
balance = balance - 250;
console.log(balance);
```

**Output:**

```text
750
```

**Line-by-line explanation:**

1. `let balance = 1000;` declares and initializes a reassignable variable.
2. `balance = balance - 250;` reads `1000`, subtracts `250`, and assigns the result `750` back to `balance`.
3. `console.log(balance);` prints `750`.

**Alternative solution:**

```javascript
let balance = 1000;
balance -= 250;
console.log(balance);
```

`-=` is a compound assignment operator; it performs the subtraction and stores the result.

### Exercise 2 â€” Model solution

```javascript
const student = {
  name: "Alice",
  age: 20,
  course: "JavaScript"
};

student.age = 21;
console.log(student);
```

**Output (object contents):**

```javascript
{
  name: "Alice",
  age: 21,
  course: "JavaScript"
}
```

**Line-by-line explanation:**

1. `const student = {...}` declares a constant binding and creates an object with three properties.
2. `student.age = 21;` updates the `age` property of the existing object.
3. `console.log(student);` displays the updated object.

**Why this is valid:** The `student` variable is not reassigned. The object it refers to is mutated. A `const` binding does not automatically freeze an object.

### Exercise 3 â€” Model solution

```text
5
15
15
15
```

**Explanation, statement by statement:**

1. `let x = 5;` stores the primitive number `5` in `x`.
2. `let y = x;` copies that number into `y`.
3. `y = 15;` reassigns `y`; `x` remains `5`.
4. `const a = { value: 5 };` creates an object and stores a reference in `a`.
5. `const b = a;` copies the reference, so `a` and `b` refer to the same object.
6. `b.value = 15;` mutates the shared object's `value` property.
7. The logs print `x = 5`, `y = 15`, `a.value = 15`, and `b.value = 15`.

**Visual comparison:**

```text
Primitive values:
x â”€â”€> 5
y â”€â”€> 15

Shared object:
a â”€â”€â”
    â”œâ”€â”€> { value: 15 }
b â”€â”€â”˜
```

### Exercise 4 â€” Model solution

**Output:**

```text
30
20
10
```

**Explanation:**

1. `let x = 10;` creates the outer `x`.
2. When `test()` runs, `let x = 20;` creates a new function-local `x`, shadowing the outer variable.
3. Inside the `if` block, `let x = 30;` creates a third binding, shadowing the function-local `x` within that block.
4. The first `console.log(x)` is inside the `if` block, so it prints `30`.
5. After the block ends, the second `console.log(x)` resolves to the function-local `x` and prints `20`.
6. After `test()` returns, the final `console.log(x)` uses the outer `x` and prints `10`.

**Scope illustration:**

```text
Outer scope:         x = 10
â””â”€â”€ test() scope:    x = 20
    â””â”€â”€ if block:    x = 30
```

This behavior is called **variable shadowing**.

### Exercise 5 â€” Model solution

**Original code (contains errors):**

```javascript
const userName = "Alice";
userName = "Bob";

let age = 20;
let age = 21;

console.log(userName);
console.log(age);
```

**Error 1:** `userName` was declared with `const`, so reassigning it to `"Bob"` is not allowed.

**Error 2:** `age` was declared twice with `let` in the same scope. This is a `SyntaxError` and prevents the script from executing as written.

**Corrected version:**

```javascript
let userName = "Alice";
userName = "Bob";

let age = 20;
age = 21;

console.log(userName);
console.log(age);
```

**Output:**

```text
Bob
21
```

**Line-by-line explanation:**

1. `let userName = "Alice";` creates a binding that can be reassigned.
2. `userName = "Bob";` updates the existing binding.
3. `let age = 20;` declares `age` once.
4. `age = 21;` reassigns the existing variable instead of redeclaring it.
5. The two logs print the updated values.

**Alternative if the initial values are unnecessary:**

```javascript
const userName = "Bob";
const age = 21;
console.log(userName);
console.log(age);
```

This shorter solution is valid if the task only requires final values rather than demonstrating reassignment.

---

## Lesson navigation

| Direction | Topic | Link |
|---|---|---|
| Previous | Course Overview | [README.md](../README.md) |
| Current | Unit 1, Lesson 1 â€” Variables, Values, Data Types, Scope, and References | This file |
| Next | Unit 1, Lesson 2 â€” JavaScript Operators and Expressions | [02-operators-and-expressions.md](02-operators-and-expressions.md) |

> **Navigation note:** The previous and next links will work after the corresponding files are added to the repository. Suggested location for this file: `unit-01-fundamentals/01-variables-and-data-types.md`.
