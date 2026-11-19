# Lesson 11: JavaScript Arrays and Array Methods

**Course:** JavaScript Master Course  
**Unit:** 1 - Fundamentals  
**Level:** Beginner through Advanced  
**Previous:** [Lesson 10 - Arrow Functions and Advanced Function Concepts](10-arrow-functions-and-advanced-function-concepts.md)  
**Next:** [Lesson 12 - JavaScript Objects and Object Methods](12-javascript-objects-and-object-methods.md)

> This GitHub-ready lesson explains syntax and operators, shows expected output, traces execution, distinguishes mutation from copying, and provides ten interview questions and ten fully worked coding exercises. All diagrams and punctuation are ASCII-only.

## Table of Contents

1. [What is an array?](#1-what-is-an-array)
2. [Indexes and element access](#2-indexes-and-element-access)
3. [The length property and sparse arrays](#3-the-length-property-and-sparse-arrays)
4. [Updating elements and const](#4-updating-elements-and-const)
5. [push, pop, shift and unshift](#5-push-pop-shift-and-unshift)
6. [slice versus splice](#6-slice-versus-splice)
7. [Traversing arrays](#7-traversing-arrays)
8. [Searching: includes, indexOf, find and findIndex](#8-searching-includes-indexof-find-and-findindex)
9. [Predicates: some and every](#9-predicates-some-and-every)
10. [Transformation with map](#10-transformation-with-map)
11. [Selection with filter](#11-selection-with-filter)
12. [Aggregation with reduce](#12-aggregation-with-reduce)
13. [Sorting and reversing](#13-sorting-and-reversing)
14. [Nested arrays](#14-nested-arrays)
15. [Destructuring, rest and spread](#15-destructuring-rest-and-spread)
16. [References, shallow copies and deep copies](#16-references-shallow-copies-and-deep-copies)
17. [Advanced array methods](#17-advanced-array-methods)
18. [Performance and edge cases](#18-performance-and-edge-cases)
19. [Worked sales-analysis project](#19-worked-sales-analysis-project)
20. [Ten interview questions with answers](#20-ten-interview-questions-with-answers)
21. [Ten coding exercises with full solutions](#21-ten-coding-exercises-with-full-solutions)
22. [Common mistakes](#22-common-mistakes)
23. [Summary and navigation](#23-summary-and-navigation)

---

## 1. What is an array?

An array is a JavaScript object that holds an ordered collection of values. Values can be numbers, strings, booleans, objects, functions, or other arrays.

```javascript
const scores = [80, 75, 90, 85];
console.log(scores);
// [80, 75, 90, 85]
```

**Token-by-token:** `const` declares a binding that cannot be reassigned; `scores` is the identifier; `=` assigns a value; `[` and `]` delimit the array literal; numbers are elements; commas separate elements; `;` ends the declaration. `console.log` prints the array.

Without an array you might write four independent variables. With an array, a loop or method can process all scores using one variable.

Arrays can contain mixed types, though consistent element types are often easier to maintain:

```javascript
const mixed = [10, "Apple", true, null, { id: 1 }];
console.log(Array.isArray(mixed)); // true
console.log(typeof mixed);        // "object"
```

`Array.isArray(value)` is the appropriate built-in test for arrays; `typeof` reports `"object"` for arrays.

## 2. Indexes and element access

JavaScript uses **zero-based indexes**:

```text
Values:  [80] [75] [90] [85]
Indexes:   0    1    2    3
```

```javascript
const scores = [80, 75, 90, 85];
console.log(scores[0]); // 80
console.log(scores[2]); // 90
console.log(scores[9]); // undefined
```

**Expression breakdown: `scores[2]`**

- `scores`: the array reference.
- `[`: begins computed property access.
- `2`: the index to read.
- `]`: closes property access.
- JavaScript returns the value stored at index `2`, which is `90`.

**Execution trace:**

| Step | Expression | Result |
|---|---|---|
| 1 | `scores[0]` | `80` |
| 2 | `scores[2]` | `90` |
| 3 | `scores[9]` | `undefined` because that property is absent |

For a negative index, bracket access is **not** relative to the end: `scores[-1]` is generally `undefined`. Use `scores.at(-1)` to retrieve the last element.

## 3. The length property and sparse arrays

```javascript
const fruits = ["Apple", "Banana", "Mango"];
console.log(fruits.length);               // 3
console.log(fruits[fruits.length - 1]);   // Mango
console.log(fruits.at(-1));               // Mango
```

**Breakdown of `fruits[fruits.length - 1]`:**

1. `fruits.length` reads the array's `length` property and evaluates to `3`.
2. `-` is subtraction; `3 - 1` evaluates to `2`.
3. `fruits[2]` reads the third element, `"Mango"`.
4. The dot in `fruits.length` accesses a named property; the square brackets access a property using a computed expression.

**Important edge case:** `length` is one greater than the highest present array index, not necessarily the count of present elements.

```javascript
const sparse = [];
sparse[4] = "X";
console.log(sparse.length); // 5
console.log(0 in sparse);   // false
console.log(4 in sparse);   // true
```

Here, indices `0` through `3` are holes. `in` checks whether the indexed property exists. Assigning a smaller length truncates elements; assigning a larger length creates holes.

## 4. Updating elements and const

```javascript
const fruits = ["Apple", "Banana", "Mango"];
fruits[1] = "Orange";
console.log(fruits); // ["Apple", "Orange", "Mango"]
```

`fruits[1]` identifies the second slot; `=` assigns `"Orange"` to that slot. `const` prevents rebinding `fruits`, not changing the contents of the referenced array.

```javascript
const numbers = [10, 20];
numbers[0] = 99;   // Allowed
// numbers = [1, 2]; // TypeError: assignment to constant binding
```

**Trace:** initial array `[10, 20]` --> assign `99` at index `0` --> `[99, 20]`.

## 5. push, pop, shift and unshift

These four methods mutate the original array.

| Method | Action | Return value | Typical cost |
|---|---|---|---|
| `push(x)` | Append `x` | New length | Amortized O(1) |
| `pop()` | Remove last | Removed value or `undefined` | O(1) |
| `unshift(x)` | Prepend `x` | New length | O(n) |
| `shift()` | Remove first | Removed value or `undefined` | O(n) |

```javascript
const items = ["Pen", "Notebook"];
console.log(items.push("Bag")); // 3
console.log(items);             // ["Pen", "Notebook", "Bag"]
console.log(items.pop());       // Bag
console.log(items);             // ["Pen", "Notebook"]
console.log(items.unshift("Eraser")); // 3
console.log(items);             // ["Eraser", "Pen", "Notebook"]
console.log(items.shift());     // Eraser
console.log(items);             // ["Pen", "Notebook"]
```

**Trace:**

| Operation | Array after operation | Return value |
|---|---|---|
| Start | `["Pen", "Notebook"]` | - |
| `push("Bag")` | `["Pen", "Notebook", "Bag"]` | `3` |
| `pop()` | `["Pen", "Notebook"]` | `"Bag"` |
| `unshift("Eraser")` | `["Eraser", "Pen", "Notebook"]` | `3` |
| `shift()` | `["Pen", "Notebook"]` | `"Eraser"` |

Do not write `const updated = items.push("Bag")` expecting an array: `updated` will be a number (the new length).

## 6. slice versus splice

### slice: copy a range without mutating the source

```javascript
const numbers = [10, 20, 30, 40, 50];
const selected = numbers.slice(1, 4);
console.log(selected); // [20, 30, 40]
console.log(numbers);  // [10, 20, 30, 40, 50]
```

`slice(start, end)` includes `start` and excludes `end`. `slice(1, 4)` reads indices `1`, `2`, and `3`. It returns a shallow copy of those values.

### splice: remove or insert in place

```javascript
const numbers = [10, 20, 30, 40, 50];
const removed = numbers.splice(1, 2);
console.log(removed); // [20, 30]
console.log(numbers); // [10, 40, 50]
```

`splice(start, deleteCount, ...newItems)` mutates the original and returns an array of removed elements.

```javascript
const values = [10, 40];
values.splice(1, 0, 20, 30);
console.log(values); // [10, 20, 30, 40]
```

**Breakdown:** `1` is insertion index; `0` means delete no elements; `20` and `30` are inserted there. The original `[10, 40]` becomes `[10, 20, 30, 40]`.

## 7. Traversing arrays

### Indexed for loop

```javascript
const numbers = [10, 20, 30];
for (let i = 0; i < numbers.length; i++) {
  console.log(i, numbers[i]);
}
// 0 10
// 1 20
// 2 30
```

`let i = 0` initializes the counter; `i < numbers.length` tests whether the index is within range; `i++` increments the counter after each iteration; `numbers[i]` retrieves the current element.

### for...of

```javascript
for (const number of numbers) {
  console.log(number);
}
// 10
// 20
// 30
```

`of` iterates values rather than index keys. In a sparse array, array iteration yields `undefined` for holes.

### forEach callback

```javascript
numbers.forEach((number, index) => {
  console.log(index, number);
});
// 0 10
// 1 20
// 2 30
```

The callback parameters are `(currentValue, index, array)`; the latter two are optional to declare. `forEach` returns `undefined`, and a `return` inside its callback does not terminate the whole iteration. For early termination, use `for`, `for...of`, `find`, `some`, or `every` as appropriate.

## 8. Searching: includes, indexOf, find and findIndex

```javascript
const numbers = [5, 12, 8, 20];
console.log(numbers.includes(12));               // true
console.log(numbers.indexOf(12));                // 1
console.log(numbers.indexOf(99));                // -1
console.log(numbers.find(n => n > 10));          // 12
console.log(numbers.findIndex(n => n > 10));     // 1
```

- `includes(value)` checks whether a value is present using SameValueZero equality; it can find `NaN`.
- `indexOf(value)` finds the first matching index using strict equality; it cannot find `NaN`.
- `find(callback)` returns the first matching element or `undefined`.
- `findIndex(callback)` returns the index of the first match or `-1`.

**Arrow expression breakdown: `n => n > 10`:** `n` is the current element; `=>` introduces an expression-body arrow function; `>` is the greater-than operator; `10` is the comparison operand; the boolean result is returned implicitly.

**Trace for `find`:**

| Callback invocation | `n` | `n > 10` | Action |
|---|---|---|---|
| 1 | 5 | false | Continue |
| 2 | 12 | true | Return `12`; stop |

For arrays of objects, `includes({id:1})` does not find a separately created object with the same properties; object comparisons use reference identity. Use `find(item => item.id === 1)`.

## 9. Predicates: some and every

```javascript
const scores = [65, 70, 80];
console.log(scores.some(score => score >= 75));  // true
console.log(scores.every(score => score >= 50)); // true
```

`some` asks whether **at least one** element passes. `every` asks whether **all** elements pass. Both can stop early when the answer is determined.

**Breakdown:** `score >= 75` uses `>=` (greater than or equal to) and evaluates to a boolean. `some` returns true when it first sees true.

**Empty-array rules:** `[].some(() => true)` returns `false`; `[].every(() => false)` returns `true` (there is no counterexample).

## 10. Transformation with map

```javascript
const prices = [100, 200, 300];
const doubled = prices.map(price => price * 2);
console.log(doubled); // [200, 400, 600]
console.log(prices);  // [100, 200, 300]
```

**Breakdown:** `prices` is the source array; `.` accesses its method; `map(...)` calls the method; `price` is the callback's current element; `=>` introduces the callback body; `*` multiplies by `2`; each result becomes the corresponding element of the new array.

| Iteration | Current `price` | `price * 2` | New result so far |
|---|---|---|---|
| 1 | 100 | 200 | `[200]` |
| 2 | 200 | 400 | `[200, 400]` |
| 3 | 300 | 600 | `[200, 400, 600]` |

**Important:** With curly braces, add `return`:

```javascript
const correct = prices.map(price => { return price * 2; });
const wrong = prices.map(price => { price * 2; });
console.log(correct); // [200, 400, 600]
console.log(wrong);   // [undefined, undefined, undefined]
```

`map` skips holes in sparse arrays and preserves those holes in the output.

## 11. Selection with filter

```javascript
const numbers = [5, 10, 15, 20, 25];
const large = numbers.filter(number => number > 12);
console.log(large); // [15, 20, 25]
```

The callback returns a truthy or falsy value. `filter` retains an element when the callback result is truthy. It creates a new array.

| Element | Condition `number > 12` | Included? |
|---|---|---|
| 5 | false | No |
| 10 | false | No |
| 15 | true | Yes |
| 20 | true | Yes |
| 25 | true | Yes |

Objects example:

```javascript
const products = [
  { name: "Pen", price: 50 },
  { name: "Notebook", price: 250 },
  { name: "Bag", price: 1500 }
];
const expensive = products.filter(product => product.price >= 200);
console.log(expensive.map(product => product.name));
// ["Notebook", "Bag"]
```

`product.price` reads the `price` property of the current product object; `>=` includes prices equal to the threshold. The second `map` extracts names from the filtered objects.

## 12. Aggregation with reduce

```javascript
const numbers = [10, 20, 30, 40];
const total = numbers.reduce((sum, number) => sum + number, 0);
console.log(total); // 100
```

**Token-by-token for `reduce((sum, number) => sum + number, 0)`:**

- `reduce`: method that folds elements into one accumulated value.
- First `(`: opens the method call.
- `(sum, number)`: callback parameters, accumulator first and current element second.
- `=>`: introduces the callback body.
- `sum + number`: adds current element to accumulator; `+` is addition here because both operands are numbers.
- `, 0`: separates callback from the initial accumulator value `0`.
- Final `)`: closes the method call.

| Iteration | Accumulator before | Element | Returned accumulator |
|---|---|---|---|
| Start | 0 | - | 0 |
| 1 | 0 | 10 | 10 |
| 2 | 10 | 20 | 30 |
| 3 | 30 | 30 | 60 |
| 4 | 60 | 40 | 100 |

**Equivalent for...of loop:**

```javascript
let totalWithLoop = 0;
for (const number of numbers) {
  totalWithLoop = totalWithLoop + number;
}
console.log(totalWithLoop); // 100
```

Without an initial value, `reduce` uses the first present element as the accumulator. On an empty array with no initial value, it throws `TypeError`. With an initial value, an empty array returns that initial value.

## 13. Sorting and reversing

```javascript
const numbers = [100, 5, 20, 3];
console.log([...numbers].sort());              // [100, 20, 3, 5]
console.log([...numbers].sort((a, b) => a - b)); // [3, 5, 20, 100]
console.log([...numbers].sort((a, b) => b - a)); // [100, 20, 5, 3]
```

Default `sort()` compares string representations lexicographically, so numerical order requires a comparator.

**Comparator breakdown:** `(a, b) => a - b` receives two elements. `a - b` returns a negative number when `a` should come before `b`, a positive number when `b` should come first, and zero when they compare equal. `b - a` reverses numerical order.

`sort()` and `reverse()` mutate their receiver. Modern alternatives `toSorted()` and `toReversed()` create new arrays:

```javascript
const original = [30, 10, 20];
const sorted = original.toSorted((a, b) => a - b);
const reversed = original.toReversed();
console.log(original); // [30, 10, 20]
console.log(sorted);   // [10, 20, 30]
console.log(reversed); // [20, 10, 30]
```

These copying methods require a modern JavaScript runtime. In older environments, `[...original].sort(...)` and `[...original].reverse()` work for ordinary arrays.

## 14. Nested arrays

```javascript
const matrix = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];
console.log(matrix[1][2]); // 6
```

**Trace:** `matrix[1]` returns `[4, 5, 6]`; then `[2]` retrieves its third element, `6`.

```text
         Column 0  Column 1  Column 2
Row 0       1         2         3
Row 1       4         5        [6] <-- matrix[1][2]
Row 2       7         8         9
```

To visit every value:

```javascript
for (const row of matrix) {
  for (const value of row) {
    console.log(value);
  }
}
// Prints 1, 2, 3, 4, 5, 6, 7, 8, 9 on separate lines
```

The outer loop visits each inner array; the inner loop visits each number in that array.

## 15. Destructuring, rest and spread

```javascript
const colors = ["Red", "Green", "Blue"];
const [first, second, third] = colors;
console.log(first, second, third); // Red Green Blue
```

The left-hand `[...]` is a destructuring pattern, not a new array literal. Each variable receives the value at the matching position.

```javascript
const [firstColor, , thirdColor] = colors;
console.log(firstColor, thirdColor); // Red Blue
```

The empty position between commas skips index `1`.

```javascript
const numbers = [10, 20, 30, 40];
const [head, ...tail] = numbers;
console.log(head); // 10
console.log(tail); // [20, 30, 40]
```

Here `...tail` is a **rest element** collecting the remaining values. In `[...numbers]`, the same three-dot token is **spread syntax** expanding iterable values into a new array.

```javascript
const copy = [...numbers];
const combined = [...numbers, 50, 60];
console.log(copy);     // [10, 20, 30, 40]
console.log(combined); // [10, 20, 30, 40, 50, 60]
```

## 16. References, shallow copies and deep copies

### Assignment does not copy an array

```javascript
const original = [10, 20, 30];
const alias = original;
alias[0] = 99;
console.log(original); // [99, 20, 30]
```

Both variables contain a reference to the same array object.

### Spread creates a new outer array

```javascript
const original = [10, 20, 30];
const copy = [...original];
copy[0] = 99;
console.log(original); // [10, 20, 30]
console.log(copy);     // [99, 20, 30]
```

### But the copy is shallow

```javascript
const original = [{ name: "Alice", score: 80 }];
const copy = [...original];
copy[0].score = 95;
console.log(original[0].score); // 95
```

The outer arrays differ, but `original[0]` and `copy[0]` reference the same nested object. For supported structured-cloneable data, `structuredClone(original)` can deep-copy nested structures. It does not clone arbitrary functions or all possible object types.

**Reference diagram:**

```text
original ----> [ object reference ] ----> { name: "Alice", score: 95 }
copy     ----> [ object reference ] -----^
```

## 17. Advanced array methods

| Method | What it does | Mutates receiver? |
|---|---|---|
| `at(index)` | Reads element, supports negative indexes | No |
| `flat(depth)` | Flattens nested arrays | No |
| `flatMap(callback)` | Maps then flattens one level | No |
| `fill(value)` | Fills elements in place | Yes |
| `copyWithin(...)` | Copies elements within same array | Yes |
| `toReversed()` | Returns reversed copy | No |
| `toSorted(compareFn)` | Returns sorted copy | No |
| `toSpliced(...)` | Returns array with splice-like change | No |
| `with(index, value)` | Returns copy with one replaced element | No |
| `findLast(callback)` | Last matching value | No |
| `findLastIndex(callback)` | Last matching index | No |
| `Array.from(source)` | Creates array from iterable/array-like input | N/A |
| `Array.isArray(value)` | Tests whether value is an array | No |

```javascript
console.log([10, 20, 30].at(-1));           // 30
console.log([1, [2, 3], [4]].flat());       // [1, 2, 3, 4]
console.log(["Hi there", "Learn JS"].flatMap(s => s.split(" ")));
// ["Hi", "there", "Learn", "JS"]
console.log(Array.from("ABC"));            // ["A", "B", "C"]
console.log(Array.isArray([]));             // true
```

**`flatMap` breakdown:** `s` is the current sentence; `.split(" ")` creates an array of words separated by spaces; `flatMap` merges the resulting word arrays one level deep.

## 18. Performance and edge cases

These are typical complexities for ordinary dense arrays; JavaScript engines can optimize differently.

| Operation | Typical time |
|---|---|
| Read by index | O(1) |
| `push`, `pop` | Amortized O(1), O(1) respectively |
| `shift`, `unshift` | O(n) |
| `includes`, `find`, `filter`, `map`, `reduce` | O(n) |
| `slice` | O(k) for k copied elements |
| Sorting | Often O(n log n), implementation-dependent |

**Edge cases to remember:**

1. `[]` is truthy even though it is empty.
2. `[] === []` is false because they are different objects.
3. `Array(3)` creates an array of length 3 with holes, not `[3]`.
4. `delete arr[1]` leaves a hole; `splice(1, 1)` shifts remaining elements.
5. `map` and `forEach` generally skip holes; `for...of` yields `undefined` for holes.
6. `sort()` mutates; `toSorted()` does not.
7. `includes(NaN)` can be true, while `indexOf(NaN)` is `-1`.
8. `reduce()` without an initial value fails on an empty array.
9. `every()` on an empty array is true; `some()` on an empty array is false.
10. `const` does not freeze the contents of an array.

## 19. Worked sales-analysis project

**Task:** Calculate revenue, unit count, average revenue per unit, highest-revenue product, and products with revenue greater than 10,000.

```javascript
const sales = [
  { product: "Laptop", price: 50000, quantity: 2 },
  { product: "Mouse", price: 1500, quantity: 5 },
  { product: "Keyboard", price: 3500, quantity: 3 },
  { product: "Monitor", price: 20000, quantity: 2 }
];

const totalRevenue = sales.reduce(
  (total, item) => total + item.price * item.quantity,
  0
);

const totalUnits = sales.reduce(
  (total, item) => total + item.quantity,
  0
);

const averageRevenuePerUnit =
  totalUnits === 0 ? 0 : totalRevenue / totalUnits;

const highestRevenueProduct = sales.reduce((highest, item) => {
  const itemRevenue = item.price * item.quantity;
  if (highest === null) return item;
  const highestRevenue = highest.price * highest.quantity;
  return itemRevenue > highestRevenue ? item : highest;
}, null);

const highRevenueProducts = sales.filter(
  item => item.price * item.quantity > 10000
);

console.log("Total revenue:", totalRevenue);
console.log("Total units:", totalUnits);
console.log("Average revenue per unit:", averageRevenuePerUnit.toFixed(2));
console.log("Highest-revenue product:", highestRevenueProduct?.product);
console.log("Products above 10000:", highRevenueProducts.map(item => item.product));
```

Expected output:

```text
Total revenue: 158000
Total units: 12
Average revenue per unit: 13166.67
Highest-revenue product: Laptop
Products above 10000: [ 'Laptop', 'Keyboard', 'Monitor' ]
```

**Revenue trace:**

| Product | `price * quantity` | Running total |
|---|---|---|
| Laptop | `50000 * 2 = 100000` | 100000 |
| Mouse | `1500 * 5 = 7500` | 107500 |
| Keyboard | `3500 * 3 = 10500` | 118000 |
| Monitor | `20000 * 2 = 40000` | 158000 |

**Unit trace:** `0 + 2 = 2` --> `2 + 5 = 7` --> `7 + 3 = 10` --> `10 + 2 = 12`.

**Average breakdown:** `totalUnits === 0` checks whether the divisor is zero; `? 0 :` is the ternary operator: choose `0` if true, otherwise evaluate `totalRevenue / totalUnits`. Here `158000 / 12 = 13166.666...`; `.toFixed(2)` formats two decimal places and returns the string `"13166.67"`.

**Highest-product breakdown:** `highest` is the current best object or `null`; `item` is the current sale; `item.price * item.quantity` calculates the current revenue; the `if` handles the first element; `itemRevenue > highestRevenue ? item : highest` selects the better of two candidates. The initial `null` makes empty input safe. `highestRevenueProduct?.product` uses optional chaining so an empty array does not cause an error when reading `.product`.

**Filtering breakdown:** `item => item.price * item.quantity > 10000` returns true only for items whose revenue exceeds 10,000; `map(item => item.product)` then extracts the product names.

## 20. Ten interview questions with answers

### Q1. What is the first index of a JavaScript array?

**Answer:** `0`. For `const a = [10, 20]`, `a[0]` is `10`, and `a[1]` is `20`. Indexing begins at zero, not one.

### Q2. Which method adds an element to the end, and what does it return?

**Answer:** `push()` appends an element and returns the new length. Example: `const a = [1]; console.log(a.push(2));` prints `2`; the array becomes `[1, 2]`.

### Q3. What does `[10, 20, 30].at(-1)` return?

**Answer:** `30`. `at(-1)` counts backward from the end; ordinary bracket access `array[-1]` does not.

### Q4. How do `map()` and `forEach()` differ?

**Answer:** `map()` returns a new array of callback results. `forEach()` returns `undefined` and is mainly used for side effects. Example: `[1, 2].map(x => x * 2)` returns `[2, 4]`.

### Q5. What does `filter()` return?

**Answer:** A new array of elements whose callback results are truthy. `[1, 4, 7].filter(n => n > 3)` returns `[4, 7]`.

### Q6. What does `reduce()` do, and why use an initial value?

**Answer:** It combines elements into an accumulator. An explicit initial value defines the starting state and allows empty arrays to return that initial value rather than throw. `[1, 2, 3].reduce((s, n) => s + n, 0)` returns `6`.

### Q7. Does `sort()` modify the source array?

**Answer:** Yes. `sort()` mutates its receiver. Use `toSorted()` or `[...arr].sort(...)` to keep the original unchanged.

### Q8. What does `indexOf()` return if there is no match?

**Answer:** `-1`. For example `[1, 2].indexOf(9)` is `-1`, not `undefined`.

### Q9. What does `Array.isArray([])` return?

**Answer:** `true`. `typeof []` is `"object"`, so `Array.isArray()` is the correct specialized test.

### Q10. What happens when two variables reference the same array?

**Answer:** Mutations through one variable are visible through the other. `const a=[1]; const b=a; b.push(2);` makes `a` equal to `[1,2]` because both bindings refer to the same object.

## 21. Ten coding exercises with full solutions

Try each challenge before reading its model solution. Every solution includes the expected output and a manual trace or detailed explanation.

### Exercise 1: Find the largest number without Math.max

**Input:** `[12, 45, 7, 89, 23]`  
**Expected output:** `89`

```javascript
function largestNumber(numbers) {
  if (numbers.length === 0) return undefined;
  let largest = numbers[0];
  for (const number of numbers) {
    if (number > largest) {
      largest = number;
    }
  }
  return largest;
}
console.log(largestNumber([12, 45, 7, 89, 23])); // 89
```

**Line-by-line:** `numbers.length === 0` checks for an empty array; `return undefined` handles that case. `let largest = numbers[0]` starts with the first number. `for (const number of numbers)` visits every element. `number > largest` compares the current candidate with the best so far. `largest = number` updates the best. `return largest` sends the result back.

| Visited number | Largest before | Condition | Largest after |
|---|---|---|---|
| 12 | 12 | false | 12 |
| 45 | 12 | true | 45 |
| 7 | 45 | false | 45 |
| 89 | 45 | true | 89 |
| 23 | 89 | false | 89 |

**Alternative:** `numbers.reduce((max, n) => n > max ? n : max)` for nonempty arrays. The ternary `condition ? a : b` returns `a` when true, otherwise `b`.

### Exercise 2: Remove duplicates

**Input:** `[1, 2, 2, 3, 4, 4, 5]`  
**Expected output:** `[1, 2, 3, 4, 5]`

```javascript
const numbers = [1, 2, 2, 3, 4, 4, 5];
const unique = numbers.filter((number, index, array) => {
  return array.indexOf(number) === index;
});
console.log(unique); // [1, 2, 3, 4, 5]
```

**Breakdown:** `filter` calls the callback with `number` (current value), `index` (current position), and `array` (source). `array.indexOf(number)` finds the first occurrence of the current value. `===` checks strict equality. Only the first occurrence has a first-occurrence index equal to its current index.

| Index | Number | `indexOf(number)` | Keep? |
|---|---|---|---|
| 0 | 1 | 0 | Yes |
| 1 | 2 | 1 | Yes |
| 2 | 2 | 1 | No |
| 3 | 3 | 3 | Yes |
| 4 | 4 | 4 | Yes |
| 5 | 4 | 4 | No |
| 6 | 5 | 6 | Yes |

**Alternative:** `const unique = [...new Set(numbers)];` `Set` stores unique values using SameValueZero equality, and spread converts the set back into an array. Unlike the `indexOf` approach, it also deduplicates `NaN`.

### Exercise 3: Count occurrences using reduce

**Input:** `['Apple','Banana','Apple','Mango','Banana','Apple']`  
**Expected:** `{ Apple: 3, Banana: 2, Mango: 1 }`

```javascript
const fruits = ["Apple", "Banana", "Apple", "Mango", "Banana", "Apple"];
const counts = fruits.reduce((result, fruit) => {
  result[fruit] = (result[fruit] ?? 0) + 1;
  return result;
}, {});
console.log(counts); // { Apple: 3, Banana: 2, Mango: 1 }
```

**Breakdown:** `{}` is the initial accumulator object. `result[fruit]` uses the current fruit string as a property key. `?? 0` is nullish coalescing: use zero only if the current count is `null` or `undefined`. `+ 1` increments the count. `return result` passes the updated object to the next iteration. This solution mutates the accumulator object, not the source array.

**Trace:**

```text
Start:  {}
Apple:  { Apple: 1 }
Banana: { Apple: 1, Banana: 1 }
Apple:  { Apple: 2, Banana: 1 }
Mango:  { Apple: 2, Banana: 1, Mango: 1 }
Banana: { Apple: 2, Banana: 2, Mango: 1 }
Apple:  { Apple: 3, Banana: 2, Mango: 1 }
```

For untrusted arbitrary strings, prefer `new Map()` or `Object.create(null)` to avoid special object property names such as `__proto__`.

### Exercise 4: Flatten a nested array in two ways

**Input:** `[[1,2],[3,4],[5,6]]`  
**Expected:** `[1,2,3,4,5,6]`

```javascript
const nested = [[1, 2], [3, 4], [5, 6]];
const flatWithMethod = nested.flat();
console.log(flatWithMethod); // [1, 2, 3, 4, 5, 6]

const flatWithLoop = [];
for (const group of nested) {
  for (const number of group) {
    flatWithLoop.push(number);
  }
}
console.log(flatWithLoop); // [1, 2, 3, 4, 5, 6]
```

**Breakdown:** `.flat()` merges one level of nested arrays by default. In the loop version, `group` is each inner array; `number` is each value inside it; `push(number)` appends that value to the output.

**Trace:** visit `[1,2]` --> `[1,2]`; visit `[3,4]` --> `[1,2,3,4]`; visit `[5,6]` --> `[1,2,3,4,5,6]`.

### Exercise 5: Sort students by score without mutation

**Input:** Alice 85, Bob 95, Carol 75  
**Expected order:** Bob, Alice, Carol

```javascript
const students = [
  { name: "Alice", score: 85 },
  { name: "Bob", score: 95 },
  { name: "Carol", score: 75 }
];
const ranked = students.toSorted((a, b) => b.score - a.score);
console.log(ranked.map(student => student.name)); // ["Bob", "Alice", "Carol"]
console.log(students[0].name); // Alice (original unchanged)
```

**Breakdown:** `toSorted` returns a new array. The comparator reads `a.score` and `b.score`; `b.score - a.score` places larger scores first. `map(student => student.name)` extracts the ordered names. **Older runtime alternative:** `[...students].sort((a, b) => b.score - a.score)`.

**Trace of final order:** scores `95 > 85 > 75` --> names `Bob, Alice, Carol`. Sort algorithms may compare pairs in different internal sequences; only the final ordering is guaranteed by this comparator.

### Exercise 6: Calculate an average safely

**Input:** `[10,20,30,40]`  
**Expected:** `25`; empty array returns `0` by this function's chosen contract.

```javascript
function average(numbers) {
  if (numbers.length === 0) return 0;
  const total = numbers.reduce((sum, number) => sum + number, 0);
  return total / numbers.length;
}
console.log(average([10, 20, 30, 40])); // 25
console.log(average([])); // 0
```

**Breakdown:** `numbers.length === 0` checks for empty input. `reduce` accumulates a sum from zero. `/` divides the sum by the count. `return` gives the computed average to the caller.

**Trace:** `0 + 10 = 10` --> `10 + 20 = 30` --> `30 + 30 = 60` --> `60 + 40 = 100` --> `100 / 4 = 25`.

### Exercise 7: Second-largest distinct value

**Input:** `[10,50,20,50,40]`  
**Expected:** `40`

```javascript
function secondLargest(numbers) {
  const distinct = [...new Set(numbers)];
  if (distinct.length < 2) return undefined;
  distinct.sort((a, b) => b - a);
  return distinct[1];
}
console.log(secondLargest([10, 50, 20, 50, 40])); // 40
console.log(secondLargest([7, 7])); // undefined
```

**Breakdown:** `new Set(numbers)` removes duplicate values; `...` spreads unique values into a new array; `< 2` checks whether fewer than two distinct values exist; `sort((a,b) => b-a)` sorts descending; `[1]` reads the second element.

**Trace:** `[10,50,20,50,40]` --> distinct `[10,50,20,40]` --> sorted `[50,40,20,10]` --> index `1` is `40`.

This assumes finite numeric inputs; a production version should validate inputs and decide how to handle `NaN`.

### Exercise 8: Group products by category

**Input:** Laptop/Electronics, Shirt/Clothing, Phone/Electronics, Shoes/Clothing.

```javascript
const products = [
  { name: "Laptop", category: "Electronics" },
  { name: "Shirt", category: "Clothing" },
  { name: "Phone", category: "Electronics" },
  { name: "Shoes", category: "Clothing" }
];
const grouped = products.reduce((groups, product) => {
  const category = product.category;
  if (!Object.hasOwn(groups, category)) groups[category] = [];
  groups[category].push(product);
  return groups;
}, Object.create(null));
console.log(grouped.Electronics.map(p => p.name)); // ["Laptop", "Phone"]
console.log(grouped.Clothing.map(p => p.name));    // ["Shirt", "Shoes"]
```

**Breakdown:** `Object.create(null)` creates a dictionary without inherited keys. `category = product.category` reads the current product's category. `Object.hasOwn(groups, category)` tests whether the dictionary already has that category. `!` negates the boolean; if the category is missing, `groups[category] = []` initializes its array. `push(product)` appends the product to that category's array. `return groups` keeps the accumulator for the next iteration.

**Trace:** Laptop --> Electronics `[Laptop]`; Shirt --> Clothing `[Shirt]`; Phone --> Electronics `[Laptop,Phone]`; Shoes --> Clothing `[Shirt,Shoes]`.

### Exercise 9: Find common elements

**Input:** `[1,2,3,4,5]` and `[3,4,5,6,7]`  
**Expected:** `[3,4,5]`

```javascript
const first = [1, 2, 3, 4, 5];
const second = [3, 4, 5, 6, 7];
const common = first.filter(number => second.includes(number));
console.log(common); // [3, 4, 5]
```

**Breakdown:** `filter` examines each `number` in `first`; `second.includes(number)` returns a boolean indicating whether the second array contains it; matching numbers are retained. `filter` preserves the first array's order and, if the first array has duplicates, retains matching duplicates.

**Trace:** 1 absent --> skip; 2 absent --> skip; 3 present --> keep; 4 present --> keep; 5 present --> keep.

**Optimization:** For large arrays, create `const lookup = new Set(second)` and use `lookup.has(number)` inside `filter` to avoid a linear scan of `second` for each element.

### Exercise 10: Analyze banking transactions

**Input:**

```javascript
const transactions = [
  { id: 1, type: "deposit", amount: 5000 },
  { id: 2, type: "withdrawal", amount: 1500 },
  { id: 3, type: "deposit", amount: 3000 },
  { id: 4, type: "withdrawal", amount: 2000 },
  { id: 5, type: "deposit", amount: 4500 }
];
```

**Requirements:** total deposits, total withdrawals, net amount, largest transaction, deposit count, transactions above 2,500, and a summary object.

```javascript
function analyzeTransactions(transactions) {
  const deposits = transactions.filter(t => t.type === "deposit");
  const withdrawals = transactions.filter(t => t.type === "withdrawal");

  const totalDeposits = deposits.reduce((sum, t) => sum + t.amount, 0);
  const totalWithdrawals = withdrawals.reduce((sum, t) => sum + t.amount, 0);
  const netAmount = totalDeposits - totalWithdrawals;

  const largestTransaction = transactions.reduce((largest, t) => {
    if (largest === null || t.amount > largest.amount) return t;
    return largest;
  }, null);

  const above2500 = transactions.filter(t => t.amount > 2500);

  return {
    totalDeposits,
    totalWithdrawals,
    netAmount,
    largestTransaction,
    depositCount: deposits.length,
    above2500Count: above2500.length,
    above2500
  };
}

const report = analyzeTransactions(transactions);
console.log(report.totalDeposits);             // 12500
console.log(report.totalWithdrawals);          // 3500
console.log(report.netAmount);                 // 9000
console.log(report.largestTransaction.amount); // 5000
console.log(report.depositCount);              // 3
console.log(report.above2500Count);            // 3
```

**Line-by-line concepts:**

1. `filter(t => t.type === "deposit")` selects only deposit objects. `===` is strict equality.
2. The second `filter` selects withdrawals.
3. `reduce((sum,t) => sum + t.amount, 0)` sums amounts starting from zero.
4. `totalDeposits - totalWithdrawals` subtracts withdrawals from deposits to calculate the net change.
5. The `largestTransaction` reduction starts with `null`; `||` is logical OR, so it returns the current transaction when there is no best candidate yet **or** the current amount exceeds the best amount.
6. `filter(t => t.amount > 2500)` selects transactions strictly greater than 2,500.
7. `.length` counts selected items.
8. `return { ... }` constructs the summary object. `totalDeposits` is shorthand for `totalDeposits: totalDeposits`.

**Manual trace:**

| ID | Type | Amount | Deposit total | Withdrawal total |
|---|---|---|---|---|
| 1 | deposit | 5000 | 5000 | 0 |
| 2 | withdrawal | 1500 | 5000 | 1500 |
| 3 | deposit | 3000 | 8000 | 1500 |
| 4 | withdrawal | 2000 | 8000 | 3500 |
| 5 | deposit | 4500 | 12500 | 3500 |

Net: `12500 - 3500 = 9000`. Largest amount: `5000`. Deposit count: `3`. Amounts strictly above 2,500: `5000`, `3000`, `4500` --> `3` transactions.

**Edge case:** Empty input returns zero totals, zero counts, an empty `above2500` array, and `largestTransaction: null`. Real banking systems should use validated integer minor currency units or appropriate decimal arithmetic rather than arbitrary floating-point values for monetary accounting.

## 22. Common mistakes

### Mistake 1: Off-by-one index

```javascript
const a = [10, 20, 30];
console.log(a[1]); // 20, not 10
```

### Mistake 2: Treating push's return as the array

```javascript
const a = [1, 2];
const result = a.push(3);
console.log(result); // 3, the new length
console.log(a);      // [1, 2, 3]
```

### Mistake 3: Sorting numbers as strings

```javascript
console.log([100, 20, 3].sort()); // [100, 20, 3]
console.log([100, 20, 3].sort((a, b) => a - b)); // [3, 20, 100]
```

### Mistake 4: Expecting forEach to return transformed values

```javascript
const result = [1, 2, 3].forEach(n => n * 2);
console.log(result); // undefined
// Use map to obtain [2, 4, 6].
```

### Mistake 5: Forgetting shared references

```javascript
const a = [1];
const b = a;
b.push(2);
console.log(a); // [1, 2]
```

### Mistake 6: Using delete instead of splice

```javascript
const a = [10, 20, 30];
delete a[1];
console.log(a.length); // 3; index 1 is a hole
// Use a.splice(1, 1) when you need remaining elements to shift left.
```

### Mistake 7: Forgetting return inside map's block body

```javascript
console.log([1, 2].map(n => { n * 2; })); // [undefined, undefined]
console.log([1, 2].map(n => { return n * 2; })); // [2, 4]
```

### Mistake 8: Reducing an empty array without initial value

```javascript
// [].reduce((a, b) => a + b); // TypeError
console.log([].reduce((a, b) => a + b, 0)); // 0
```

### Mistake 9: Assuming spread deep-copies objects

```javascript
const a = [{ score: 1 }];
const b = [...a];
b[0].score = 2;
console.log(a[0].score); // 2
```

### Mistake 10: Confusing slice and splice

`slice(start, end)` returns a copied range without mutating the original. `splice(start, deleteCount, ...items)` modifies the original and returns removed elements.

## 23. Summary and navigation

| Goal | Common method |
|---|---|
| Add at end | `push` |
| Remove at end | `pop` |
| Extract a range | `slice` |
| Insert/remove in place | `splice` |
| Transform elements | `map` |
| Select elements | `filter` |
| Find one element | `find` |
| Check any/all | `some` / `every` |
| Combine values | `reduce` |
| Sort without mutation | `toSorted` |
| Flatten one level | `flat` |
| Check array type | `Array.isArray` |

**Before advancing, you should be able to:** explain every component of `numbers.reduce((sum, n) => sum + n, 0)`, distinguish `map` from `filter`, predict whether an operation mutates its receiver, and trace reference-sharing bugs.

**Previous lesson:** [Lesson 10 - Arrow Functions and Advanced Function Concepts](10-arrow-functions-and-advanced-function-concepts.md)  
**Next lesson:** [Lesson 12 - JavaScript Objects and Object Methods](12-javascript-objects-and-object-methods.md)
