# Chapter 02 - JavaScript Array Methods and Plain Loops

## Part 1 - Understanding iteration and building array operations

[Previous: JavaScript Arrays](javascript-arrays.md) | Next: Part 2 - Loop scope, callbacks and insertion/removal algorithms (forthcoming)

In the previous chapter, we learned how arrays store, organize and manage collections of values. Now we need to learn how to process those values.

```javascript
const temperatures = [24, 28, 31, 19, 26, 33, 22];
```

We might find the highest temperature, calculate the average, filter temperatures above 25, count hot days, convert Celsius to Fahrenheit, check whether any reading exceeds 30, sort readings, or find the longest sequence of hot days. Array methods solve many of these problems; plain loops allow us to develop algorithms, combine operations and solve problems without dedicated methods.

This is one continuous chapter, divided into parts only because of its length.

## Chapter table of contents

1. Iteration fundamentals
2. Plain loops and loop control
3. Callbacks and higher-order functions
4. Adding and removing elements
5. Copying, extracting and merging
6. Traversing arrays
7. Searching arrays
8. Filtering and partitioning
9. Transforming arrays
10. Counting and frequency analysis
11. Accumulation and reduction
12. Testing and validating elements
13. Sorting algorithms
14. Reversing and rearranging
15. Filling and copying within arrays
16. Flattening and nested traversal
17. String and array conversion
18. Immutable array methods
19. Static methods and array construction
20. Advanced method behavior
21. Combining methods and fusing loops
22. Algorithms beyond array methods
23. Performance and complexity
24. Common mistakes and debugging
25. Practical exercises and solutions
26. Complete capability reference

## Part 1 contents

- [Understanding iteration](#1-understanding-iteration)
- [Plain loops](#2-plain-loops)
- [Controlling loop execution](#3-controlling-loop-execution)
- [Fundamental loop problem-solving patterns](#4-fundamental-loop-problem-solving-patterns)
- [Combining multiple operations](#5-combining-multiple-operations-in-one-loop)
- [Choosing the appropriate loop](#6-choosing-the-appropriate-loop)
- [Practice exercises](#7-practice-exercises)
- [Coverage tracker](#coverage-tracker)

## 1. Understanding iteration

Iteration is the process of repeatedly performing an operation. When working with arrays, iteration commonly involves visiting elements one after another.

```javascript
const numbers = [10, 20, 30, 40, 50];
console.log(numbers[0]);
console.log(numbers[1]);
console.log(numbers[2]);
console.log(numbers[3]);
console.log(numbers[4]);
```

Instead of writing thousands of statements for a large array, use a loop:

```javascript
for (let i = 0; i < numbers.length; i++) {
    console.log(numbers[i]);
}
```

Both produce:

```text
10
20
30
40
50
```

```text
Index:  0    1    2    3    4
Value: [10] [20] [30] [40] [50]
        -->  -->  -->  -->  --> end
```

| Term | Meaning |
|---|---|
| Iteration | Repeatedly performing an operation |
| Iteration step | One repetition |
| Traversal | Visiting elements of a data structure |
| Loop | A construct that repeats instructions |
| Counter | Tracks repetitions or quantities |
| Iterator | Object that produces values through an iteration protocol |
| Iterable | Value supporting the iterable protocol |
| Accumulator | Stores a progressively calculated result |

Iteration does not require an array:

```javascript
for (let i = 1; i <= 5; i++) {
    console.log(i);
}
```

Output: `1, 2, 3, 4, 5` (on separate lines).

## 2. Plain loops

| Loop | Typical purpose |
|---|---|
| `for` | Counter-controlled repetition |
| `while` | Condition-controlled repetition |
| `do...while` | Execute at least once, then repeat conditionally |
| `for...of` | Traverse iterable values |
| `for...in` | Enumerate enumerable string properties |
| Nested loops | Repeat operations inside other loops |

Nested loops are not a separate statement.

### The for loop

```javascript
for (initialization; condition; update) {
    // Instructions to repeat
}
```

```javascript
for (let i = 0; i < 4; i++) {
    console.log(i);
}
```

Output: `0, 1, 2, 3` (on separate lines).

- `let i = 0`: declare a reassignable counter and initialize it once. `i` is a convention, not a requirement.
- `i < 4`: test before each iteration. A falsy condition terminates the loop.
- `i++`: increment after the body. In this update clause, `i += 1` and `i = i + 1` have the same effect, although expression return values may differ.
- `{ ... }`: the body containing repeated instructions.

```text
Initialize --> Condition -- false --> End
                  |
                 true
                  v
                 Body --> Update --+
                  ^                |
                  +-- Condition <--+
```

| Step | i | Condition | Output | After update |
|---:|---:|---|---:|---:|
| 1 | 0 | true | 0 | 1 |
| 2 | 1 | true | 1 | 2 |
| 3 | 2 | true | 2 | 3 |
| 4 | 3 | true | 3 | 4 |
| 5 | 4 | false | None | No update |

The condition is checked five times, but the body executes four times.

### Forward iteration

Display library books in original order.

```javascript
const books = ["The Hobbit", "Dune", "Foundation", "Dracula"];
for (let i = 0; i < books.length; i++) {
    console.log(`${i + 1}. ${books[i]}`);
}
```

```text
1. The Hobbit
2. Dune
3. Foundation
4. Dracula
```

`i` is the index; `books[i]` is the value. `i + 1` displays human-friendly numbering without changing `i`.

### Backward iteration

```javascript
const activities = ["Account created", "Profile updated", "Photo uploaded", "Password changed"];
for (let i = activities.length - 1; i >= 0; i--) {
    console.log(activities[i]);
}
```

```text
Password changed
Photo uploaded
Profile updated
Account created
```

| Component | Forward | Backward |
|---|---|---|
| Start | 0 | length - 1 |
| Condition | `i < length` | `i >= 0` |
| Update | `i++` | `i--` |

Backward traversal is useful for `lastIndexOf`, `findLast`, `findLastIndex`, `reduceRight`, reverse searching and in-place removal.

### Different step sizes

```javascript
const measurements = [12, 15, 18, 21, 24, 27, 30, 33, 36, 39, 42, 45];
for (let i = 0; i < measurements.length; i += 3) {
    console.log(`Index ${i}: ${measurements[i]}`);
}
```

```text
Index 0: 12
Index 3: 21
Index 6: 30
Index 9: 39
```

Strided iteration visits selected positions, useful for sampling and fixed-size groups.

```javascript
const selected = measurements.filter((value, index) => index % 3 === 0);
console.log(selected); // [12, 21, 30, 39]
```

The loop visits four indexes; `filter` evaluates twelve callbacks for this dense array.

### Iterating through a specific range

```javascript
const rainfall = [5, 12, 8, 20, 14, 3, 9];
for (let i = 2; i < 5; i++) {
    console.log(rainfall[i]);
}
// 8, 20, 14 (separate lines)
```

The start is inclusive and end exclusive, as with `slice` and `fill`.

```javascript
rainfall.slice(2, 5).forEach(value => console.log(value));
```

The loop avoids creating the intermediate slice.

Experiment with `[10,20,30,40,50,60,70,80]`: start 1, end 7, step 2 visits indexes 1, 3, 5 and values 20, 40, 60. Change the boundaries and predict the output.

### Generating sequences without an array

```javascript
const multiples = [];
for (let i = 1; i <= 10; i++) {
    multiples[multiples.length] = i * 7;
}
console.log(multiples);
// [7, 14, 21, 28, 35, 42, 49, 56, 63, 70]
```

Initially length is zero, so assignment writes index zero. Length grows automatically. Next assignment writes index one. This is the append operation behind many algorithms, equivalent here to `multiples.push(i * 7)`.

```javascript
function range(start, end, step = 1) {
    if (!Number.isSafeInteger(start) ||
        !Number.isSafeInteger(end) ||
        !Number.isSafeInteger(step) || step <= 0) {
        throw new RangeError("Invalid range arguments");
    }
    const result = [];
    for (let i = start; i <= end; i += step) {
        result[result.length] = i;
    }
    return result;
}
console.log(range(3, 8)); // [3, 4, 5, 6, 7, 8]
console.log(range(10, 30, 5)); // [10, 15, 20, 25, 30]
console.log(range(8, 3)); // []
```

Supports ascending integer ranges and positive steps, not descending or fractional ranges. Production code should enforce a maximum result size.

### Multiple counters

```javascript
const letters = ["A", "B", "C", "D", "E", "F"];
for (let left = 0, right = letters.length - 1;
     left < right;
     left++, right--) {
    console.log(letters[left], letters[right]);
}
```

```text
A F
B E
C D
```

The declaration creates two variables. The condition checks whether left precedes right. The comma operator evaluates the updates left to right.

| Iteration | left | right | Pair |
|---:|---:|---:|---|
| 1 | 0 | 5 | A, F |
| 2 | 1 | 4 | B, E |
| 3 | 2 | 3 | C, D |
| Stop | 3 | 2 | Condition false |

This is the two-pointer technique, useful for reversal, palindromes, pair searching and partitioning.

### Omitting clauses

```javascript
let i = 0;
for (; i < 3; i++) {
    console.log(i);
}
```

```javascript
for (let i = 0; i < 3;) {
    console.log(i);
    i++;
}
```

```javascript
for (let i = 0;; i++) {
    if (i === 3) break;
    console.log(i);
}
```

```javascript
let i = 0;
for (;;) {
    if (i === 3) break;
    console.log(i);
    i++;
}
```

All produce 0, 1, 2 on separate lines. An omitted condition is always true; another termination mechanism is necessary unless indefinite repetition is intended.

### Variable step sizes

```javascript
for (let number = 1; number <= 128; number *= 2) {
    console.log(number);
}
// 1, 2, 4, 8, 16, 32, 64, 128 (separate lines)
```

`number *= 2` means `number = number * 2`. Useful for exponential growth and logarithmic algorithms.

| Update | Example sequence |
|---|---|
| `i++` | 0, 1, 2, 3, 4 |
| `i += 2` | 0, 2, 4, 6, 8 |
| `i--` | 5, 4, 3, 2, 1 |
| `i *= 2` | 1, 2, 4, 8, 16 |
| `i /= 2` | 32, 16, 8, 4, 2 |
| `i = i * i` | 2, 4, 16, 256 |

Choose starting values and termination carefully: doubling zero never changes it.

### The while loop

```javascript
while (condition) {
    // Instructions
}
```

A tank contains 100 litres; a machine consumes 15 per operation.

```javascript
let water = 100;
let operations = 0;
while (water >= 15) {
    water -= 15;
    operations++;
    console.log(`Operation ${operations}: ${water} litres remaining`);
}
console.log(`Total operations: ${operations}`);
```

```text
Operation 1: 85 litres remaining
Operation 2: 70 litres remaining
Operation 3: 55 litres remaining
Operation 4: 40 litres remaining
Operation 5: 25 litres remaining
Operation 6: 10 litres remaining
Total operations: 6
```

The remaining water sequence is 100 -> 85 -> 70 -> 55 -> 40 -> 25 -> 10. The number of repetitions need not be known beforehand.

```javascript
const cities = ["Tokyo", "Cairo", "Oslo"];
let i = 0;
while (i < cities.length) {
    console.log(cities[i]);
    i++;
}
```

Equivalent to `for (let i = 0; i < cities.length; i++) { console.log(cities[i]); }`. Both print Tokyo, Cairo, Oslo on separate lines.

#### Processing digits

```javascript
let number = 5832;
let sum = 0;
while (number > 0) {
    const digit = number % 10;
    sum += digit;
    number = Math.floor(number / 10);
}
console.log(sum); // 18
```

Remainder extracts the last decimal digit; floor division removes it for nonnegative integers. Assumes a nonnegative safe integer.

| Before | Digit | Sum | After |
|---:|---:|---:|---:|
| 5832 | 2 | 2 | 583 |
| 583 | 3 | 5 | 58 |
| 58 | 8 | 13 | 5 |
| 5 | 5 | 18 | 0 |

### The do...while loop

```javascript
let number = 10;
do {
    console.log(number);
    number++;
} while (number < 5);
// 10
```

```javascript
let number = 10;
while (number < 5) {
    console.log(number);
    number++;
}
// No output
```

`while` checks first; `do...while` executes first and checks afterward.

#### Pagination simulation

```javascript
function getPage(page) {
    return { page: page, totalPages: 3 };
}
let page = 1;
let response;
do {
    response = getPage(page);
    console.log(`Processing page ${response.page}`);
    page++;
} while (page <= response.totalPages);
```

```text
Processing page 1
Processing page 2
Processing page 3
```

The first operation is necessary before the continuation condition can be determined.

### The for...of loop

```javascript
const languages = ["JavaScript", "Python", "Rust"];
for (const language of languages) {
    console.log(language);
}
```

Output: JavaScript, Python, Rust on separate lines. `for (const value of iterable)` obtains values through the iterable protocol, which also supports strings, sets and maps.

```javascript
const planets = ["Mercury", "Venus", "Earth"];
for (const [index, planet] of planets.entries()) {
    console.log(index, planet);
}
```

```text
0 Mercury
1 Venus
2 Earth
```

`entries()` yields index-value pairs, destructured into `index` and `planet`.

```javascript
const numbers = [2, 4, 6];
for (let number of numbers) {
    number *= 10;
}
console.log(numbers); // [2, 4, 6]
for (let i = 0; i < numbers.length; i++) {
    numbers[i] *= 10;
}
console.log(numbers); // [20, 40, 60]
```

Reassigning a primitive loop variable does not modify the array. Indexed assignment does.

```javascript
const products = [
    { name: "Notebook", stock: 10 },
    { name: "Pencil", stock: 20 }
];
for (const product of products) {
    product.stock += 5;
}
console.log(products);
// [{ name: "Notebook", stock: 15 }, { name: "Pencil", stock: 25 }]
```

Objects are shared through reference values, so property mutation affects the original objects.

### The for...in loop

```javascript
const colors = ["Red", "Green", "Blue"];
for (const key in colors) {
    console.log(key, colors[key]);
}
// 0 Red\n1 Green\n2 Blue
for (const key in colors) {
    console.log(typeof key);
}
// string, string, string (separate lines)
colors.description = "Primary display colors";
for (const key in colors) {
    console.log(key, colors[key]);
}
```

```text
0 Red
1 Green
2 Blue
description Primary display colors
```

`for...in` enumerates enumerable string keys, including inherited enumerable properties. Prefer `for`, `for...of` or an array method for normal array traversal.

## 3. Controlling loop execution

### break

```javascript
const equipment = [101, 102, 104, 107, 110];
let found = false;
for (let i = 0; i < equipment.length; i++) {
    if (equipment[i] === 104) {
        found = true;
        break;
    }
}
console.log(found); // true
```

101 and 102 are checked, 104 is found, and 107 and 110 are not visited. `break` exits the nearest loop or switch unless a valid label specifies another target.

```javascript
const found = equipment.includes(104);
console.log(found); // true
```

The native method uses SameValueZero equality, unlike the strict equality in our loop. They differ for `NaN`.

### continue

```javascript
const readings = [24, null, 28, null, 31];
for (let i = 0; i < readings.length; i++) {
    if (readings[i] === null) {
        continue;
    }
    console.log(readings[i]);
}
// 24, 28, 31 (separate lines)
```

```javascript
readings.filter(reading => reading !== null)
    .forEach(reading => console.log(reading));
```

The loop filters and displays in one traversal without an intermediate array.

| Behavior | break | continue |
|---|---|---|
| Skip remaining body | Yes | Yes |
| Terminate loop | Yes | No |
| Next iteration | No | If condition permits |

Experiment:

```javascript
for (let i = 1; i <= 5; i++) {
    if (i === 3) {
        continue; // Replace with break and compare.
    }
    console.log(i);
}
```

With `continue`: 1, 2, 4, 5. With `break`: 1, 2.

#### A while loop mistake

```javascript
// WARNING: Infinite loop. Do not execute as written.
let i = 0;
while (i < 5) {
    if (i === 2) continue;
    console.log(i);
    i++;
}
```

The increment is skipped when i reaches two. Correct it:

```javascript
let i = 0;
while (i < 5) {
    if (i === 2) {
        i++;
        continue;
    }
    console.log(i);
    i++;
}
// 0, 1, 3, 4 (separate lines)
```

In a `for` loop, `continue` normally transfers control to the update clause before checking the condition again.

## 4. Fundamental loop problem-solving patterns

### Pattern 1: Traversal

Display laboratory sample identifiers.

```javascript
const samples = ["S101", "S102", "S103"];
for (const sample of samples) {
    console.log(`Inspecting ${sample}`);
}
// Array method alternative:
samples.forEach(sample => console.log(`Inspecting ${sample}`));
```

Each approach prints:

```text
Inspecting S101
Inspecting S102
Inspecting S103
```

### Pattern 2: Counting

```javascript
const components = ["good", "defective", "good", "defective", "defective"];
let count = 0;
for (const component of components) {
    if (component === "defective") count++;
}
console.log(count); // 3
```

| Component | Matches? | Count |
|---|---|---:|
| good | No | 0 |
| defective | Yes | 1 |
| good | No | 1 |
| defective | Yes | 2 |
| defective | Yes | 3 |

```javascript
const count = components.filter(component => component === "defective").length;
// Alternative (run separately):
const countWithReduce = components.reduce((total, component) => {
    return total + (component === "defective" ? 1 : 0);
}, 0);
```

There is no standard array `count()` method.

### Pattern 3: Accumulation

```javascript
const distances = [12, 18, 9, 25, 16];
let total = 0;
for (const distance of distances) {
    total += distance;
}
console.log(total); // 80
```

| Distance | Previous total | New total |
|---:|---:|---:|
| 12 | 0 | 12 |
| 18 | 12 | 30 |
| 9 | 30 | 39 |
| 25 | 39 | 64 |
| 16 | 64 | 80 |

```javascript
const total = distances.reduce((sum, distance) => sum + distance, 0);
```

Both carry a calculated result from one iteration to the next.

### Pattern 4: Collection

Collect books with at least 300 pages.

```javascript
const books = [
    { title: "Book A", pages: 180 },
    { title: "Book B", pages: 350 },
    { title: "Book C", pages: 240 },
    { title: "Book D", pages: 420 }
];
const selected = [];
for (const book of books) {
    if (book.pages >= 300) {
        selected[selected.length] = book;
    }
}
console.log(selected);
```

```javascript
// Output:
[
    { title: "Book B", pages: 350 },
    { title: "Book D", pages: 420 }
]
```

```javascript
const selected = books.filter(book => book.pages >= 300);
```

Create an empty result, visit each element, append matching elements, return the result. This is the basic filtering algorithm.

### Pattern 5: Transformation

```javascript
const metres = [1.5, 2, 3.2, 4];
const centimetres = [];
for (let i = 0; i < metres.length; i++) {
    centimetres[i] = metres[i] * 100;
}
console.log(centimetres); // [150, 200, 320, 400]
```

```javascript
const centimetres = metres.map(value => value * 100);
```

Create a result array, visit each element, transform it, store the transformed value. This is the basic map algorithm for dense arrays.

### Pattern 6: Tracking the best candidate

```javascript
const speeds = [18, 35, 22, 41, 29];
let highest = speeds[0];
for (let i = 1; i < speeds.length; i++) {
    if (speeds[i] > highest) highest = speeds[i];
}
console.log(highest); // 41
```

| Current | Previous highest | New highest |
|---:|---|---:|
| 18 | Initial | 18 |
| 35 | 18 | 35 |
| 22 | 35 | 35 |
| 41 | 35 | 41 |
| 29 | 41 | 41 |

Initializing to zero fails for all-negative arrays such as `[-15, -8, -20]`. Handle empty input explicitly.

```javascript
function maximum(numbers) {
    if (numbers.length === 0) return undefined;
    let highest = numbers[0];
    for (let i = 1; i < numbers.length; i++) {
        if (numbers[i] > highest) highest = numbers[i];
    }
    return highest;
}
console.log(maximum([-15, -8, -20])); // -8
console.log(maximum([])); // undefined
```

Assumes a dense array of valid numbers.

```javascript
const highest = speeds.reduce((best, current) => current > best ? current : best);
```

This reduction requires a nonempty dense array.

### Pattern 7: State tracking

```javascript
const measurements = [12, 15, 13, 18, 21, 17];
let increases = 0;
for (let i = 1; i < measurements.length; i++) {
    if (measurements[i] > measurements[i - 1]) increases++;
}
console.log(increases); // 3
```

| Previous | Current | Increased? | Count |
|---:|---:|---|---:|
| 12 | 15 | Yes | 1 |
| 15 | 13 | No | 1 |
| 13 | 18 | Yes | 2 |
| 18 | 21 | Yes | 3 |
| 21 | 17 | No | 3 |

`measurements[i - 1]` accesses the preceding element. Start at one because zero has no predecessor. Useful for trends, consecutive sequences and local peaks.

## 5. Combining multiple operations in one loop

Calculate total, average, highest, lowest, count above 30 and collect high readings.

```javascript
const readings = [18, 32, 25, 41, 12, 36];
const total = readings.reduce((sum, value) => sum + value, 0);
const average = total / readings.length;
const highest = Math.max(...readings);
const lowest = Math.min(...readings);
const highReadings = readings.filter(value => value > 30);
const highCount = highReadings.length;
```

A single traversal alternative (run separately):

```javascript
const readings = [18, 32, 25, 41, 12, 36];
let total = 0;
let highest = readings[0];
let lowest = readings[0];
let highCount = 0;
const highReadings = [];
for (const reading of readings) {
    total += reading;
    if (reading > highest) highest = reading;
    if (reading < lowest) lowest = reading;
    if (reading > 30) {
        highCount++;
        highReadings[highReadings.length] = reading;
    }
}
const average = total / readings.length;
console.log({ total, average, highest, lowest, highCount, highReadings });
```

```javascript
// Output:
{
    total: 164,
    average: 27.333333333333332,
    highest: 41,
    lowest: 12,
    highCount: 3,
    highReadings: [32, 41, 36]
}
```

| Reading | Total | Highest | Lowest | High count |
|---:|---:|---:|---:|---:|
| 18 | 18 | 18 | 18 | 0 |
| 32 | 50 | 32 | 18 | 1 |
| 25 | 75 | 32 | 18 | 1 |
| 41 | 116 | 41 | 18 | 2 |
| 12 | 128 | 41 | 12 | 2 |
| 36 | 164 | 41 | 12 | 3 |

Loop fusion combines compatible operations into one traversal. Both approaches are O(n) under ordinary assumptions. Actual performance depends on the engine, data and implementation. Define behavior for empty arrays and invalid measurements in production.

## 6. Choosing the appropriate loop

| Requirement | Suitable approach |
|---|---|
| Visit every value | `for...of` |
| Direct indexes | `for` |
| Backward traversal | `for` |
| Fixed strides | `for` |
| Multiple pointers | `for` or `while` |
| Changing condition | `while` |
| Execute at least once | `do...while` |
| Enumerate properties | `for...in` |
| Stop at match | `break` |
| Skip unwanted values | `continue` |
| Generate sequences | `for` or `while` |
| Several operations together | Plain loop |
| Transform | `map` or loop |
| Collect matches | `filter` or loop |
| Combined result | `reduce` or loop |

No loop is universally best. Choose based on control, readability and behavior.

## 7. Practice exercises

Use plain loops without array-processing methods. Append using indexed assignment.

### Level 1 - Basic iteration

- [ ] 1. Display integers 1 to 100.
- [ ] 2. Display integers 100 down to 1.
- [ ] 3. Generate even numbers between 1 and 50.
- [ ] 4. Generate multiples of 7 between 1 and 100.
- [ ] 5. Display every third element.
- [ ] 6. Traverse backward without modification.
- [ ] 7. Generate squares of integers 1 to 10.
- [ ] 8. Generate powers of three not exceeding 1,000.

### Level 2 - Searching and counting

- [ ] 9. Count negative numbers.
- [ ] 10. Count strings longer than five characters.
- [ ] 11. Determine whether an array contains a given number.
- [ ] 12. Find the index of the first negative number.
- [ ] 13. Find the index of the last positive number.
- [ ] 14. Count occurrences of a given value.
- [ ] 15. Find the first number divisible by both 3 and 5.
- [ ] 16. Determine whether all elements are positive.

### Level 3 - Transformation and accumulation

- [ ] 17. Create an array of squares.
- [ ] 18. Collect numbers between supplied boundaries.
- [ ] 19. Sum even numbers.
- [ ] 20. Average positive numbers.
- [ ] 21. Find minimum and maximum in one traversal.
- [ ] 22. Count vowels in a string.
- [ ] 23. Calculate the product of all elements.
- [ ] 24. Separate positive and negative numbers into arrays.

### Level 4 - Beyond basic array methods

- [ ] 25. Count increases compared with preceding values.
- [ ] 26. Generate Fibonacci numbers up to a limit.
- [ ] 27. Sum the digits of a positive integer.
- [ ] 28. Reverse the decimal digits of a positive integer.
- [ ] 29. Count divisions by two before a number becomes less than one.
- [ ] 30. Find the longest consecutive sequence of positive numbers.
- [ ] 31. Find adjacent pairs whose sum exceeds a limit.
- [ ] 32. Calculate running totals.

Running total: `[3, 5, 2, 8]` -> `[3, 8, 10, 18]`.

Longest positive sequence: `[-2, 4, 5, -1, 3, 7, 9, -4]` -> `3`.

## Coverage tracker

- [x] Iteration fundamentals
- [x] Forward and backward traversal
- [x] Strided and range-based iteration
- [x] Generating sequences
- [x] Multiple counters
- [x] Omitted loop clauses
- [x] Variable step sizes
- [x] while and do...while
- [x] for...of and for...in
- [x] break and continue
- [x] Counters and accumulators
- [x] Collection and transformation patterns
- [x] Candidate tracking
- [x] Neighboring-element analysis
- [x] Combining operations in one loop
- [ ] Loop scope, closures and labeled statements
- [ ] Callbacks and higher-order functions
- [ ] Complete individual array methods
- [ ] Specification-sensitive method behavior
- [ ] Advanced searching and filtering algorithms
- [ ] Sorting algorithms
- [ ] Sliding windows and two-pointer algorithms
- [ ] Advanced loop problem-solving
- [ ] Complete exercise solutions

Method equivalents in this part are previews. Full syntax, parameters, returns, callback behavior, sparse arrays, exceptions and manual implementations follow in later sections. Loop material is an original expansion beyond the attached [JavaScript.info Array Methods](https://javascript.info/array-methods) documentation. Examples are independent unless explicitly linked; run alternative snippets separately to avoid duplicate declarations.

---

[Previous: JavaScript Arrays](javascript-arrays.md) | Next: Part 2 - Loop scope, advanced loop control, functions, callbacks and higher-order functions, followed by push, pop, shift, unshift and splice (forthcoming).
