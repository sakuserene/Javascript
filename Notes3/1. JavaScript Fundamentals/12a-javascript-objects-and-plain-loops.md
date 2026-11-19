# Lesson 12A: JavaScript Objects and Plain Loops

**Course:** JavaScript Master Course  
**Level:** Beginner to Advanced  
**Previous:** [Lesson 12 - Objects and Object Methods](12-javascript-objects-and-object-methods.md)  
**Next:** [Lesson 13 - Object-Oriented Programming](13-object-oriented-programming-in-javascript.md)

> Goal: Master ordinary JavaScript loops with objects, arrays of objects, nested objects, and nested arrays **without** using `map()`, `filter()`, or `reduce()` to solve the problems.

## Table of Contents

1. [Why objects and loops belong together](#1-why-objects-and-loops-belong-together)
2. [The five loop types and which data they work with](#2-the-five-loop-types-and-which-data-they-work-with)
3. [Using for...in on plain objects](#3-using-forin-on-plain-objects)
4. [Using for with Object.keys](#4-using-for-with-objectkeys)
5. [Using while with object keys](#5-using-while-with-object-keys)
6. [Using do...while safely](#6-using-dowhile-safely)
7. [Using for...of with Object.entries](#7-using-forof-with-objectentries)
8. [Arrays of objects: accessing and traversing](#8-arrays-of-objects-accessing-and-traversing)
9. [Filtering objects with plain loops](#9-filtering-objects-with-plain-loops)
10. [Calculating totals and averages](#10-calculating-totals-and-averages)
11. [Finding minimums and maximums](#11-finding-minimums-and-maximums)
12. [Nested loops with arrays inside objects](#12-nested-loops-with-arrays-inside-objects)
13. [Nested loops with objects inside objects](#13-nested-loops-with-objects-inside-objects)
14. [break and continue](#14-break-and-continue)
15. [Object enumeration and tricky edge cases](#15-object-enumeration-and-tricky-edge-cases)
16. [Debugging and performance](#16-debugging-and-performance)
17. [Ten interview questions with answers](#17-ten-interview-questions-with-answers)
18. [Ten coding exercises with complete solutions](#18-ten-coding-exercises-with-complete-solutions)
19. [Mastery checklist and navigation](#19-mastery-checklist-and-navigation)

---

## 1. Why objects and loops belong together

An object groups named values:

```javascript
const student = {
  name: "Alice",
  age: 20,
  score: 85
};

console.log(student.name);  // Alice
console.log(student.age);   // 20
console.log(student.score); // 85
```

Here `const` declares a binding; `student` is its name; `=` assigns the object reference; `{}` encloses the object literal; `name`, `age`, and `score` are property keys; `:` separates a key from its value; and `,` separates properties. The dot in `student.name` accesses the literal property named `name`.

For three properties, individual statements are manageable. For a large object, a loop processes keys systematically. A **plain object is not directly iterable with `for...of`**. We either enumerate its properties with `for...in`, or turn its keys, values, or entries into arrays first.

## 2. The five loop types and which data they work with

| Loop | Can use directly on plain object? | Typical input | How it advances |
| --- | --- | --- | --- |
| `for` | No | `Object.keys(obj)` | Programmer updates index |
| `while` | No | `Object.keys(obj)` | Programmer updates index |
| `do...while` | No | `Object.keys(obj)` | Body first, condition afterward |
| `for...in` | Yes | Object | Enumerates enumerable string keys, including inherited keys |
| `for...of` | No | Array, string, `Object.entries(obj)` | Retrieves iterable values |

`Object.keys(obj)` returns **own enumerable string keys**. `Object.values(obj)` returns their values. `Object.entries(obj)` returns `[key, value]` pairs. Symbol keys are omitted by all three.

## 3. Using for...in on plain objects

```javascript
const student = { name: "Alice", age: 20, score: 85 };

for (const key in student) {
  if (Object.hasOwn(student, key)) {
    console.log(key, student[key]);
  }
}
```

Output:

```text
name Alice
age 20
score 85
```

**Read the syntax one piece at a time:**

- `for` begins the loop.
- `(` and `)` enclose the loop declaration.
- `const key` creates a binding for the current property name in each iteration.
- `in` enumerates enumerable string property names.
- `student` is the object being inspected.
- `{` and `}` enclose the body.
- `Object.hasOwn(student, key)` is `true` only for the object's own property.
- `student[key]` evaluates the **variable** `key`, then accesses the corresponding property.

**Why not `student.key`?** Dot notation looks for a property literally named `key`. If `key` holds `"score"`, bracket notation `student[key]` reads `student["score"]`.

| Iteration | `key` | `student[key]` | Output |
| --- | --- | --- | --- |
| 1 | `"name"` | `"Alice"` | `name Alice` |
| 2 | `"age"` | `20` | `age 20` |
| 3 | `"score"` | `85` | `score 85` |

`for...in` can visit inherited enumerable properties. This is why the own-property check matters when processing records.

## 4. Using for with Object.keys

```javascript
const product = { name: "Laptop", price: 50000, quantity: 3 };
const keys = Object.keys(product);

for (let i = 0; i < keys.length; i++) {
  const key = keys[i];
  const value = product[key];
  console.log(key, value);
}
```

Output:

```text
name Laptop
price 50000
quantity 3
```

`let i = 0` initializes the index. `i < keys.length` means "continue while the index is less than the number of keys." `i++` increments the index by one after the body runs. In this context, `i++` has the same updating effect as `i = i + 1`. The expression `keys[i]` retrieves a string key from the keys array, and `product[key]` retrieves its value from the original object.

| Check | `i` | `keys[i]` | Result |
| --- | ---: | --- | --- |
| `0 < 3` | 0 | `"name"` | `Laptop` |
| `1 < 3` | 1 | `"price"` | `50000` |
| `2 < 3` | 2 | `"quantity"` | `3` |
| `3 < 3` | 3 | Not accessed | Loop exits |

A traditional `for` loop is useful when you need the numeric position of a key or want to control the step size.

## 5. Using while with object keys

```javascript
const product = { name: "Laptop", price: 50000, quantity: 3 };
const keys = Object.keys(product);
let i = 0;

while (i < keys.length) {
  const key = keys[i];
  console.log(key, product[key]);
  i++;
}
```

Output:

```text
name Laptop
price 50000
quantity 3
```

A `while` loop tests its condition **before** each iteration. It runs zero times if the initial condition is false. Unlike `for`, its initialization and increment are separate statements. Forgetting `i++` can cause an infinite loop. The index sequence is `0 -> 1 -> 2 -> 3`, then the condition `3 < 3` is false.

## 6. Using do...while safely

```javascript
const product = { name: "Laptop", price: 50000 };
const keys = Object.keys(product);
let i = 0;

do {
  if (i < keys.length) {
    const key = keys[i];
    console.log(key, product[key]);
  }
  i++;
} while (i < keys.length);
```

Output:

```text
name Laptop
price 50000
```

`do` executes the body **before** the `while` condition is tested. The `if` guard matters for an empty object, whose `Object.keys()` result is `[]`. Without the guard, the first iteration could attempt to process a nonexistent key. Use `do...while` when at least one pass is genuinely required; otherwise, `for` or `while` is usually simpler.

## 7. Using for...of with Object.entries

```javascript
const product = { name: "Laptop", price: 50000, quantity: 3 };

for (const [key, value] of Object.entries(product)) {
  console.log(key, value);
}
```

Output:

```text
name Laptop
price 50000
quantity 3
```

`Object.entries(product)` creates an array such as `[["name", "Laptop"], ["price", 50000], ["quantity", 3]]`. `for...of` visits each inner pair. `[key, value]` is an **array destructuring pattern**: it assigns the pair's first element to `key` and second to `value`.

Equivalent without destructuring:

```javascript
for (const pair of Object.entries(product)) {
  const key = pair[0];
  const value = pair[1];
  console.log(key, value);
}
```

Do not write `for (const item of product)` for an ordinary object: it throws a `TypeError` because the object is not iterable.

## 8. Arrays of objects: accessing and traversing

```javascript
const students = [
  { name: "Alice", score: 85 },
  { name: "Bob", score: 42 },
  { name: "Carol", score: 73 },
  { name: "David", score: 35 }
];

console.log(students[0].name); // Alice

for (let i = 0; i < students.length; i++) {
  console.log(students[i].name, students[i].score);
}
```

Output:

```text
Alice 85
Bob 42
Carol 73
David 35
```

`students[0]` returns the first **object** in the array; `.name` reads its name. In the loop, `students[i]` returns the object at the current index. `students.length` is `4`, so valid indexes are `0`, `1`, `2`, and `3`.

A `for...of` version does not require an index:

```javascript
for (const student of students) {
  console.log(student.name, student.score);
}
```

The variable `student` holds the current object, **not** a property name or array index.

## 9. Filtering objects with plain loops

```javascript
const students = [
  { name: "Alice", score: 85 },
  { name: "Bob", score: 42 },
  { name: "Carol", score: 73 },
  { name: "David", score: 35 }
];

const passedStudents = [];

for (let i = 0; i < students.length; i++) {
  const student = students[i];
  if (student.score >= 50) {
    passedStudents.push(student);
  }
}

console.log(passedStudents);
```

Output:

```text
[
  { name: "Alice", score: 85 },
  { name: "Carol", score: 73 }
]
```

`[]` creates an empty result array. `>=` means "greater than or equal to." `if` executes its body only if the comparison is true. `push(student)` appends the current object to the result. It does **not** clone the object; both arrays then refer to the same student objects.

| Index | Student | Score | `score >= 50` | Result after iteration |
| --- | --- | ---: | --- | --- |
| 0 | Alice | 85 | true | `[Alice]` |
| 1 | Bob | 42 | false | `[Alice]` |
| 2 | Carol | 73 | true | `[Alice, Carol]` |
| 3 | David | 35 | false | `[Alice, Carol]` |

This is the manual logic that `filter()` automates.

## 10. Calculating totals and averages

```javascript
const products = [
  { name: "Laptop", price: 50000, quantity: 2 },
  { name: "Mouse", price: 1500, quantity: 5 },
  { name: "Keyboard", price: 3500, quantity: 3 }
];

let total = 0;
for (let i = 0; i < products.length; i++) {
  const product = products[i];
  const productTotal = product.price * product.quantity;
  total = total + productTotal;
}
console.log(total); // 118000
```

`let total = 0` creates an **accumulator**. `*` multiplies unit price by quantity. `total = total + productTotal` reads the old total, adds the current product value, and stores the new total. `total += productTotal` is shorthand for this update.

| Product | Multiplication | Previous total | New total |
| --- | --- | ---: | ---: |
| Laptop | `50000 * 2 = 100000` | 0 | 100000 |
| Mouse | `1500 * 5 = 7500` | 100000 | 107500 |
| Keyboard | `3500 * 3 = 10500` | 107500 | 118000 |

Average of numeric values with an empty-input guard:

```javascript
function average(numbers) {
  if (numbers.length === 0) return null;
  let total = 0;
  for (const number of numbers) {
    total += number;
  }
  return total / numbers.length;
}
console.log(average([10, 20, 30, 40])); // 25
console.log(average([])); // null
```

`===` compares without type coercion. `/` divides the accumulated total by the number of values. Returning `null` explicitly signals that an average cannot be calculated for an empty list.

## 11. Finding minimums and maximums

```javascript
const students = [
  { name: "Alice", score: 85 },
  { name: "Bob", score: 92 },
  { name: "Carol", score: 73 }
];

let highestStudent = null;
for (const student of students) {
  if (highestStudent === null || student.score > highestStudent.score) {
    highestStudent = student;
  }
}
console.log(highestStudent); // { name: "Bob", score: 92 }
```

`||` means logical OR. JavaScript short-circuits: if `highestStudent === null` is true, the right side is not evaluated, avoiding an invalid access to `null.score`. `>` checks whether the current score exceeds the best score seen so far.

| Current student | Score | Highest before | Highest after |
| --- | ---: | --- | --- |
| Alice | 85 | `null` | Alice |
| Bob | 92 | Alice (85) | Bob |
| Carol | 73 | Bob (92) | Bob |

For an empty array, the result remains `null`. To find the minimum, use `<` instead of `>`.

## 12. Nested loops with arrays inside objects

```javascript
const students = [
  { name: "Alice", scores: [80, 90, 85] },
  { name: "Bob", scores: [60, 75, 70] }
];

for (let i = 0; i < students.length; i++) {
  const student = students[i];
  let total = 0;

  for (let j = 0; j < student.scores.length; j++) {
    total += student.scores[j];
  }

  console.log(student.name, total);
}
```

Output:

```text
Alice 255
Bob 205
```

The **outer** loop selects a student (`i`). The **inner** loop selects a score (`j`). `total` is declared **inside** the outer loop so it resets to `0` for each student.

| `i` | Student | `j` | Score | Current student's total |
| ---: | --- | ---: | ---: | ---: |
| 0 | Alice | 0 | 80 | 80 |
| 0 | Alice | 1 | 90 | 170 |
| 0 | Alice | 2 | 85 | 255 |
| 1 | Bob | 0 | 60 | 60 |
| 1 | Bob | 1 | 75 | 135 |
| 1 | Bob | 2 | 70 | 205 |

If you move `let total = 0` outside the outer loop, Bob's reported total becomes `460`, which is incorrect for his individual scores.

## 13. Nested loops with objects inside objects

```javascript
const company = {
  departments: {
    sales: { Alice: 5000, Bob: 7000 },
    support: { Carol: 4000, David: 6000 }
  }
};

let grandTotal = 0;

for (const departmentName in company.departments) {
  if (!Object.hasOwn(company.departments, departmentName)) continue;
  const department = company.departments[departmentName];

  for (const employeeName in department) {
    if (!Object.hasOwn(department, employeeName)) continue;
    const amount = department[employeeName];
    grandTotal += amount;
  }
}
console.log(grandTotal); // 22000
```

The outer loop obtains each department key, then the inner loop obtains each employee key within that department. `!` negates the Boolean result of `Object.hasOwn()`. `continue` skips inherited properties. The running totals are `5000 -> 12000 -> 16000 -> 22000`.

## 14. break and continue

`break` exits the nearest enclosing loop immediately:

```javascript
const products = [
  { id: 1, name: "Laptop" },
  { id: 2, name: "Mouse" },
  { id: 3, name: "Keyboard" }
];
let found = null;
for (const product of products) {
  if (product.id === 2) {
    found = product;
    break;
  }
}
console.log(found); // { id: 2, name: "Mouse" }
```

`continue` skips the remainder of the **current** iteration and moves to the next:

```javascript
const products = [
  { name: "Laptop", available: true },
  { name: "Mouse", available: false },
  { name: "Keyboard", available: true }
];
for (const product of products) {
  if (!product.available) continue;
  console.log(product.name);
}
```

Output:

```text
Laptop
Keyboard
```

For nested loops, `break` and `continue` normally affect only the **innermost** loop containing them. A `return` statement inside a function exits the function, not just its loop.

## 15. Object enumeration and tricky edge cases

### Inherited properties

```javascript
const parent = { inherited: "yes" };
const child = Object.create(parent);
child.own = "yes";

for (const key in child) console.log(key);
// own
// inherited

console.log(Object.keys(child)); // ["own"]
```

### Numeric-looking keys and order

```javascript
const obj = { b: 1, 10: "ten", 2: "two", a: 2 };
console.log(Object.keys(obj)); // ["2", "10", "b", "a"]
```

Integer-index keys are generally enumerated in ascending numeric order before other string keys in insertion order. Do not assume every property follows the exact textual order used in the object literal.

### Symbols

```javascript
const secret = Symbol("secret");
const obj = { visible: 1, [secret]: 2 };
console.log(Object.keys(obj)); // ["visible"]
console.log(Reflect.ownKeys(obj)); // ["visible", Symbol(secret)]
```

`Object.keys()`, `Object.values()`, `Object.entries()`, and `for...in` do not enumerate symbol keys. `Reflect.ownKeys()` includes own string and symbol keys, even non-enumerable ones.

### Empty objects and arrays

```javascript
const empty = {};
for (const key in empty) console.log(key); // No output
console.log(Object.keys(empty).length); // 0
```

### Values that are undefined

```javascript
const record = { result: undefined };
console.log(record.result === undefined); // true
console.log(Object.hasOwn(record, "result")); // true
```

A property may exist even if its value is `undefined`.

### Mutation during enumeration

Avoid adding or deleting properties of an object while enumerating it. Behavior can be surprising, and it is harder to reason about than iterating over a snapshot of keys.

## 16. Debugging and performance

**Common mistakes:**

1. Using `object.key` when `key` is a variable: use `object[key]`.
2. Using `for...of` directly on a plain object: use `Object.entries(object)`.
3. Forgetting `i++` in a `while` loop: can create an infinite loop.
4. Using `do...while` without guarding empty key arrays: the body runs at least once.
5. Placing a per-student accumulator outside the outer loop: totals leak across students.
6. Assuming `for...in` only visits own properties: inherited enumerable properties may appear.
7. Assuming `push(object)` makes a copy: it adds the same object reference.
8. Forgetting empty input in average or maximum calculations.

To trace a loop, print its state:

```javascript
const numbers = [5, 10, 15];
let total = 0;
for (let i = 0; i < numbers.length; i++) {
  console.log("before", { i, current: numbers[i], total });
  total += numbers[i];
  console.log("after", { i, total });
}
```

**Typical complexity:** a single pass over `n` items is O(n) time. A nested loop that compares every item against every other item is typically O(n^2) time. An accumulator uses O(1) extra space; a result array that stores up to `n` items uses O(n) extra space. These are high-level estimates, not guarantees for every JavaScript engine or input.

---

## 17. Ten interview questions with answers

**1. Why can't you use `for...of` directly on a plain object?**  
A plain object does not implement the iterable protocol by default. Convert its own properties to an iterable array with `Object.keys()`, `Object.values()`, or `Object.entries()`.

**2. What does `for...in` enumerate?**  
Enumerable string-keyed properties, including inherited ones. Guard with `Object.hasOwn(object, key)` to process only own properties.

**3. Why is `object[key]` different from `object.key`?**  
Bracket notation evaluates the variable `key`. Dot notation accesses the literal property named `key`.

**4. What is the difference between `for` and `while`?**  
Both can check conditions before each iteration. `for` places initialization, condition, and update together; `while` keeps initialization and update separate.

**5. When does `do...while` evaluate its condition?**  
After running the body, so the body always runs at least once.

**6. What is an accumulator?**  
A variable such as `total` that carries an intermediate result from one iteration to the next.

**7. Why initialize a maximum search with `null`?**  
It represents "no candidate selected" and handles empty input explicitly. The first element becomes the initial candidate.

**8. What does `break` do inside nested loops?**  
It exits the nearest enclosing loop unless a labeled statement is used. It does not automatically exit all loops.

**9. What does `continue` do?**  
It skips the remainder of the current loop iteration and proceeds with the next iteration.

**10. What is the difference between O(n) and O(n^2) loop work?**  
O(n) scales roughly with the number of items; O(n^2) scales roughly with the square of that number, commonly arising from comparing every item with every other item.

---

## 18. Ten coding exercises with complete solutions

Each exercise contains the task, a complete plain-loop solution, a breakdown of the key statements, and an execution trace. Try the task first, then compare your code with the model answer.

### Exercise 1. Print all own object properties

**Task:** Create an object with five properties and print each key and value using `for...in`.

```javascript
const book = {
  title: "JavaScript Basics",
  author: "Alex",
  pages: 250,
  available: true,
  edition: 2
};

for (const key in book) {
  if (Object.hasOwn(book, key)) {
    console.log(key + ":", book[key]);
  }
}
```

Output:

```text
title: JavaScript Basics
author: Alex
pages: 250
available: true
edition: 2
```

**Explanation:** `for...in` assigns the current key string to `key`. `Object.hasOwn()` excludes inherited properties. `key + ":"` builds a display label using string concatenation. `book[key]` looks up the corresponding value. `console.log()` prints both.

**Trace:** `title -> JavaScript Basics`; `author -> Alex`; `pages -> 250`; `available -> true`; `edition -> 2`. The loop ends after the fifth key.

### Exercise 2. Count own enumerable string-keyed properties

**Task:** Count properties using a loop rather than returning `Object.keys(obj).length`.

```javascript
function countProperties(obj) {
  let count = 0;
  for (const key in obj) {
    if (Object.hasOwn(obj, key)) {
      count++;
    }
  }
  return count;
}

console.log(countProperties({ a: 1, b: 2, c: 3 })); // 3
console.log(countProperties({})); // 0
```

**Explanation:** `function countProperties(obj)` declares a function with parameter `obj`. `count = 0` starts the accumulator. Each owned enumerable string property increments `count` using `count++`, equivalent here to `count = count + 1`. `return count` sends the final result back to the caller.

**Trace:** Start `0`; key `a -> 1`; key `b -> 2`; key `c -> 3`; return `3`. For `{}`, the body does not run and `0` is returned.

### Exercise 3. Sum numeric properties

**Task:** Add the values of `{ a: 10, b: 20, c: 30 }` using `for...in`.

```javascript
const values = { a: 10, b: 20, c: 30 };
let total = 0;

for (const key in values) {
  if (!Object.hasOwn(values, key)) continue;
  const value = values[key];
  if (typeof value === "number" && Number.isFinite(value)) {
    total += value;
  }
}
console.log(total); // 60
```

**Explanation:** `!` negates the own-property check. `continue` skips inherited keys. `typeof value === "number"` checks the value's type. `&&` requires both conditions to be true; `Number.isFinite(value)` rejects `NaN` and infinities. `total += value` updates the accumulator.

| Key | Value | Total before | Total after |
| --- | ---: | ---: | ---: |
| a | 10 | 0 | 10 |
| b | 20 | 10 | 30 |
| c | 30 | 30 | 60 |

### Exercise 4. Find the highest-scoring student

**Task:** Use a traditional `for` loop to return the highest-scoring student, or `null` if empty.

```javascript
function findHighest(students) {
  let highest = null;
  for (let i = 0; i < students.length; i++) {
    const student = students[i];
    if (highest === null || student.score > highest.score) {
      highest = student;
    }
  }
  return highest;
}

const students = [
  { name: "Alice", score: 85 },
  { name: "Bob", score: 92 },
  { name: "Carol", score: 73 }
];
console.log(findHighest(students)); // { name: "Bob", score: 92 }
console.log(findHighest([])); // null
```

**Explanation:** `i` starts at zero and advances while `i < students.length`. `students[i]` reads each object. `highest === null` handles the first student. `||` short-circuits, so `highest.score` is only read when `highest` is an object. `>` replaces the candidate only when a strictly higher score is found. Equal scores keep the earlier candidate.

**Trace:** `null -> Alice (85) -> Bob (92) -> Bob (92)`. Final answer: Bob.

### Exercise 5. Filter available products

**Task:** Use `for...of` and `push()` to collect available products.

```javascript
const products = [
  { name: "Laptop", available: true },
  { name: "Mouse", available: false },
  { name: "Keyboard", available: true }
];
const availableProducts = [];

for (const product of products) {
  if (product.available === true) {
    availableProducts.push(product);
  }
}
console.log(availableProducts);
```

Output:

```text
[
  { name: "Laptop", available: true },
  { name: "Keyboard", available: true }
]
```

**Explanation:** `for...of` assigns each array element to `product`. `product.available === true` checks the Boolean flag. `push(product)` appends the original object reference to the result array.

**Trace:** Laptop -> add; Mouse -> skip; Keyboard -> add. Result has two objects. Changes to those objects can be observed through either array because the objects are shared.

### Exercise 6. Calculate inventory value with while

**Task:** Sum `price * quantity` for each product using `while`.

```javascript
function inventoryValue(products) {
  let i = 0;
  let total = 0;
  while (i < products.length) {
    const product = products[i];
    total += product.price * product.quantity;
    i++;
  }
  return total;
}

const products = [
  { name: "Laptop", price: 50000, quantity: 2 },
  { name: "Mouse", price: 1500, quantity: 5 },
  { name: "Keyboard", price: 3500, quantity: 3 }
];
console.log(inventoryValue(products)); // 118000
```

**Explanation:** `i` selects the current product; `total` accumulates money values; `*` multiplies unit price and quantity; `+=` adds the product value; `i++` advances the index so the loop terminates. An empty array returns `0` because the loop never runs.

**Trace:** `0 + 100000 = 100000`; `100000 + 7500 = 107500`; `107500 + 10500 = 118000`; return `118000`.

### Exercise 7. Convert an object into key-value pairs

**Task:** Convert `{ a: 1, b: 2, c: 3 }` into `[["a", 1], ["b", 2], ["c", 3]]` using a loop.

```javascript
function toPairs(obj) {
  const pairs = [];
  for (const key in obj) {
    if (Object.hasOwn(obj, key)) {
      pairs.push([key, obj[key]]);
    }
  }
  return pairs;
}

console.log(toPairs({ a: 1, b: 2, c: 3 }));
// [["a", 1], ["b", 2], ["c", 3]]
```

**Explanation:** `pairs = []` initializes the result. `[key, obj[key]]` creates a new two-element array: its first element is the property name, and its second is the corresponding value. `pairs.push(...)` appends that pair. `return pairs` returns the completed array.

**Trace:** Start `[]`; after `a`: `[["a",1]]`; after `b`: `[["a",1],["b",2]]`; after `c`: `[["a",1],["b",2],["c",3]]`.

### Exercise 8. Calculate student averages with nested loops

**Task:** For each student, calculate the average of their score array. Return `null` for a student with no scores.

```javascript
function studentAverages(students) {
  const results = [];
  for (let i = 0; i < students.length; i++) {
    const student = students[i];
    let total = 0;
    for (let j = 0; j < student.scores.length; j++) {
      total += student.scores[j];
    }
    const average = student.scores.length === 0
      ? null
      : total / student.scores.length;
    results.push({ name: student.name, average: average });
  }
  return results;
}

const students = [
  { name: "Alice", scores: [80, 90, 85] },
  { name: "Bob", scores: [60, 75, 70] },
  { name: "Carol", scores: [] }
];
console.log(studentAverages(students));
```

Output:

```text
[
  { name: "Alice", average: 85 },
  { name: "Bob", average: 68.33333333333333 },
  { name: "Carol", average: null }
]
```

**Explanation:** The outer `for` selects a student. The inner `for` sums that student's scores. `total` resets for each student. The ternary `condition ? ifTrue : ifFalse` checks whether the score array is empty; if so, it uses `null`, otherwise it divides by the score count. `results.push({ ... })` appends a newly created summary object.

| Student | Score additions | Total | Count | Average |
| --- | --- | ---: | ---: | ---: |
| Alice | 80 + 90 + 85 | 255 | 3 | 85 |
| Bob | 60 + 75 + 70 | 205 | 3 | 68.333... |
| Carol | No scores | 0 | 0 | null |

### Exercise 9. Find duplicate names using nested loops

**Task:** Find names that appear more than once. Report each duplicate name only once. Do not use `Set`, `filter`, or `includes`.

```javascript
function duplicateNames(students) {
  const duplicates = [];

  for (let i = 0; i < students.length; i++) {
    const name = students[i].name;
    let seenEarlier = false;

    for (let j = 0; j < i; j++) {
      if (students[j].name === name) {
        seenEarlier = true;
        break;
      }
    }
    if (seenEarlier) continue;

    let occurrences = 0;
    for (let j = 0; j < students.length; j++) {
      if (students[j].name === name) {
        occurrences++;
      }
    }
    if (occurrences > 1) duplicates.push(name);
  }

  return duplicates;
}

const students = [
  { name: "Alice" },
  { name: "Bob" },
  { name: "Alice" },
  { name: "Carol" },
  { name: "Bob" },
  { name: "Alice" }
];
console.log(duplicateNames(students)); // ["Alice", "Bob"]
```

**Explanation:** The outer loop considers each name. The first inner loop checks whether the same name appeared at an earlier index; `break` stops that check as soon as a match is found. If already seen, `continue` skips the rest of the outer iteration. The second inner loop counts all occurrences of a name that is being considered for the first time. `occurrences > 1` identifies a duplicate. `push(name)` adds it exactly once.

| Outer index | Name | Seen earlier? | Occurrences if counted | Action |
| ---: | --- | --- | ---: | --- |
| 0 | Alice | No | 3 | Add Alice |
| 1 | Bob | No | 2 | Add Bob |
| 2 | Alice | Yes | Skipped | Continue |
| 3 | Carol | No | 1 | Do not add |
| 4 | Bob | Yes | Skipped | Continue |
| 5 | Alice | Yes | Skipped | Continue |

**Performance:** This solution is O(n^2) time in the worst case because it uses nested scans. It uses O(d) additional result space, where `d` is the number of distinct duplicate names. Later you can optimize counting with an object or `Map`.

### Exercise 10. Build a sales report using plain loops

**Task:** From an array of sales, calculate total revenue, total quantity, highest-revenue product, and products with revenue strictly above 10,000. Handle empty input.

```javascript
function buildSalesReport(sales) {
  let totalRevenue = 0;
  let totalQuantity = 0;
  let highestRevenueProduct = null;
  let highestProductRevenue = 0;
  const productsAbove10000 = [];

  for (const sale of sales) {
    const revenue = sale.price * sale.quantity;
    totalRevenue += revenue;
    totalQuantity += sale.quantity;

    if (highestRevenueProduct === null || revenue > highestProductRevenue) {
      highestRevenueProduct = sale.product;
      highestProductRevenue = revenue;
    }

    if (revenue > 10000) {
      productsAbove10000.push(sale.product);
    }
  }

  return {
    totalRevenue: totalRevenue,
    totalQuantity: totalQuantity,
    highestRevenueProduct: highestRevenueProduct,
    highestProductRevenue: highestProductRevenue,
    productsAbove10000: productsAbove10000
  };
}

const sales = [
  { product: "Laptop", price: 50000, quantity: 2 },
  { product: "Mouse", price: 1500, quantity: 5 },
  { product: "Keyboard", price: 3500, quantity: 3 },
  { product: "Monitor", price: 20000, quantity: 2 }
];

console.log(buildSalesReport(sales));
console.log(buildSalesReport([]));
```

Output:

```text
{
  totalRevenue: 158000,
  totalQuantity: 12,
  highestRevenueProduct: "Laptop",
  highestProductRevenue: 100000,
  productsAbove10000: ["Laptop", "Keyboard", "Monitor"]
}
{
  totalRevenue: 0,
  totalQuantity: 0,
  highestRevenueProduct: null,
  highestProductRevenue: 0,
  productsAbove10000: []
}
```

**Line-by-line concepts:**

1. `function buildSalesReport(sales)` declares a function accepting an array.
2. The `let` variables hold changing totals and the current best product.
3. `const productsAbove10000 = []` creates a result array that can be mutated with `push()`.
4. `for (const sale of sales)` reads each sales object in order.
5. `sale.price * sale.quantity` calculates that sale's revenue.
6. `totalRevenue += revenue` accumulates revenue.
7. `totalQuantity += sale.quantity` accumulates units sold.
8. `highestRevenueProduct === null || revenue > highestProductRevenue` selects the first product or replaces the best with a strictly higher one.
9. `revenue > 10000` is strict: exactly `10000` does not qualify.
10. `productsAbove10000.push(sale.product)` records qualifying names.
11. `return { ... }` constructs and returns the final summary object.
12. An empty array causes zero loop iterations, leaving safe default totals and a `null` highest product.

| Product | Revenue | Running revenue | Running quantity | Highest product | Above 10000? |
| --- | ---: | ---: | ---: | --- | --- |
| Laptop | 100000 | 100000 | 2 | Laptop | Yes |
| Mouse | 7500 | 107500 | 7 | Laptop | No |
| Keyboard | 10500 | 118000 | 10 | Laptop | Yes |
| Monitor | 40000 | 158000 | 12 | Laptop | Yes |

**Optional extension:** Validate that each price and quantity is a finite, nonnegative number before calculating financial totals. The example assumes valid input data.

---

## 19. Mastery checklist and navigation

Before continuing, verify you can explain and implement each of these without copying a solution:

- [ ] Explain the difference between an object key and an array index.
- [ ] Explain why `object[key]` is not the same as `object.key`.
- [ ] Enumerate own object properties with `for...in` and `Object.hasOwn()`.
- [ ] Traverse `Object.keys()` using `for`, `while`, and `do...while`.
- [ ] Traverse `Object.entries()` using `for...of` and destructuring.
- [ ] Process an array of objects using a plain loop.
- [ ] Build a filtered result using `if` and `push()`.
- [ ] Accumulate totals and calculate averages safely.
- [ ] Find a minimum or maximum with an empty-input fallback.
- [ ] Trace nested loops and reset inner accumulators correctly.
- [ ] Use `break` and `continue` intentionally.
- [ ] Recognize inherited properties, symbol keys, and enumeration-order issues.

**Previous:** [Lesson 12 - Objects and Object Methods](12-javascript-objects-and-object-methods.md)  
**Current:** Lesson 12A - Objects and Plain Loops  
**Next:** [Lesson 13 - Object-Oriented Programming](13-object-oriented-programming-in-javascript.md)

**Study recommendation:** Reimplement Exercises 4, 8, 9, and 10 from memory, then trace the values of every variable for each iteration. This builds the skills needed to understand both advanced array methods and object-oriented code.
