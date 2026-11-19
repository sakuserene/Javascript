# Lesson 12B: JavaScript Objects - Plain Loops vs Built-in Methods

**Course:** JavaScript Mastery | **Level:** Beginner to Intermediate | **Format:** GitHub-ready standalone lesson

**Previous:** [Lesson 12A - Objects and Plain Loops](12a-javascript-objects-and-plain-loops.md)  
**Next:** [Lesson 13 - Object-Oriented Programming](13-object-oriented-programming.md)

## Table of Contents

1. [Learning objectives](#1-learning-objectives)
2. [Objects, arrays, and the two approaches](#2-objects-arrays-and-the-two-approaches)
3. [Enumerating keys and values](#3-enumerating-keys-and-values)
4. [Summing object values](#4-summing-object-values)
5. [Finding a maximum](#5-finding-a-maximum)
6. [Filtering an array of objects](#6-filtering-an-array-of-objects)
7. [Transforming an array of objects](#7-transforming-an-array-of-objects)
8. [Finding a matching object](#8-finding-a-matching-object)
9. [Testing any and all matches](#9-testing-any-and-all-matches)
10. [Counting occurrences](#10-counting-occurrences)
11. [Grouping objects](#11-grouping-objects)
12. [Performance and behavior](#12-performance-and-behavior)
13. [Common mistakes and debugging](#13-common-mistakes-and-debugging)
14. [Ten interview questions and answers](#14-ten-interview-questions-and-answers)
15. [Ten coding exercises with dual solutions](#15-ten-coding-exercises-with-dual-solutions)
16. [Mastery checklist](#16-mastery-checklist)
17. [Lesson navigation](#17-lesson-navigation)

---

## 1. Learning objectives

Learn to solve the **same problem twice**: first with an ordinary loop (`for`, `while`, `for...in`, or `for...of`), then with a built-in method (`Object.keys`, `Object.values`, `Object.entries`, `forEach`, `filter`, `map`, `find`, `some`, `every`, or `reduce`). Explain each operator, accumulator, callback, return value, mutation, edge case, and execution trace. Examples are generic and runnable in a browser console or Node.js.

## 2. Objects, arrays, and the two approaches

```js
const student = { name: "Alice", age: 20, score: 85 };
```

An object holds **properties**, each associated with a key. An array holds **ordered indexed elements**. Ordinary objects are not iterable by `for...of` directly. `for...in` enumerates enumerable string keys, including inherited ones; `Object.keys()`, `Object.values()`, and `Object.entries()` return arrays of **own enumerable string-keyed** properties. Symbol keys are omitted by all these operations. Numeric-looking object keys have particular enumeration ordering rules.

**Plain loop:**

```js
for (const key in student) {
  if (Object.hasOwn(student, key)) {
    console.log(key, student[key]);
  }
}
```

**Built-in methods:**

```js
Object.entries(student).forEach(([key, value]) => {
  console.log(key, value);
});
```

Both print `name Alice`, `age 20`, `score 85` for this object.

**Syntax, piece by piece:** `for` starts a loop; `const key` is a fresh binding for the current key; `in` enumerates keys; `Object.hasOwn(student, key)` returns a boolean for an **own** property; `student[key]` evaluates the variable `key` and accesses that property; `Object.entries(student)` returns `[ ["name", "Alice"], ["age", 20], ["score", 85] ]`; `.forEach(...)` invokes a callback for each array element; `[key, value]` destructures the two-element pair; `=>` separates an arrow function's parameters from its body. `student.key` would instead access a property literally named `key`.

The method solution creates an intermediate array. The plain `for...in` solution does not. `forEach()` returns `undefined`, not a new transformed array. Use `map()` for transformations.

## 3. Enumerating keys and values

**Task:** Print an object's own enumerable string keys.

```js
const product = { name: "Laptop", price: 50000, quantity: 3 };

// A: Plain loop
for (const key in product) {
  if (Object.hasOwn(product, key)) console.log(key);
}

// B: Methods
Object.keys(product).forEach(key => console.log(key));
```

`Object.keys(product)` produces `["name", "price", "quantity"]`. Both approaches print the same three names. `key => console.log(key)` is an arrow function with one parameter; its expression is evaluated for each key. For each property value instead, use `product[key]` inside `for...in`, or `Object.values(product).forEach(value => console.log(value))`.

**Trace:** iteration 1 key=`"name"`; iteration 2 key=`"price"`; iteration 3 key=`"quantity"`. In a plain loop you can `break` or `continue`; in `forEach()` a callback's `return` exits only that callback invocation, not the overall iteration.

## 4. Summing object values

```js
const scores = { math: 80, english: 75, science: 90 };

// A: Plain loop
let totalA = 0;
for (const subject in scores) {
  if (Object.hasOwn(scores, subject)) {
    totalA = totalA + scores[subject];
  }
}

// B: Methods
const totalB = Object.values(scores).reduce((sum, score) => {
  return sum + score;
}, 0);
console.log(totalA, totalB); // 245 245
```

`let` allows reassignment; `totalA = totalA + ...` reads the old total, adds the current value, and writes the new total. The shorthand `totalA += scores[subject]` is equivalent here. `Object.values()` creates `[80, 75, 90]`. `reduce(callback, 0)` starts an accumulator at zero. `sum` is the current accumulator; `score` is the current element; `return sum + score` supplies the accumulator for the next iteration. **Do not omit `return`** from a block-bodied reducer.

| Step | Subject | Current value | Previous total | New total |
|---|---|---:|---:|---:|
| 1 | math | 80 | 0 | 80 |
| 2 | english | 75 | 80 | 155 |
| 3 | science | 90 | 155 | 245 |

An empty object produces `0` in both solutions. Non-numeric values may cause string concatenation or `NaN`; validate data before summing.

## 5. Finding a maximum

```js
const scores = { math: 80, english: 75, science: 90 };

// A: Plain loop, undefined for empty objects
let highestA;
for (const key in scores) {
  if (!Object.hasOwn(scores, key)) continue;
  const value = scores[key];
  if (highestA === undefined || value > highestA) highestA = value;
}

// B: Methods
const values = Object.values(scores);
const highestB = values.length === 0 ? undefined : Math.max(...values);
console.log(highestA, highestB); // 90 90
```

`!` negates a boolean; `continue` skips to the next iteration; `||` means OR and short-circuits; `>` compares numbers. The ternary `condition ? whenTrue : whenFalse` handles the empty object. `...values` spreads the array into individual arguments, conceptually `Math.max(80,75,90)`. Avoid spread into `Math.max()` for huge arrays because of argument-count limits. Both solutions assume numeric, comparable values. A method alternative avoiding argument limits is `values.reduce((best, value) => best === undefined || value > best ? value : best, undefined)`.

## 6. Filtering an array of objects

```js
const students = [
  { name: "Alice", score: 85 },
  { name: "Bob", score: 42 },
  { name: "Carol", score: 73 },
  { name: "David", score: 35 }
];

// A: Plain loop
const passedA = [];
for (const student of students) {
  if (student.score >= 50) passedA.push(student);
}

// B: Method
const passedB = students.filter(student => student.score >= 50);
console.log(passedA, passedB);
```

Both arrays contain Alice and Carol. `for...of` visits array elements directly; `student` is the current object, not an index. `>=` means greater than or equal. `push()` adds an element to the result array. `filter()` creates the result array automatically and includes an element if the callback returns a truthy value. The source array is not changed by either solution, but the returned elements are **references to the same student objects**, not deep copies.

| Student | Score | `score >= 50` | Included? |
|---|---:|---|---|
| Alice | 85 | true | Yes |
| Bob | 42 | false | No |
| Carol | 73 | true | Yes |
| David | 35 | false | No |

## 7. Transforming an array of objects

```js
const students = [
  { name: "Alice", score: 85 },
  { name: "Bob", score: 42 },
  { name: "Carol", score: 73 }
];

// A: Plain loop
const namesA = [];
for (const student of students) namesA.push(student.name);

// B: Method
const namesB = students.map(student => student.name);
console.log(namesA, namesB); // ["Alice", "Bob", "Carol"] twice
```

`map()` creates one output slot for each visited input element; here each object becomes its `name` string. `student => student.name` implicitly returns the expression. By contrast, `student => { student.name; }` returns `undefined` because block bodies require an explicit `return` to return a value. `map()` is for transformations; `filter()` is for selecting a subset.

## 8. Finding a matching object

```js
const students = [
  { name: "Alice", score: 85 },
  { name: "Bob", score: 42 },
  { name: "Carol", score: 73 }
];

// A: Plain loop
let foundA;
for (const student of students) {
  if (student.name === "Carol") {
    foundA = student;
    break;
  }
}

// B: Method
const foundB = students.find(student => student.name === "Carol");
console.log(foundA, foundB); // Carol's object twice
```

`===` checks strict equality without coercion. `break` exits the loop at the first match. `find()` stops invoking its callback after the first truthy result. If nothing matches, both produce `undefined`. `filter()` would instead return an array of *all* matches.

## 9. Testing any and all matches

```js
const students = [
  { name: "Alice", score: 85 },
  { name: "Bob", score: 42 },
  { name: "Carol", score: 73 }
];

// A1: Does ANY student have a score below 50?
let anyFailedA = false;
for (const student of students) {
  if (student.score < 50) { anyFailedA = true; break; }
}
// B1: some()
const anyFailedB = students.some(student => student.score < 50);

// A2: Did ALL students score at least 50?
let allPassedA = true;
for (const student of students) {
  if (student.score < 50) { allPassedA = false; break; }
}
// B2: every()
const allPassedB = students.every(student => student.score >= 50);

console.log(anyFailedA, anyFailedB); // true true
console.log(allPassedA, allPassedB); // false false
```

`some()` stops on the first truthy callback result. `every()` stops on the first falsy callback result. On an empty array, `some()` returns `false` and `every()` returns `true`. The plain-loop initial booleans deliberately reproduce those edge cases.

## 10. Counting occurrences

```js
const fruits = ["Apple", "Banana", "Apple", "Mango", "Banana", "Apple"];

// A: Plain loop
const countsA = Object.create(null);
for (const fruit of fruits) {
  countsA[fruit] = (countsA[fruit] ?? 0) + 1;
}

// B: reduce()
const countsB = fruits.reduce((result, fruit) => {
  result[fruit] = (result[fruit] ?? 0) + 1;
  return result;
}, Object.create(null));

console.log(countsA); // Apple:3, Banana:2, Mango:1
console.log(countsB); // Same counts
```

`result[fruit]` uses the current fruit string as a dynamic property key. `??` means nullish coalescing: if the value on the left is `null` or `undefined`, use the right value (`0`); otherwise retain the existing count. `(existing ?? 0) + 1` increments the count. `Object.create(null)` creates a dictionary without inherited keys and avoids pitfalls with arbitrary names such as `"__proto__"`. The reducer must return `result` for the next iteration.

| Step | Fruit | Old count | New count |
|---|---|---:|---:|
| 1 | Apple | 0 | 1 |
| 2 | Banana | 0 | 1 |
| 3 | Apple | 1 | 2 |
| 4 | Mango | 0 | 1 |
| 5 | Banana | 1 | 2 |
| 6 | Apple | 2 | 3 |

## 11. Grouping objects

```js
const products = [
  { name: "Laptop", category: "Electronics" },
  { name: "Shirt", category: "Clothing" },
  { name: "Mouse", category: "Electronics" },
  { name: "Shoes", category: "Clothing" }
];

// A: Plain loop
const groupedA = Object.create(null);
for (const product of products) {
  const category = product.category;
  if (!Object.hasOwn(groupedA, category)) groupedA[category] = [];
  groupedA[category].push(product);
}

// B: reduce()
const groupedB = products.reduce((result, product) => {
  const category = product.category;
  if (!Object.hasOwn(result, category)) result[category] = [];
  result[category].push(product);
  return result;
}, Object.create(null));

console.log(groupedA);
console.log(groupedB);
```

Both group Laptop and Mouse under `Electronics`, and Shirt and Shoes under `Clothing`. `groupedA[category]` retrieves the array stored at the dynamic key. `push(product)` appends the current product. The reducer accumulates a dictionary rather than a number. The objects in the grouped arrays remain shared references to the input products.

## 12. Performance and behavior

| Task | Plain approach | Method approach | Typical time |
|---|---|---|---|
| Enumerate keys | `for...in` + own check | `Object.keys().forEach()` | O(n) |
| Sum values | `for...in` + accumulator | `Object.values().reduce()` | O(n) |
| Filter | Loop + `push()` | `filter()` | O(n) |
| Transform | Loop + `push()` | `map()` | O(n) |
| Find first | Loop + `break` | `find()` | O(n) worst case |
| Any match | Loop + `break` | `some()` | O(n) worst case |
| All match | Loop + `break` | `every()` | O(n) worst case |
| Count/group | Loop + dictionary | `reduce()` + dictionary | O(n) expected |

**Differences:** A plain loop provides explicit control of iteration, `break`, and `continue`. Array methods emphasize intent and often shorten code. `Object.values()` and `Object.entries()` allocate intermediate arrays. `filter()` and `map()` allocate output arrays. `reduce()` may mutate an accumulator you supply, even though it does not mutate the input array by itself. Big-O time is usually similar; constant factors, allocation, and readability differ. Prefer whichever version makes the algorithm easiest to verify and maintain.

## 13. Common mistakes and debugging

1. **`object.key` vs `object[key]`:** the first accesses a literal property called `key`; the second evaluates the variable `key`.
2. **`for...of` directly on `{}`:** ordinary objects are not iterable; use `Object.entries(object)` or `Object.keys(object)`.
3. **Inherited keys in `for...in`:** add `Object.hasOwn(object, key)` when you need only own properties.
4. **Missing `return` in a block-bodied `map()` callback:** `items.map(item => { item.name; })` produces `undefined` entries; write `return item.name` or omit braces.
5. **Missing `return` in a reducer:** the next accumulator becomes `undefined`.
6. **Assuming `forEach()` returns a new array:** it returns `undefined`; use `map()` or `filter()`.
7. **Assuming `return` in `forEach()` stops iteration:** it only ends that callback invocation; use a plain loop or `find()`/`some()` for early stopping.
8. **Forgetting the empty case:** `Math.max(...[])` returns `-Infinity`; `reduce()` without an initial value throws on an empty array.
9. **Unsafe arbitrary dictionary keys:** prefer `Object.create(null)` or `Map` when keys come from arbitrary input.
10. **Mistaking shallow output for deep copies:** `filter()` and grouping retain references to original object elements.

**Debugging technique:** Log the current key/element, accumulator before, condition result, and accumulator after on each iteration. Recreate the same trace for both implementations and verify identical outputs.

## 14. Ten interview questions and answers

### Question 1. What does `Object.keys(obj)` return?

**Answer:** An array of own enumerable string property keys. It does not include symbol keys or inherited properties.

### Question 2. What does `Object.values(obj)` return?

**Answer:** An array of values for own enumerable string-keyed properties.

### Question 3. What does `Object.entries(obj)` return?

**Answer:** An array of two-element `[key, value]` arrays for own enumerable string-keyed properties.

### Question 4. How does `for...in` differ from `for...of`?

**Answer:** `for...in` enumerates enumerable string keys (including inherited ones); `for...of` iterates values from an iterable such as an array.

### Question 5. What is the difference between `filter()` and `map()`?

**Answer:** `filter()` keeps matching elements; `map()` transforms each visited element into a new value.

### Question 6. What does `find()` return if there is no match?

**Answer:** `undefined`; unlike `filter()`, it returns a single element, not an array.

### Question 7. What do `some()` and `every()` return on an empty array?

**Answer:** `some()` returns `false`; `every()` returns `true`.

### Question 8. What does the initial `0` do in `reduce((sum,x)=>sum+x,0)`?

**Answer:** It initializes the accumulator to zero and makes an empty input return zero.

### Question 9. Why is `Object.hasOwn()` useful with `for...in`?

**Answer:** It excludes inherited enumerable properties and restricts processing to own properties.

### Question 10. When might a plain loop be preferable to `forEach()`?

**Answer:** When you need `break`, `continue`, explicit control flow, or want to avoid an intermediate array produced by `Object.entries()`.

## 15. Ten coding exercises with dual solutions

For each exercise, first read the problem, attempt it yourself, then compare the two worked solutions. The examples below are independent: run each exercise separately to avoid variable-name conflicts.

### Exercise 1: Count object properties

**Problem:** Count own enumerable string keys of `{name:"Alice", age:20, score:85}`. Expected: `3`.

**Solution A - Plain loop**

```js
const student = { name: "Alice", age: 20, score: 85 };
let count = 0;
for (const key in student) {
  if (Object.hasOwn(student, key)) count++;
}
console.log(count); // 3
```

**Solution B - Built-in method**

```js
const student = { name: "Alice", age: 20, score: 85 };
const count = Object.keys(student).length;
console.log(count); // 3
```

**Line-by-line reasoning and execution:** `count++` increments the count by one. The loop visits `name`, `age`, `score` and updates 0 -> 1 -> 2 -> 3. The method builds an array of keys and reads its `.length`. Empty object: both return 0.

**Comparison:** Solution A makes the iteration and state changes explicit. Solution B uses an API that describes the operation directly. Compare the printed outputs by running both independently.

### Exercise 2: Sum monthly values

**Problem:** Sum `{january:100, february:200, march:300}`. Expected: `600`.

**Solution A - Plain loop**

```js
const months = { january: 100, february: 200, march: 300 };
let total = 0;
for (const key in months) {
  if (Object.hasOwn(months, key)) total += months[key];
}
console.log(total); // 600
```

**Solution B - Built-in method**

```js
const months = { january: 100, february: 200, march: 300 };
const total = Object.values(months).reduce((sum, value) => sum + value, 0);
console.log(total); // 600
```

**Line-by-line reasoning and execution:** `+=` means assign the old total plus the new value. Trace: 0 -> 100 -> 300 -> 600. `Object.values()` returns `[100,200,300]`; the reducer starts at 0. Empty object returns 0.

**Comparison:** Solution A makes the iteration and state changes explicit. Solution B uses an API that describes the operation directly. Compare the printed outputs by running both independently.

### Exercise 3: Find highest value

**Problem:** Find the maximum of `{a:10,b:50,c:30}`. Expected: `50`.

**Solution A - Plain loop**

```js
const values = { a: 10, b: 50, c: 30 };
let highest;
for (const key in values) {
  if (!Object.hasOwn(values, key)) continue;
  const n = values[key];
  if (highest === undefined || n > highest) highest = n;
}
console.log(highest); // 50
```

**Solution B - Built-in method**

```js
const values = { a: 10, b: 50, c: 30 };
const numbers = Object.values(values);
const highest = numbers.length === 0 ? undefined : Math.max(...numbers);
console.log(highest); // 50
```

**Line-by-line reasoning and execution:** Loop trace: undefined -> 10 -> 50 -> 50. `continue` skips inherited keys. `...numbers` expands `[10,50,30]` into three arguments. Both return `undefined` for an empty object. For huge arrays prefer a loop or reducer rather than spread arguments.

**Comparison:** Solution A makes the iteration and state changes explicit. Solution B uses an API that describes the operation directly. Compare the printed outputs by running both independently.

### Exercise 4: Filter passing students

**Problem:** Return students with score >= 70. Expected: Alice and Carol.

**Solution A - Plain loop**

```js
const students = [
  { name: "Alice", score: 85 },
  { name: "Bob", score: 42 },
  { name: "Carol", score: 73 }
];
const passed = [];
for (const student of students) {
  if (student.score >= 70) passed.push(student);
}
console.log(passed);
```

**Solution B - Built-in method**

```js
const students = [
  { name: "Alice", score: 85 },
  { name: "Bob", score: 42 },
  { name: "Carol", score: 73 }
];
const passed = students.filter(student => student.score >= 70);
console.log(passed);
```

**Line-by-line reasoning and execution:** The condition is true, false, true for Alice, Bob, Carol. The loop manually pushes matching objects; `filter()` creates the array. Both return `[]` when none match. Both preserve object references.

**Comparison:** Solution A makes the iteration and state changes explicit. Solution B uses an API that describes the operation directly. Compare the printed outputs by running both independently.

### Exercise 5: Extract names

**Problem:** Convert student objects to name strings. Expected: `["Alice","Bob","Carol"]`.

**Solution A - Plain loop**

```js
const students = [{name:"Alice"},{name:"Bob"},{name:"Carol"}];
const names = [];
for (const student of students) names.push(student.name);
console.log(names);
```

**Solution B - Built-in method**

```js
const students = [{name:"Alice"},{name:"Bob"},{name:"Carol"}];
const names = students.map(student => student.name);
console.log(names);
```

**Line-by-line reasoning and execution:** Trace: [] -> ["Alice"] -> ["Alice","Bob"] -> ["Alice","Bob","Carol"]. The concise arrow callback implicitly returns `student.name`. `map()` preserves the array length for an ordinary dense array.

**Comparison:** Solution A makes the iteration and state changes explicit. Solution B uses an API that describes the operation directly. Compare the printed outputs by running both independently.

### Exercise 6: Find product by ID

**Problem:** Find the first product whose ID is 3. Expected: `{id:3,name:"Keyboard"}`.

**Solution A - Plain loop**

```js
const products = [{id:1,name:"Laptop"},{id:2,name:"Mouse"},{id:3,name:"Keyboard"}];
let found;
for (const product of products) {
  if (product.id === 3) { found = product; break; }
}
console.log(found);
```

**Solution B - Built-in method**

```js
const products = [{id:1,name:"Laptop"},{id:2,name:"Mouse"},{id:3,name:"Keyboard"}];
const found = products.find(product => product.id === 3);
console.log(found);
```

**Line-by-line reasoning and execution:** Check ID 1 (false), ID 2 (false), ID 3 (true). `break` and `find()` both stop after the first match. If no match exists, both yield `undefined`.

**Comparison:** Solution A makes the iteration and state changes explicit. Solution B uses an API that describes the operation directly. Compare the printed outputs by running both independently.

### Exercise 7: Check out-of-stock products

**Problem:** Determine whether any product has quantity 0. Expected: `true`.

**Solution A - Plain loop**

```js
const products = [{name:"Laptop",quantity:2},{name:"Mouse",quantity:0},{name:"Keyboard",quantity:3}];
let anyOutOfStock = false;
for (const product of products) {
  if (product.quantity === 0) { anyOutOfStock = true; break; }
}
console.log(anyOutOfStock); // true
```

**Solution B - Built-in method**

```js
const products = [{name:"Laptop",quantity:2},{name:"Mouse",quantity:0},{name:"Keyboard",quantity:3}];
const anyOutOfStock = products.some(product => product.quantity === 0);
console.log(anyOutOfStock); // true
```

**Line-by-line reasoning and execution:** Check Laptop (false), Mouse (true), stop without examining Keyboard. `some()` answers an existential yes/no question. On an empty array, both return false.

**Comparison:** Solution A makes the iteration and state changes explicit. Solution B uses an API that describes the operation directly. Compare the printed outputs by running both independently.

### Exercise 8: Validate all scores

**Problem:** Check every student score is between 0 and 100 inclusive. Expected: `false` because one score is 105.

**Solution A - Plain loop**

```js
const students = [{score:85},{score:105},{score:73}];
let valid = true;
for (const student of students) {
  if (student.score < 0 || student.score > 100) {
    valid = false;
    break;
  }
}
console.log(valid); // false
```

**Solution B - Built-in method**

```js
const students = [{score:85},{score:105},{score:73}];
const valid = students.every(student => student.score >= 0 && student.score <= 100);
console.log(valid); // false
```

**Line-by-line reasoning and execution:** `||` means OR: invalid when below 0 OR above 100. `&&` means AND: valid when at least 0 AND at most 100. The loop starts `true` and flips on the first invalid score. `every()` stops on the first false result. Empty input returns true in both approaches; decide separately whether that is appropriate for your application.

**Comparison:** Solution A makes the iteration and state changes explicit. Solution B uses an API that describes the operation directly. Compare the printed outputs by running both independently.

### Exercise 9: Count repeated words

**Problem:** Count `["red","blue","red","green","blue","red"]`. Expected red:3, blue:2, green:1.

**Solution A - Plain loop**

```js
const words = ["red","blue","red","green","blue","red"];
const counts = Object.create(null);
for (const word of words) {
  counts[word] = (counts[word] ?? 0) + 1;
}
console.log(counts);
```

**Solution B - Built-in method**

```js
const words = ["red","blue","red","green","blue","red"];
const counts = words.reduce((acc, word) => {
  acc[word] = (acc[word] ?? 0) + 1;
  return acc;
}, Object.create(null));
console.log(counts);
```

**Line-by-line reasoning and execution:** Trace: red 1, blue 1, red 2, green 1, blue 2, red 3. `?? 0` supplies zero when a key has no count. The reducer returns the accumulator object each iteration. A null-prototype object avoids collisions with inherited names.

**Comparison:** Solution A makes the iteration and state changes explicit. Solution B uses an API that describes the operation directly. Compare the printed outputs by running both independently.

### Exercise 10: Group products by category

**Problem:** Group Electronics (Laptop, Mouse) and Clothing (Shirt, Shoes).

**Solution A - Plain loop**

```js
const products = [
  {name:"Laptop",category:"Electronics"},
  {name:"Shirt",category:"Clothing"},
  {name:"Mouse",category:"Electronics"},
  {name:"Shoes",category:"Clothing"}
];
const groups = Object.create(null);
for (const product of products) {
  const key = product.category;
  if (!Object.hasOwn(groups, key)) groups[key] = [];
  groups[key].push(product);
}
console.log(groups);
```

**Solution B - Built-in method**

```js
const products = [
  {name:"Laptop",category:"Electronics"},
  {name:"Shirt",category:"Clothing"},
  {name:"Mouse",category:"Electronics"},
  {name:"Shoes",category:"Clothing"}
];
const groups = products.reduce((acc, product) => {
  const key = product.category;
  if (!Object.hasOwn(acc, key)) acc[key] = [];
  acc[key].push(product);
  return acc;
}, Object.create(null));
console.log(groups);
```

**Line-by-line reasoning and execution:** Iteration 1 creates Electronics and adds Laptop; 2 creates Clothing and adds Shirt; 3 appends Mouse to Electronics; 4 appends Shoes to Clothing. Both return two groups. The grouped arrays hold references to original product objects.

**Comparison:** Solution A makes the iteration and state changes explicit. Solution B uses an API that describes the operation directly. Compare the printed outputs by running both independently.

## 16. Mastery checklist

- [ ] I can explain why `object[key]` is different from `object.key`.
- [ ] I can use `for...in` safely with `Object.hasOwn()`.
- [ ] I know `Object.keys`, `Object.values`, and `Object.entries` produce arrays.
- [ ] I can explain each part of an arrow callback (`parameter => expression`).
- [ ] I can filter, map, find, and sum with both a loop and a method.
- [ ] I know when `break` is useful and why `forEach()` cannot use it.
- [ ] I can manually trace a reducer accumulator.
- [ ] I understand `some()` and `every()` on empty arrays.
- [ ] I can count or group arbitrary keys safely using `Object.create(null)`.
- [ ] I can compare readability, mutation, memory allocation, and edge cases.

## 17. Lesson navigation

**Previous:** [Lesson 12A - JavaScript Objects and Plain Loops](12a-javascript-objects-and-plain-loops.md)  
**Current:** Lesson 12B - JavaScript Objects: Plain Loops vs Built-in Methods  
**Next:** [Lesson 13 - Object-Oriented Programming](13-object-oriented-programming.md)

---

**Study tip:** For every new array or object method you learn, first write the equivalent `for` or `for...of` loop. Trace both with a table showing current item, condition, accumulator, and output. Then rewrite the method solution from memory.
