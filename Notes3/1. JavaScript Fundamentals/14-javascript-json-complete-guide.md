# Lesson 14: JSON (JavaScript Object Notation)

**Level:** Beginner to Advanced  
**Previous:** [Lesson 12B - Objects: Plain Loops vs Methods](12b-javascript-objects-plain-loops-vs-methods.md)  
**Next:** [Lesson 15 - DOM Manipulation](15-javascript-dom-manipulation.md)  
**Postponed:** Lesson 13 - Object-Oriented Programming

## Table of Contents

1. [Learning objectives](#1-learning-objectives)
2. [What is JSON?](#2-what-is-json)
3. [JSON versus JavaScript objects](#3-json-versus-javascript-objects)
4. [JSON syntax rules](#4-json-syntax-rules)
5. [JSON data types](#5-json-data-types)
6. [JSON.stringify](#6-jsonstringify)
7. [JSON.parse](#7-jsonparse)
8. [Nested JSON](#8-nested-json)
9. [JSON with plain loops versus methods](#9-json-with-plain-loops-versus-methods)
10. [Filtering and transforming JSON records](#10-filtering-and-transforming-json-records)
11. [Updating and reserializing JSON](#11-updating-and-reserializing-json)
12. [Pretty printing and replacers](#12-pretty-printing-and-replacers)
13. [Invalid JSON and error handling](#13-invalid-json-and-error-handling)
14. [Advanced serialization behavior](#14-advanced-serialization-behavior)
15. [JSON and localStorage](#15-json-and-localstorage)
16. [JSON and the Fetch API](#16-json-and-the-fetch-api)
17. [Complete student records project](#17-complete-student-records-project)
18. [Common mistakes and best practices](#18-common-mistakes-and-best-practices)
19. [Ten interview questions with answers](#19-ten-interview-questions-with-answers)
20. [Ten coding exercises with complete solutions](#20-ten-coding-exercises-with-complete-solutions)
21. [Mastery checklist](#21-mastery-checklist)
22. [Lesson navigation](#22-lesson-navigation)

## 1. Learning objectives

By the end, you should be able to explain JSON syntax, distinguish JSON text from JavaScript objects, serialize and parse data, process parsed arrays and objects with **both plain loops and methods**, validate errors and expected shapes, and build a JSON report. All examples are generic and self-contained.

## 2. What is JSON?

JSON is a text format for exchanging structured data. It is not executable JavaScript. A server might send this response body:

```json
{"name":"Alice","age":20,"passed":true}
```

The double-quoted names are **keys**; the values are a string, number, and boolean. JSON is supported across many programming languages. A JSON document may have an object, array, string, number, boolean, or `null` at the top level.

## 3. JSON versus JavaScript objects

```javascript
// JavaScript object in memory:
const student = { name: "Alice", age: 20 };
// JSON text stored in a JavaScript string:
const jsonText = '{"name":"Alice","age":20}';
console.log(typeof student);  // "object"
console.log(typeof jsonText); // "string"
```

JavaScript object keys may be unquoted, and objects may contain methods, undefined, symbols, and other values. JSON keys and strings **must** use double quotes; JSON has no functions or undefined values. `const` prevents reassignment of `student`, not changing `student.age`.

## 4. JSON syntax rules

Valid JSON:

```json
{
  "name": "Alice",
  "age": 20,
  "active": true,
  "tags": ["beginner", "student"],
  "address": null
}
```

Rules: use double-quoted keys and strings; use `:` between a key and its value; separate members with commas; do not put a trailing comma; do not include comments; use lowercase `true`, `false`, and `null`; numbers cannot be `NaN`, `Infinity`, or `-Infinity`. The JSON object is delimited by `{` and `}`; arrays by `[` and `]`. The comma separates elements; the colon separates a property name from its value.

Invalid JSON examples (shown as JavaScript strings so the examples themselves are runnable):

```javascript
const unquotedKey = '{name:"Alice"}';
const singleQuotes = "{'name':'Alice'}";
const trailingComma = '{"name":"Alice",}';
const commented = '{"age":20 // age
}';
```

## 5. JSON data types

JSON supports strings, finite numbers, booleans, null, arrays, and objects. Arrays and objects may nest arbitrarily (within practical size and memory limits).

```json
{
  "title": "Notebook",
  "price": 12.5,
  "available": false,
  "discount": null,
  "colors": ["red", "blue"],
  "dimensions": {"width": 10, "height": 15}
}
```

`null` is different from the string `"null"`. JSON has no native Date, BigInt, function, or undefined type. Dates are often represented as ISO 8601 strings by convention.

## 6. JSON.stringify

`JSON.stringify(value)` serializes a JavaScript value into JSON text (when serialization is possible).

```javascript
const student = { name: "Alice", age: 20, passed: true };
const text = JSON.stringify(student);
console.log(text);        // {"name":"Alice","age":20,"passed":true}
console.log(typeof text); // "string"
```

**Line-by-line:** `const` declares a binding; `text` is its name; `=` assigns the expression result; `JSON` is the built-in object; `.` accesses its `stringify` method; `(student)` passes the argument; `;` ends the statement. `JSON.stringify` does not modify `student`. Some top-level values, such as `undefined`, serialize to `undefined` rather than a string.

## 7. JSON.parse

`JSON.parse(text)` converts valid JSON text into a JavaScript value.

```javascript
const jsonText = '{"name":"Alice","age":20}';
const student = JSON.parse(jsonText);
console.log(student.name); // Alice
console.log(student.age);  // 20
```

The outer single quotes delimit a JavaScript string; the inner double quotes are characters in the JSON text. Alternatively use escaped quotes: `"{\"name\":\"Alice\"}"` (less readable).

Execution trace:

| Step | Expression | Result |
| --- | --- | --- |
| 1 | `jsonText` | JavaScript string |
| 2 | `JSON.parse(jsonText)` | JavaScript object |
| 3 | `student.name` | `"Alice"` |
| 4 | `student.age` | `20` |

**Round trip:** `JSON.parse(JSON.stringify(value))` can copy JSON-compatible data, but is not a general-purpose deep clone.

## 8. Nested JSON

```javascript
const text = `{
  "name":"Alice",
  "address":{"city":"Nairobi","country":"Kenya"},
  "subjects":[{"name":"Math","score":85},{"name":"English","score":90}]
}`;
const student = JSON.parse(text);
console.log(student.address.city);   // Nairobi
console.log(student.subjects[1].score); // 90
```

Read `student.subjects[1].score` left to right: retrieve `subjects` array, index `1` (second object), then its `score` property. Accessing missing intermediate properties may throw; `student.address?.city ?? "Unknown"` is safer when `address` may be null or missing. `?.` stops on null/undefined; `??` supplies a fallback only for null/undefined, unlike `||` which also treats zero, false, and empty strings as absent.

## 9. JSON with plain loops versus methods

First parse the JSON. The string is not an array of data records until parsed.

```javascript
const data = JSON.parse('{"scores":[80,75,90,85]}');

// A. Traditional for loop:
let loopTotal = 0;
for (let i = 0; i < data.scores.length; i++) {
  loopTotal += data.scores[i];
}

// B. Array reduce method:
const methodTotal = data.scores.reduce((sum, score) => sum + score, 0);
console.log(loopTotal, methodTotal); // 330 330
```

`i = 0` initializes an index, `i < length` is the continuation test, and `i++` increments the index. `+=` adds to and reassigns the accumulator. In `reduce`, `sum` is the accumulator, `score` is the current element, `=>` introduces an arrow function, and the final `0` is the initial accumulator value.

| Step | Score | Loop total | Reduce sum |
| --- | ---: | ---: | ---: |
| Start | - | 0 | 0 |
| 1 | 80 | 80 | 80 |
| 2 | 75 | 155 | 155 |
| 3 | 90 | 245 | 245 |
| 4 | 85 | 330 | 330 |

For plain object properties, compare:

```javascript
const scores = { math: 80, english: 75, science: 90 };
// A. Plain for...in loop with own-property guard:
let totalA = 0;
for (const key in scores) {
  if (Object.hasOwn(scores, key)) totalA += scores[key];
}
// B. Object.values + reduce:
const totalB = Object.values(scores).reduce((sum, value) => sum + value, 0);
console.log(totalA, totalB); // 245 245
```

`for...in` can enumerate inherited enumerable properties, so `Object.hasOwn()` is important. `Object.values()` returns only own enumerable string-keyed values. Both omit symbol-keyed properties.

## 10. Filtering and transforming JSON records

```javascript
const products = JSON.parse(`[
  {"name":"Laptop","price":50000,"available":true},
  {"name":"Mouse","price":1500,"available":false},
  {"name":"Keyboard","price":3500,"available":true}
]`);

// Filter, plain loop:
const availableA = [];
for (const product of products) {
  if (product.available === true) availableA.push(product);
}
// Filter, built-in method:
const availableB = products.filter(product => product.available === true);

// Transform to names, plain loop:
const namesA = [];
for (const product of products) namesA.push(product.name);
// Transform to names, built-in method:
const namesB = products.map(product => product.name);
console.log(namesA, namesB); // ["Laptop", "Mouse", "Keyboard"] twice
```

`filter` keeps elements whose callback returns truthy. `map` produces one output per input element. `for...of` iterates the array's values. `push` appends to an array. Arrow functions without braces implicitly return their expression; with braces you need `return`.

## 11. Updating and reserializing JSON

```javascript
const originalText = '{"name":"Alice","age":20}';
const student = JSON.parse(originalText);
student.age = 21;
student.city = "Nairobi";
const updatedText = JSON.stringify(student);
console.log(originalText); // {"name":"Alice","age":20}
console.log(updatedText);  // {"name":"Alice","age":21,"city":"Nairobi"}
```

Strings are immutable: the original text does not change. Parsing produces an object, object properties can change, and stringify produces a new string.

## 12. Pretty printing and replacers

```javascript
const student = { name: "Alice", age: 20, secret: "hidden" };
console.log(JSON.stringify(student, null, 2));
// Two-space indentation
console.log(JSON.stringify(student, ["name", "age"], 2));
// Includes only selected properties
```

Signature: `JSON.stringify(value, replacer, space)`. `value` is serialized; `replacer` can be a function or array controlling serialized data; `space` controls indentation. Do not assume a replacer alone is sufficient to protect sensitive data; explicitly choose safe fields before transmitting or storing records.

## 13. Invalid JSON and error handling

```javascript
function safeParse(text) {
  try {
    return { ok: true, value: JSON.parse(text) };
  } catch (error) {
    return { ok: false, error: error.message };
  }
}
console.log(safeParse('{"name":"Alice"}'));
console.log(safeParse('{"name":"Alice",}'));
```

`try` runs potentially failing code; `catch` handles a thrown error. Invalid JSON throws a `SyntaxError`. A successful parse does **not** mean the data matches your application's expected structure:

```javascript
function isValidStudent(value) {
  return value !== null &&
    typeof value === "object" &&
    !Array.isArray(value) &&
    typeof value.name === "string" &&
    typeof value.age === "number" &&
    Number.isFinite(value.age) &&
    value.age >= 0;
}
```

Here `&&` requires all conditions to be true; `!` negates; `Array.isArray` distinguishes arrays from ordinary objects; `Number.isFinite` rejects NaN and infinity.

## 14. Advanced serialization behavior

```javascript
JSON.stringify({ name: "Alice", age: undefined }); // '{"name":"Alice"}'
JSON.stringify([10, undefined, 30]);          // '[10,null,30]'
JSON.stringify({ value: NaN });                // '{"value":null}'
JSON.stringify(new Date("2026-01-01T00:00:00Z")); // '"2026-01-01T00:00:00.000Z"'
```

Function-valued object properties are omitted. `BigInt` normally causes `JSON.stringify` to throw unless customized. Circular references cause a `TypeError`:

```javascript
const item = {};
item.self = item;
try { JSON.stringify(item); } catch (error) { console.log(error.name); } // TypeError
```

Date objects serialize to strings and parse back as strings. `JSON.parse(JSON.stringify(obj))` loses unsupported data; `structuredClone` can clone more kinds of structured values but also has limitations.

**Advanced: reviver**

```javascript
const data = JSON.parse('{"createdAt":"2026-01-01T00:00:00.000Z"}', (key, value) => {
  if (key === "createdAt") return new Date(value);
  return value;
});
console.log(data.createdAt instanceof Date); // true
```

A reviver transforms parsed values. Only revive dates when the field and format are trusted and expected.

## 15. JSON and localStorage

In browsers, `localStorage` stores strings:

```javascript
const student = { name: "Alice", age: 20 };
localStorage.setItem("student", JSON.stringify(student));
const saved = localStorage.getItem("student");
if (saved !== null) {
  try {
    const restored = JSON.parse(saved);
    console.log(restored.name);
  } catch (error) {
    console.error("Saved data is invalid", error);
  }
}
localStorage.removeItem("student");
```

`setItem(key, value)` writes a string; `getItem(key)` returns a string or `null`; `removeItem(key)` deletes it. Browser storage can be unavailable or throw in some contexts; do not store passwords or other secrets in localStorage. This API is not available in every JavaScript runtime.

## 16. JSON and the Fetch API

```javascript
async function loadUsers() {
  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/users");
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    const users = await response.json();
    console.log(users);
  } catch (error) {
    console.error("Unable to load users:", error.message);
  }
}
// Call loadUsers() in a browser or runtime with fetch support.
```

`async` enables `await`; `fetch` makes a request and returns a Promise; `response.ok` indicates a 2xx HTTP status; `response.json()` reads and parses the response body asynchronously. These concepts will receive full treatment in later asynchronous JavaScript and Fetch lessons.

## 17. Complete student records project

Input:

```javascript
const jsonText = `[
  {"id":1,"name":"Alice","score":85},
  {"id":2,"name":"Bob","score":42},
  {"id":3,"name":"Carol","score":73},
  {"id":4,"name":"David","score":35}
]`;
const students = JSON.parse(jsonText);
```

**Approach A: Plain loop**

```javascript
let total = 0;
let highest = null;
const passed = [];
for (const student of students) {
  total += student.score;
  if (student.score >= 50) passed.push(student);
  if (highest === null || student.score > highest.score) highest = student;
}
const reportA = {
  totalStudents: students.length,
  totalScore: total,
  averageScore: students.length ? total / students.length : 0,
  passedStudents: passed,
  highestStudent: highest
};
console.log(JSON.stringify(reportA, null, 2));
```

**Approach B: Methods**

```javascript
const totalB = students.reduce((sum, student) => sum + student.score, 0);
const passedB = students.filter(student => student.score >= 50);
const highestB = students.reduce((highest, student) =>
  highest === null || student.score > highest.score ? student : highest, null);
const reportB = {
  totalStudents: students.length,
  totalScore: totalB,
  averageScore: students.length ? totalB / students.length : 0,
  passedStudents: passedB,
  highestStudent: highestB
};
console.log(JSON.stringify(reportB, null, 2));
```

Both produce total `235`, average `58.75`, passed students Alice and Carol, highest student Alice. Execution trace:

| Student | Score | Running total | Pass? | Highest so far |
| --- | ---: | ---: | --- | --- |
| Alice | 85 | 85 | Yes | Alice |
| Bob | 42 | 127 | No | Alice |
| Carol | 73 | 200 | Yes | Alice |
| David | 35 | 235 | No | Alice |

The plain loop updates several accumulators in one pass. The method version uses separate operations that express each intention clearly. Both use `JSON.stringify` to produce a portable report string.

## 18. Common mistakes and best practices

| Mistake | Correct understanding |
| --- | --- |
| Treating JSON text like an object | Parse first using `JSON.parse` |
| Writing single quotes inside JSON | JSON strings must use double quotes |
| Leaving trailing commas | Remove them |
| Assuming `JSON.stringify` returns an object | It returns a string (or undefined for some values) |
| Expecting methods to survive serialization | Functions are not JSON data |
| Assuming valid JSON means valid application data | Validate shape and field types separately |
| Assuming JSON is a safe deep clone | Serialization can lose or transform data |
| Using `for...in` without an own-property guard | Use `Object.hasOwn` if inherited keys are unwanted |
| Forgetting empty-array cases | Supply initial values to `reduce`; guard division and maxima |
| Storing secrets in browser storage | Avoid storing sensitive secrets in localStorage |

## 19. Ten interview questions with answers

**Q1. What is JSON?**  
JSON is a language-independent text format for structured data exchange, supporting objects, arrays, strings, finite numbers, booleans, and null.

**Q2. JSON versus a JavaScript object?**  
JSON is text with strict syntax. A JavaScript object is a runtime value and can have functions and other non-JSON properties.

**Q3. What does JSON.stringify return?**  
A JSON string for supported values; some top-level values, such as undefined, result in undefined instead.

**Q4. What does JSON.parse return?**  
A JavaScript value representing the parsed JSON: it might be an object, array, string, number, boolean, or null.

**Q5. Can JSON use single quotes?**  
No. Keys and string values require double quotes.

**Q6. What happens on invalid JSON?**  
`JSON.parse` throws `SyntaxError`. Use `try...catch` to recover gracefully.

**Q7. Can JSON contain functions?**  
No. Function-valued object properties are omitted by `JSON.stringify`.

**Q8. How do you pretty-print JSON?**  
`JSON.stringify(value, null, 2)` requests two spaces of indentation.

**Q9. How do you process a JSON array with loops and methods?**  
Parse it first, then use `for`/`for...of` or `map`/`filter`/`reduce` on the resulting array.

**Q10. Does successful parsing validate data?**  
No. Syntax validation and application/schema validation are different. Check required fields, types, ranges, and nested structures.

## 20. Ten coding exercises with complete solutions

Each exercise includes a problem, runnable model solution, and explanation. Where a collection is involved, compare a **plain loop** with a **built-in method**.

### Exercise 1: Create and serialize a student

**Task:** Create `{ name: "Alice", age: 20, score: 85, active: true }` and produce JSON text.

**Complete model solution:**

```javascript
const student = { name: "Alice", age: 20, score: 85, active: true };
const json = JSON.stringify(student);
console.log(json);
```

**Explanation and execution trace:** `student` is an in-memory object; `JSON.stringify` converts it into a string. The result includes double-quoted keys and string values. No loop is needed because this is a serialization operation.

### Exercise 2: Parse and calculate product value

**Task:** Parse a product with price 1500 and quantity 3; compute its total value.

**Complete model solution:**

```javascript
const text = '{"name":"Mouse","price":1500,"quantity":3}';
const product = JSON.parse(text);
const total = product.price * product.quantity;
console.log(total); // 4500
```

**Explanation and execution trace:** Parsing produces an object; `.price` is 1500 and `.quantity` is 3. `*` multiplies them. The original text remains unchanged.

### Exercise 3: Access nested JSON

**Task:** Parse a person with an address and print city and country.

**Complete model solution:**

```javascript
const text = '{"name":"Alice","address":{"city":"Nairobi","country":"Kenya"}}';
const person = JSON.parse(text);
console.log(person.address.city);    // Nairobi
console.log(person.address.country); // Kenya
```

**Explanation and execution trace:** The first dot accesses the nested address object; the second dot accesses its property. When address is optional, use `person.address?.city ?? "Unknown"`.

### Exercise 4: Sum subject scores twice

**Task:** Given JSON scores 80, 75, and 90, compute the sum using `for...in` and `Object.values().reduce()`.

**Complete model solution:**

```javascript
const scores = JSON.parse('{"math":80,"english":75,"science":90}');
let loopTotal = 0;
for (const key in scores) {
  if (Object.hasOwn(scores, key)) loopTotal += scores[key];
}
const methodTotal = Object.values(scores).reduce((sum, value) => sum + value, 0);
console.log(loopTotal, methodTotal); // 245 245
```

**Explanation and execution trace:** The loop enumerates keys and reads each value through `scores[key]`. The method creates an array `[80,75,90]`, then reduces from zero. Running totals: 80, 155, 245.

### Exercise 5: Filter student records twice

**Task:** From Alice 85, Bob 42, Carol 73, return scores at least 70 using a loop and filter.

**Complete model solution:**

```javascript
const students = JSON.parse('[{"name":"Alice","score":85},{"name":"Bob","score":42},{"name":"Carol","score":73}]');
const loopResult = [];
for (const student of students) {
  if (student.score >= 70) loopResult.push(student);
}
const methodResult = students.filter(student => student.score >= 70);
console.log(loopResult, methodResult);
```

**Explanation and execution trace:** The condition `>= 70` is true for Alice and Carol. The loop explicitly pushes matching objects; `filter` builds the matching array. Neither approach changes the original array.

### Exercise 6: Extract product names twice

**Task:** Convert an array of product objects into an array of names using a loop and map.

**Complete model solution:**

```javascript
const products = JSON.parse('[{"name":"Laptop"},{"name":"Mouse"},{"name":"Keyboard"}]');
const loopNames = [];
for (const product of products) loopNames.push(product.name);
const methodNames = products.map(product => product.name);
console.log(loopNames, methodNames); // both: ["Laptop", "Mouse", "Keyboard"]
```

**Explanation and execution trace:** `product` is the current object. `.name` retrieves its string. `map` returns exactly one output per input element; the loop achieves the same result through `push`.

### Exercise 7: Find employee ID 3 twice

**Task:** Find the employee with ID 3 using a loop and find().

**Complete model solution:**

```javascript
const employees = JSON.parse('[{"id":1,"name":"Alice"},{"id":3,"name":"Carol"},{"id":5,"name":"David"}]');
let loopFound;
for (const employee of employees) {
  if (employee.id === 3) { loopFound = employee; break; }
}
const methodFound = employees.find(employee => employee.id === 3);
console.log(loopFound, methodFound); // both: { id: 3, name: "Carol" }
```

**Explanation and execution trace:** Strict equality `===` compares the numeric ID to 3. `break` exits the loop at the first match; `find` does that automatically. No match produces undefined.

### Exercise 8: Safe JSON parser

**Task:** Write a function that never throws for malformed JSON; return a success/failure object.

**Complete model solution:**

```javascript
function safeParse(text) {
  try {
    return { ok: true, data: JSON.parse(text) };
  } catch (error) {
    return { ok: false, error: error.message };
  }
}
console.log(safeParse('{"a":1}')); // { ok: true, data: { a: 1 } }
console.log(safeParse('{"a":1,}').ok); // false
```

**Explanation and execution trace:** `try` executes the parse. A successful parse returns a result with `ok: true`; a thrown syntax error transfers to `catch`, which returns `ok: false`. No loop is needed.

### Exercise 9: Update a product and reserialize

**Task:** Update product ID 2 to price 1800 and produce indented JSON, using a loop and find().

**Complete model solution:**

```javascript
const text = '[{"id":1,"price":50000},{"id":2,"price":1500}]';
const loopProducts = JSON.parse(text);
for (const product of loopProducts) {
  if (product.id === 2) { product.price = 1800; break; }
}
const methodProducts = JSON.parse(text);
const target = methodProducts.find(product => product.id === 2);
if (target !== undefined) target.price = 1800;
console.log(JSON.stringify(loopProducts, null, 2));
console.log(JSON.stringify(methodProducts, null, 2));
```

**Explanation and execution trace:** Both arrays are parsed separately to keep comparisons independent. `find` returns a reference to an object inside the array, so assigning `.price` updates that object. `JSON.stringify(..., null, 2)` formats the new JSON text.

### Exercise 10: Build a sales report twice

**Task:** For sales of Laptop (50000 x 2), Mouse (1500 x 5), Keyboard (3500 x 3), calculate total revenue, units, highest-value sale, and sales exceeding 10000. Solve using loops and methods.

**Complete model solution:**

```javascript
const sales = JSON.parse(`[
  {"name":"Laptop","price":50000,"quantity":2},
  {"name":"Mouse","price":1500,"quantity":5},
  {"name":"Keyboard","price":3500,"quantity":3}
]`);
// A. Plain loop
let revenueA = 0;
let unitsA = 0;
let highestA = null;
const largeA = [];
for (const sale of sales) {
  const value = sale.price * sale.quantity;
  revenueA += value;
  unitsA += sale.quantity;
  if (highestA === null || value > highestA.price * highestA.quantity) highestA = sale;
  if (value > 10000) largeA.push(sale);
}
// B. Built-in methods
const revenueB = sales.reduce((sum, sale) => sum + sale.price * sale.quantity, 0);
const unitsB = sales.reduce((sum, sale) => sum + sale.quantity, 0);
const highestB = sales.reduce((best, sale) =>
  best === null || sale.price * sale.quantity > best.price * best.quantity ? sale : best, null);
const largeB = sales.filter(sale => sale.price * sale.quantity > 10000);
console.log({ revenueA, unitsA, highestA, largeA });
console.log({ revenueB, unitsB, highestB, largeB });
console.log(JSON.stringify({ totalRevenue: revenueB, totalUnits: unitsB, highestSale: highestB, largeSales: largeB }, null, 2));
```

**Explanation and execution trace:** Sale values: Laptop 100000, Mouse 7500, Keyboard 10500. Revenue accumulation: 100000, 107500, 118000. Total units: 2 + 5 + 3 = 10. Highest sale is Laptop. Large sales are Laptop and Keyboard because 100000 and 10500 are greater than 10000. The plain loop computes all aggregates in one pass; the method approach uses separate `reduce` and `filter` passes. For an empty array, both approaches return revenue 0, units 0, highest null, and large sales [].

## 21. Mastery checklist

- [ ] I can distinguish JSON text from JavaScript objects.
- [ ] I can explain strict JSON syntax and supported data types.
- [ ] I can use `JSON.stringify` and `JSON.parse`.
- [ ] I can read nested JSON and handle optional properties.
- [ ] I can sum, filter, map, and search parsed JSON using plain loops.
- [ ] I can perform the same operations with array/object methods.
- [ ] I can explain accumulators, arrow functions, `return`, `break`, `?.`, and `??`.
- [ ] I can handle invalid JSON and validate expected data types.
- [ ] I can explain serialization limitations and browser storage concerns.
- [ ] I can generate a complete JSON report from records.

## 22. Lesson navigation

**Previous:** [Lesson 12B - Objects: Plain Loops vs Methods](12b-javascript-objects-plain-loops-vs-methods.md)  
**Current:** Lesson 14 - JSON  
**Next:** [Lesson 15 - DOM Manipulation](15-javascript-dom-manipulation.md)  
**Deferred:** Lesson 13 - Object-Oriented Programming (return later)
